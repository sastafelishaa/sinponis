<script setup>
useHead({
  title: "Sinponis",
  meta: [
    {
      name: "description",
      content: "Halaman Tambah Poin",
    },
  ],
});

const supabase = useSupabaseClient();

const route = useRoute();

const jenis = ref([]);

const sub_jenis = ref([]);

const form = ref({
  siswa: "",
  jenis_p: "",
  sub_jenisp: "",
  poin: "",
});

const getSiswa = async () => {
  const siswaId = route.params.id;
  const { data, error } = await supabase
    .from("siswa")
    .select("id, nama")
    .eq("id", siswaId)
    .single();

  if (data) {
    form.value.siswa = data.nama;
  }
};

const getPoint = async (event) => {
  let idpoint = event.target.value;
  form.value.sub_jenisp = parseInt(idpoint);
  const { data, error } = await supabase
    .from("sub_jenis_p")
    .select(`poin(id,jumlah_poin)`)
    .eq("id", idpoint)
    .single();
  if (data) {
    form.value.poin = data.poin.jumlah_poin;
  }
};

const getJenis = async () => {
  const { data, error } = await supabase
    .from("jenis_p")
    .select("*")
    .order("id", { ascending: true });
  if (data) jenis.value = data;
};

const getPelanggaran = async (event) => {
  let id = event.target.value;
  form.value.jenis_p = parseInt(id);
  const { data, error } = await supabase
    .from("sub_jenis_p")
    .select(`*, jenis_p(id, nama)`)
    .eq("jenis_p", id)
    .order("id", { ascending: true });
  if (data) sub_jenis.value = data;
};



onMounted(() => {
  getSiswa();
  getJenis();
});
</script>

<template>
  <div class="container-fluid">
    <div class="row">
      <div class="col-lg-12">
        <h2 class="text-center my-4">Tambah Point</h2>
        <form @submit.prevent="submitPoin">
          <div class="mb-3">
            <input
              v-model="form.siswa"
              class="form-control rounded-5"
              type="text"
              placeholder="Nama"
              readonly
            />
          </div>

          <div class="mb-3">
            <select
              @change="getPelanggaran"
              class="form-control form-control-lg form-select rounded-5"
            >
              <option value="">Jenis</option>
              <option v-for="(jenisp, i) in jenis" :key="i" :value="jenisp.id">
                {{ jenisp.nama }}
              </option>
            </select>
          </div>

          <div class="mb-3">
            <select
              @change="getPoint"
              class="form-control form-control-lg form-select rounded-5"
            >
              <option value="">Pelanggaran</option>
              <option v-for="(sub, i) in sub_jenis" :key="i" :value="sub.id">
                {{ sub.sub_jenisp }}
              </option>
            </select>
          </div>
          <div class="mb-3">
            <input
              v-model="form.poin"
              class="form-control rounded-5"
              type="text"
              placeholder="Jumlah Point"
              readonly
            />
          </div>

          <button
            type="submit"
            class="btn btn-primary text-white rounded-5 px-4 text-center"
          >
            Tambah
          </button>
        </form>
      </div>
    </div>
  </div>
</template>
