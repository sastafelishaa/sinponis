<script setup>
// Set page title and meta description
useHead({
  title: "Sinponis",
  meta: [
    {
      name: "description",
      content: "Halaman Jenis Pelanggaran",
    },
  ],
});

// Initialize Supabase client
const supabase = useSupabaseClient();

// State variables
const loading = ref(true);
const groupedJenis = ref({});
const errorMsg = ref("");

// Function to sanitize IDs by replacing special characters
function sanitizeId(id) {
  return id.replace(/[^a-zA-Z0-9-_ ]/g, "-").replace(/\s+/g, "-"); // Replace spaces with dashes
}

// Fetch data about violations
const fetchViolationTypes = async () => {
  loading.value = true; // Set loading state to true

  // Query Supabase for data
  const { data, error } = await supabase
    .from("sub_jenis_p")
    .select(`*, jenis_p(*), poin(id,jumlah_poin)`)
    .order("id", { ascending: true });

  // Handle errors or process data
  if (error) {
    errorMsg.value = "Gagal memuat data. Silakan coba lagi."; // Set error message
    loading.value = false; // Set loading state to false
  } else if (data) {
    // Group data by violation type
    groupedJenis.value = data.reduce((acc, item) => {
      const violationType = item.jenis_p?.nama; // Get violation type name
      if (violationType) {
        if (!acc[violationType]) {
          acc[violationType] = {
            nama: violationType,
            items: [],
          };
        }
        acc[violationType].items.push(item); // Add item to the group
      }
      return acc;
    }, {});
    loading.value = false; // Set loading state to false after processing data
  }
};

// Call fetchViolationTypes when the component is mounted
onMounted(() => {
  fetchViolationTypes();
});
</script>

<template>
  <div class="container">
    <div class="row">
      <div class="col-lg-12">
        <h2 class="text-center my-4">JENIS PELANGGARAN</h2>

        <h3 v-if="loading" class="text-center text-muted">
          <em>Tunggu sebentar..</em>
        </h3>

        <h3 v-if="errorMsg" class="text-center text-danger">
          <em>{{ errorMsg }}</em>
        </h3>

        <div
          v-if="!loading && !errorMsg"
          class="accordion"
          id="accordionExample"
        >
          <div
            v-for="(group, index) in groupedJenis"
            :key="index"
            class="accordion-item"
          >
            <h2 class="accordion-header">
              <button
                class="accordion-button collapsed"
                type="button"
                data-bs-toggle="collapse"
                :data-bs-target="'#collapse-' + sanitizeId(group.nama)"
                aria-expanded="false"
                :aria-controls="'collapse-' + sanitizeId(group.nama)"
              >
                {{ group.nama }}
              </button>
            </h2>
            <div
              :id="'collapse-' + sanitizeId(group.nama)"
              class="accordion-collapse collapse"
              data-bs-parent="#accordionExample"
            >
              <div class="accordion-body">
                <ul>
                  <li v-for="item in group.items" :key="item.id">
                    <strong>Pelanggaran : </strong>{{ item.sub_jenisp }} <br />
                    <strong>Konsekwensi : </strong>{{ item.konsekw }} <br />
                    <strong>Poin : </strong>{{ item.poin.jumlah_poin }}
                  </li>
                </ul>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
