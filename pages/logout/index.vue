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
  <!-- <div>
    <h1 class="mt-5 text-center">tunggu sebentar.....</h1>
  </div> -->
</template>
