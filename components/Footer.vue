<template>
  <footer>
    <div class="container">
      <div>
        <nuxt-link to="/" type="submit" class="me-3">
          <i class="bi bi-house text-white fs-3"></i>
        </nuxt-link>
        <nuxt-link to="/jenispl" type="submit" class="me-3">
          <i class="bi bi-database text-white fs-3"></i>
        </nuxt-link>

        <nuxt-link to="/logout">
          <i class="bi bi-box-arrow-right text-white fs-3"></i>
        </nuxt-link>
      </div>
    </div>
  </footer>
</template>

<style scoped>
footer {
  background-color: #3a6e97 !important;
  padding: 1rem;
  text-align: center;
  position: fixed;
  bottom: 0;
  width: 100%;
  height: 4rem;
}
img {
  width: 5%;
}
svg {
  color: white !important;
  justify-content: center;
}
</style>
