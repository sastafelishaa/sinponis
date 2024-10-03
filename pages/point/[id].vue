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
    form.value.siswa = data.id;
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

// Mengurangi poin dari siswa
const reducePoints = async (siswaId, subJenisId) => {
  try {
    console.log(
      "Mengurangi poin untuk siswa:",
      siswaId,
      "dengan sub_jenis:",
      subJenisId
    ); // Log info siswa dan subJenis

    // Ambil jumlah poin dari sub_jenis_p yang dipilih
    const { data: subJenisData, error: subJenisError } = await supabase
      .from("sub_jenis_p")
      .select(`poin(id,jumlah_poin)`)
      .eq("id", subJenisId)
      .single();

    if (subJenisError || !subJenisData) {
      console.error("Error fetching points:", subJenisError);
      return;
    }

    const jumlahPoin = subJenisData.poin.jumlah_poin;
    console.log("Jumlah poin yang akan dikurangi:", jumlahPoin); // Log poin yang akan dikurangi

    // Ambil poin saat ini dari siswa
    const { data: siswaData, error: siswaError } = await supabase
      .from("siswa")
      .select("point")
      .eq("id", siswaId)
      .single();

    if (siswaError || !siswaData) {
      console.error("Error fetching student points:", siswaError);
      return;
    }

    console.log("Poin siswa sebelum dikurangi:", siswaData.point); // Log poin siswa sebelum pengurangan

    // Hitung poin yang baru setelah pengurangan
    const updatedPoints = siswaData.point - jumlahPoin;

    console.log("Poin siswa setelah dikurangi:", updatedPoints); // Log poin siswa setelah dikurangi

    // Perbarui poin siswa di database
    const { error: updateError } = await supabase
      .from("siswa")
      .update({ point: updatedPoints })
      .eq("id", siswaId);

    if (updateError) {
      console.error("Error updating siswa points:", updateError);
    } else {
      console.log("Points updated successfully.");
    }
  } catch (error) {
    console.error("Error in reducePoints:", error);
  }
};

// Mengirim form dan mengurangi poin siswa
const submitPoin = async () => {
  try {
    console.log("Submitting:", form.value); // Debug log

    // Kurangi poin siswa
    await reducePoints(form.value.siswa, form.value.sub_jenisp);

    // Masukkan data ke tabel 'form_point'
    const { error } = await supabase.from("form_point").insert([form.value]);

    if (error) {
      console.error("Error inserting form_point:", error);
    } else {
      navigateTo("/"); // Redirect setelah sukses
    }
  } catch (error) {
    console.error("Error in submitPoin:", error);
  }
};

// Mengurangi poin dari siswa
// const reducePoints = async (siswaId, subJenisId) => {
//   try {
//     // Ambil jumlah poin dari sub_jenis_p yang dipilih
//     const { data: subJenisData, error: subJenisError } = await supabase
//       .from("sub_jenis_p")
//       .select(`poin(id,jumlah_poin)`)
//       .eq("id", subJenisId)
//       .single();

//     if (subJenisError || !subJenisData) {
//       console.error("Error fetching points:", subJenisError);
//       return;
//     }

//     const jumlahPoin = subJenisData.poin.jumlah_poin;

//     // Ambil poin saat ini dari siswa
//     const { data: siswaData, error: siswaError } = await supabase
//       .from("siswa")
//       .select("point")
//       .eq("id", siswaId)
//       .single();

//     if (siswaError || !siswaData) {
//       console.error("Error fetching student points:", siswaError);
//       return;
//     }

//     // Hitung poin yang baru setelah pengurangan
//     const updatedPoints = siswaData.point - jumlahPoin;

//     // Perbarui poin siswa di database
//     const { error: updateError } = await supabase
//       .from("siswa")
//       .update({ point: updatedPoints })
//       .eq("id", siswaId);

//     if (updateError) {
//       console.error("Error updating siswa points:", updateError);
//     } else {
//       console.log("Points updated successfully.");
//     }
//   } catch (error) {
//     console.error("Error in reducePoints:", error);
//   }
// };

// // Mengirim form dan mengurangi poin siswa
// const submitPoin = async () => {
//   try {
//     console.log("Submitting:", form.value); // Debug log

//     // Kurangi poin siswa
//     await reducePoints(form.value.siswa, form.value.sub_jenisp);

//     // Masukkan data ke tabel 'form_point'
//     const { error } = await supabase.from("form_point").insert([form.value]);

//     if (error) {
//       console.error("Error inserting form_point:", error);
//     } else {
//       navigateTo("/"); // Redirect setelah sukses
//     }
//   } catch (error) {
//     console.error("Error in submitPoin:", error);
//   }
// };

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
