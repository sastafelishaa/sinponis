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
                {{ jenisp.jenisp }}
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

<script setup>
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
    .select("id")
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
  //form.value.poin = idpoint;
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
    .select(`*, jenis_p(id, jenisp)`)
    .eq("jenis_p", id)
    .order("id", { ascending: true });
  if (data) sub_jenis.value = data;
};

// async function deductPoints(siswaId, pointId) {
//   try {
//     // Get the jumlah_poin to deduct
//     const { data: poinData, error: poinError } = await supabase
//       .from("poin")
//       .select("jumlah_poin")
//       .eq("id", pointId)
//       .single();

//     if (poinError) {
//       throw new Error(`Error fetching points: ${poinError.message}`);
//     }

//     const jumlahPoin = poinData.jumlah_poin;

//     // Update the siswa's points by subtracting jumlah_poin
//     const { data: siswaData, error: updateError } = await supabase
//       .from("siswa")
//       .select("point") // Get the current points
//       .eq("id", siswaId)
//       .single();

//     if (updateError) {
//       throw new Error(`Error fetching student points: ${updateError.message}`);
//     }

//     const currentPoints = siswaData.point;

//     // Calculate the new points
//     const newPoints = currentPoints - jumlahPoin;

//     // Update the siswa table with the new points
//     const { error: finalUpdateError } = await supabase
//       .from("siswa")
//       .update({ poin: newPoints })
//       .eq("id", siswaId);

//     if (finalUpdateError) {
//       throw new Error(
//         `Error updating student points: ${finalUpdateError.message}`
//       );
//     }

//     console.log("Student points updated successfully.");
//   } catch (error) {
//     console.error(error.message);
//   }
// }

// async function submitPoin() {
//   const { data, error } = await supabase
//     .from("form_point")
//     .insert([form.value]);

//   if (error) {
//     console.error("Error inserting point:", error);
//     return;
//   }

//   // Deduct points from the student
//   await deductPoints(form.value.siswa, form.value.sub_jenisp);
// }

async function submitPoin() {
  console.log(form.value);
  const { data, error } = await supabase
    .from("form_point")
    .insert([form.value]);
  if (data) console.log("siswa berhasil di POIN!!!!");
}

// async function point() {
//   const { data, error } = await supabase
//     .from("form_point")
//     .select([form.value]);

//   if (error) {
//     console.error("Error inserting point : ", error);
//     return;
//   }

//   const { data: siswaData, error: siswaError } = await supabase
//     .from("siswa")
//     .update({ poin: supabase.raw(`point - ${form.value.poin.jumlah_poin}`) })
//     .eq("id", form.value.siswa);

//   if (siswaError) {
//     console.error("Error updating siswa points:", siswaError);
//     return;
//   }

//   console.log("siswa berhasil di POIN!!!!");
// }

onMounted(() => {
  getSiswa();
  getJenis();
});
</script>

<!-- async function submitPoin() {
  const { data, error } = await supabase
    .from("form_point")
    .insert([form.value]);

  if (error) {
    console.error("Error inserting point:", error);
    return;
  }

  // Deduct points from the siswa after successful insertion
  const { data: siswaData, error: siswaError } = await supabase
    .from("siswa")
    .update({ poin: supabase.raw(`poin - ${form.value.poin}`) })
    .eq("id", form.value.siswa);

  if (siswaError) {
    console.error("Error updating siswa points:", siswaError);
    return;
  }

  console.log("siswa berhasil di POIN!!!!");
}
 -->
