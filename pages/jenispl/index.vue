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

const jenis = ref([]);

const jenisPelanggaran = async () => {
  const { data, error } = await supabase
    .from("sub_jenis_p")
    .select(`*, jenis_p(*)`)
    .order("id", { ascending: true });
  if (data) jenis.value = data;
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
                  <li>Poin : {{ jenisp.poin }}</li>
                </ul>
              </div>
            </div>
          </div>
        </div>

        <!-- <div class="table-responsive">
          <table class="table table-bordered">
            <thead class="table-secondary">
              <tr class="text-center">
                <td>No</td>
                <td>Jenis Pelanggaran</td>
                <td>Sub Jenis Pelanggaran</td>
                <td>Konsekwensi</td>
                <td>Point</td>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(jenisp, i) in jenis" :key="i">
                <td>{{ i + 1 }}</td>
                <td>{{ jenisp.jenis_p.jenisp }}</td>
                <td>{{ jenisp.sub_jenisp }}</td>
                <td>{{ jenisp.konsekw }}</td>
                <td>{{ jenisp.poin }}</td>
              </tr>
            </tbody>
          </table>
        </div> -->
      </div>
    </div>
  </div>
</template>
