<script setup>
useHead({
  title: "Sinponis",
  meta: [
    {
      name: "description",
      content: "Halaman Awal",
    },
  ],
});

const supabase = useSupabaseClient();
const keyword = ref("");
const siswa = ref([]);
const found = ref(false);
const poin = ref([]);

const getSiswa = async () => {
  if (keyword.value.length > 0) {
    const { data, error } = await supabase
      .from("siswa")
      .select(`*, kelas(*)`)
      .ilike("nama", `%${keyword.value}%`);

    if (data.length > 0) {
      found.value = true;
      siswa.value = data;
    }
  } else {
    siswa.value = [];
    found.value = false;
  }
};

const getPoint = async () => {
  const { data, error } = await supabase
    .from("form_point")
    .select(`*, siswa(*), jenis_p(*), sub_jenisp(*), poin(*)`)
    .order("id", { ascending: false });
  if (data) poin.value = data;
};

onMounted(() => {
  getPoint();
});
</script>

<template>
  <div class="container">
    <div class="row">
      <div class="col">
        <h4 class="text-center text-black my-4">CARI SISWA</h4>
        <form @submit.prevent="getSiswa">
          <div class="input my-3">
            <input
              v-model="keyword"
              type="search"
              class="shadow-sm form-control rounded-5"
              placeholder="Cari Siswa"
            />
          </div>
        </form>
      </div>
    </div>
    <div class="row">
      <div v-for="(siswa1, i) in siswa" :key="i" class="card pt-4 mt-5">
        <div class="row align-items-start">
          <div class="col-sm-3">
            <img src="~assets/img/profil.webp" class="img-fluid" alt="profil" />
          </div>
          <div class="col-sm-9">
            <ul class="list-group list-group-flush">
              <li class="list-group-item">NIS : {{ siswa1.nis }}</li>
              <li class="list-group-item">Nama : {{ siswa1.nama }}</li>
              <li class="list-group-item">Kelas : {{ siswa1.kelas.kelas }}</li>
              <li class="list-group-item">Poin : {{ siswa1.point }}</li>
              <li class="list-group-item">
                <nuxt-link :to="`/point/${siswa1.id}`" class="btn btn-warning">
                  ➖
                </nuxt-link>
              </li>
            </ul>
          </div>
          <div>
            <h5 class="mt-3 text-center">Riwayat Pelanggaran</h5>
          </div>
          <div class="row">
            <div class="col">
              <!-- <div class="accordion" id="accordionExample">
                <div class="accordion-item">
                  <h2 class="accordion-header">
                    <button
                      class="accordion-button collapsed"
                      type="button"
                      data-bs-toggle="collapse"
                      data-bs-target="#collapse"
                      aria-expanded="false"
                      aria-controls="collapse"
                    >
                      Pelanggaran
                    </button>
                  </h2>
                  <div
                    id="collapse"
                    class="accordion-collapse collapse"
                    data-bs-parent="#accordionExample"
                  >
                    <div class="accordion-body">
                      <ul>
                        <li>Jenis Pelanggaran :</li>
                        <li>Konsekwensi :</li>
                        <li>Poin :</li>
                      </ul>
                    </div>
                  </div>
                </div>
              </div> -->
              <!-- <div class="table-responsive">
                <table class="table mt-3 table-bordered">
                  <thead class="table-secondary">
                    <tr>
                      <td>No</td>
                      <td>Jenis Pelanggaran</td>
                      <td>Konsekwensi</td>
                      <td>Point</td>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-for="(data, i) in poin" :key="i">
                      <td>{{ i + 1 }}</td>
                      <td>{{ data.jenis_p.nama }}</td>
                      <td>{{ data.sub_jenis_p.konsekw }}</td>
                      <td>{{ data.poin.jumlah_poin }}</td>
                    </tr>
                  </tbody>
                </table>
              </div> -->
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
