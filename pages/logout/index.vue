<script setup>
useHead({
  title: "Sinponis",
  meta: [
    {
      name: "description",
      content: "Halaman Logout",
    },
  ],
});

const supabase = useSupabaseClient();

async function logout() {
  const { error } = await supabase.auth.signOut();
  if (!error) navigateTo("/login");
}

onMounted(() => {
  logout();
});
</script>

<template>
  <h3 class="text-center text-muted mt-3">
    <em>Tunggu sebentar..</em>
  </h3>
</template>
