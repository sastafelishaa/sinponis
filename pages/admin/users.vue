<template>
  <div>
    <h3>Tambah Siswa</h3>
    <table class="table table-bordered mt-4">
      <thead>
        <tr>
          <th>No</th>
          <th>Nama</th>
          <th>Email</th>
          <th>Aksi</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="user in siswa" :key="user.id">
          <td>{{ user.id }}</td>
          <td>{{ user.nama }}</td>
          <td>{{ user.email }}</td>
          <td>
            <button class="btn btn-danger btn-sm" @click="deleteUser(user.id)">
              Hapus
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup>
definePageMeta({
  layout: "admin",
});

const supabase = useSupabaseClient();
const siswa = ref([]);

const getSiswa = async () => {
  const { data, error } = await supabase
    .from("siswa")
    .select(`*, kelas(*)`)
    .order("id", { ascending: true });
  if (data) {
    siswa.value = data;
  }
};

// Fungsi untuk mengambil data pengguna dari Supabase
// const fetchUsers = async () => {
//   const { data, error } = await supabase.from("siswa").select("*");

//   if (error) {
//     console.error("Error fetching users:", error);
//   } else {
//     siswa.value = data;
//   }
// };

// Fungsi untuk menghapus pengguna
const deleteUser = async (userId) => {
  const { error } = await supabase
    .from("siswa") // Ganti 'users' dengan nama tabel Anda
    .delete()
    .eq("id", userId);

  if (error) {
    console.error("Error deleting user:", error);
  } else {
    // Mengambil ulang data setelah penghapusan
    fgetSiswa();
  }
};

// Mengambil data saat komponen di-mount
onMounted(() => {
  getSiswa();
});
</script>
