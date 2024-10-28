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
</script>

<template>
  <div class="container">
    <div class="row">
      <div class="col">
        <h4 class="text-center text-black my-4">CARI SISWA</h4>
        <form @submit.prevent="getSiswa">
          <div class="input">
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
      <div v-for="(siswa1, i) in siswa" :key="i" class="card pt-4 mt-3">
        <div class="row align-items-start">
          <div class="col-sm-3">
            <img
              src="~assets/img/profil.webp"
              class="img-fluid mb-3"
              alt="profil"
            />
          </div>
          <div class="col-sm-9">
            <ul class="list-group list-group-flush">
              <li class="list-group-item">NIS : {{ siswa1.nis }}</li>
              <li class="list-group-item">Nama : {{ siswa1.nama }}</li>
              <li class="list-group-item">Kelas : {{ siswa1.kelas.kelas }}</li>
              <li class="list-group-item">Poin : {{ siswa1.point }}</li>
              <li class="list-group-item">
                <nuxt-link
                  :to="`/point/${siswa1.id}`"
                  class="btn btn-warning me-3"
                >
                  Kurangi Poin
                </nuxt-link>
                <nuxt-link
                  :to="`/riwayat/${siswa1.id}`"
                  class="btn btn-warning"
                >
                  Riwayat Pelanggaran
                </nuxt-link>
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
