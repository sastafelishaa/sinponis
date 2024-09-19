<template>
  <div>
    <Header />

    <slot />

    <div class="mym">
      <Footer />
    </div>
  </div>
</template>

<style scoped>
.mym {
  margin-top: 5rem;
}
</style>
