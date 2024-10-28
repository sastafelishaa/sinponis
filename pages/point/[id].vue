<script setup>
useHead({
  title: "Sinponis",
  meta: [
    {
      name: "description",
      content: "Halaman Pengurangan Poin",
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
  jumlah_poin: "",
});

const getSiswa = async () => {
  const siswaId = route.params.id;
  const { data, error } = await supabase
    .from("siswa")
    .select("id, nama, point")
    .eq("id", siswaId)
    .single();

  if (data) {
    form.value.siswa = data.id;
  }
};

const getPoint = async (event) => {
  let idpoint = event.target.value;
  form.value.sub_jenisp = parseInt(idpoint);

  const { data, error } = await supabase
    .from("sub_jenis_p")
    .select("poin(id, jumlah_poin)")
    .eq("id", idpoint)
    .single();

  if (data) {
    form.value.poin = data.poin.id;
    form.value.jumlah_poin = data.poin.jumlah_poin;
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

const ubahPoint = async () => {
  try {
    const { data: siswaData, error: siswaError } = await supabase
      .from("siswa")
      .select("id, point")
      .eq("id", form.value.siswa)
      .single();
    if (siswaError) {
      console.error("Error fetching siswa data:", siswaError);
      return;
    }

    let updatedPoin = siswaData.point - parseInt(form.value.jumlah_poin);
    if (updatedPoin < 0) {
      updatedPoin = 0;
    }
    const { error: updateError } = await supabase
      .from("siswa")
      .update({ point: updatedPoin })
      .eq("id", form.value.siswa);
    if (updateError) {
      console.error("Error updating points:", updateError);
    } else {
      console.log("Points updated successfully:", updatedPoin);
      alert("Poin berhasil dikurangi");
    }
  } catch (error) {
    console.error("Error reducing points:", error);
  }
};

const submitPoin = async () => {
  console.log("Form Data:", form.value);
  await ubahPoint();

  const dataToInsert = {
    siswa: form.value.siswa,
    jenis_p: form.value.jenis_p,
    sub_jenisp: form.value.sub_jenisp,
    poin: form.value.poin,
  };

  const { error } = await supabase.from("form_point").insert([dataToInsert]);

  if (error) {
    console.error("Error inserting data into form_point:", error); // Log error
    alert("An error occurred: " + error.message);
  } else {
    console.log("Data inserted successfully");
    navigateTo("/");
  }
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
        <h2 class="text-center my-4">KURANGI POIN</h2>
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
              v-model="form.jumlah_poin"
              class="form-control rounded-5"
              type="text"
              placeholder="Jumlah Point"
              readonly
            />
          </div>
          <button
            type="submit"
            class="btn btn-outline-primary rounded-5 px-3 text-center"
          >
            Submit
          </button>
        </form>
      </div>
    </div>
  </div>
</template>
