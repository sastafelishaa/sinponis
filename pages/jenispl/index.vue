<script setup>
useHead({
  title: "Sinponis",
  meta: [
    {
      name: "description",
      content: "Halaman Jenis Pelanggaran",
    },
  ],
});

const supabase = useSupabaseClient();

const loading = ref(true);
const groupedJenis = ref({});
const errorMsg = ref("");

// Fungsi untuk membuat id aman dengan mengganti karakter khusus
function sanitizeId(id) {
  return id.replace(/[^a-zA-Z0-9-_]/g, "-");
}

const jenisPelanggaran = async () => {
  loading.value = true;
  const { data, error } = await supabase
    .from("sub_jenis_p")
    .select(`*, jenis_p(*), poin(id,jumlah_poin)`)
    .order("id", { ascending: true });

  if (error) {
    errorMsg.value = "Gagal memuat data. Silakan coba lagi.";
    loading.value = false;
  } else if (data) {
    // Mengelompokkan data berdasarkan jenis_p.nama dan mengabaikan data yang tidak lengkap
    groupedJenis.value = data.reduce((acc, item) => {
      const namaJenis = item.jenis_p?.nama;
      if (namaJenis) {
        if (!acc[namaJenis]) {
          acc[namaJenis] = {
            nama: namaJenis,
            items: [],
          };
        }
        acc[namaJenis].items.push(item);
      }
      return acc;
    }, {});
    loading.value = false;
  }
};

onMounted(() => {
  jenisPelanggaran();
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
            v-for="(group, i) in groupedJenis"
            :key="i"
            class="accordion-item"
          >
            <h2 class="accordion-header">
              <button
                class="accordion-button collapsed"
                type="button"
                data-bs-toggle="collapse"
                :data-bs-target="'#collapse' + sanitizeId(group.nama)"
                aria-expanded="false"
                :aria-controls="'collapse' + sanitizeId(group.nama)"
              >
                {{ group.nama }}
              </button>
            </h2>
            <div
              :id="'collapse' + sanitizeId(group.nama)"
              class="accordion-collapse collapse"
              data-bs-parent="#accordionExample"
            >
              <div class="accordion-body">
                <ul>
                  <li v-for="item in group.items" :key="item.id">
                    <strong>Pelanggaran : </strong>
                    {{ item.sub_jenisp }} <br />
                    <strong>Konsekwensi : </strong>
                    {{ item.konsekw }} <br />
                    <strong>Poin : </strong>
                    {{ item.poin.jumlah_poin }}
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
