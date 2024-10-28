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
const route = useRoute();
const loading = ref(true);
const dataPoint = ref([]);

const getData = async () => {
  loading.value = true;
  const { data, error } = await supabase
    .from("form_point")
    .select(
      `id, tanggal, siswa(id, nama), jenis_p(nama), sub_jenisp(*), poin(id,jumlah_poin)`
    )
    .eq("siswa", route.params.id)
    .order("id", { ascending: false });

  dataPoint.value = data || [];
  loading.value = false;
};

onMounted(() => {
  getData();
});
</script>

<template>
  <div class="container">
    <div class="row">
      <div class="col-lg-12">
        <h2 class="text-center my-4">RIWAYAT PELANGGARAN</h2>

        <h3 v-if="loading" class="text-center text-muted">
          <em>Tunggu sebentar..</em>
        </h3>

        <div
          class="accordion"
          id="accordionExample"
          v-if="dataPoint.length > 0"
        >
          <div v-for="(data, i) in dataPoint" :key="i" class="accordion-item">
            <h2 class="accordion-header">
              <button
                class="accordion-button collapsed"
                type="button"
                data-bs-toggle="collapse"
                :data-bs-target="'#collapse' + i"
                aria-expanded="false"
                :aria-controls="'collapse' + i"
              >
                {{ data.tanggal }}
              </button>
            </h2>
            <div
              :id="'collapse' + i"
              class="accordion-collapse collapse"
              data-bs-parent="#accordionExample"
            >
              <div class="accordion-body">
                <ul class="list-group list-group-flush">
                  <li class="list-group-item">
                    <span v-if="data.jenis_p">
                      Jenis Pelanggaran : {{ data.jenis_p.nama }}
                    </span>
                  </li>
                  <li class="list-group-item">
                    <span v-if="data.sub_jenisp">
                      Pelanggaran : {{ data.sub_jenisp.sub_jenisp }}
                    </span>
                  </li>
                  <li class="list-group-item">
                    <span v-if="data.sub_jenisp">
                      Konsekwensi : {{ data.sub_jenisp.konsekw }}
                    </span>
                  </li>
                  <li class="list-group-item">
                    <span v-if="data.poin">
                      Poin : {{ data.poin.jumlah_poin }}
                    </span>
                  </li>
                </ul>
              </div>
            </div>
          </div>
        </div>
        <div v-else>
          <p class="text-center text-muted">Data tidak ditemukan</p>
        </div>
      </div>
    </div>
  </div>
</template>
