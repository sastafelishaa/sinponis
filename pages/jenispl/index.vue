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
const jenis = ref([]);

const jenisPelanggaran = async () => {
  loading.value = true;
  const { data, error } = await supabase
    .from("sub_jenis_p")
    .select(`*, jenis_p(*), poin(id,jumlah_poin)`)
    .order("id", { ascending: true });
  if (data) {
    jenis.value = data;
    // console.log(data);
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

        <div class="accordion" id="accordionExample">
          <div v-for="(jenisp, i) in jenis" :key="i" class="accordion-item">
            <h2 class="accordion-header">
              <button
                class="accordion-button collapsed"
                type="button"
                data-bs-toggle="collapse"
                :data-bs-target="'#collapse' + jenisp.id"
                aria-expanded="false"
                :aria-controls="'collapse' + jenisp.id"
              >
                {{ jenisp.jenis_p.nama }}
              </button>
            </h2>
            <div
              :id="'collapse' + jenisp.id"
              class="accordion-collapse collapse"
              data-bs-parent="#accordionExample"
            >
              <div class="accordion-body">
                <ul>
                  <li>Jenis Pelanggaran : {{ jenisp.sub_jenisp }}</li>
                  <li>Konsekwensi : {{ jenisp.konsekw }}</li>
                  <li>Poin : {{ jenisp.poin.jumlah_poin }}</li>
                </ul>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
