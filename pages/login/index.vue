<!-- <script setup>
useHead({
  title: "Sinponis",
  meta: [
    {
      name: "description",
      content: "Halaman Login",
    },
  ],
});

const supabase = useSupabaseClient();
const email = ref("");
const password = ref("");

const Login = async () => {
  const { data, error } = await supabase.auth.signInWithPassword({
    email: email.value,
    password: password.value,
  });
  if (data) {
    navigateTo("/");
  }
  if (error) throw error;
};

definePageMeta({
  layout: "login-page",
});
</script>

<template>
  <div class="container">
    <div class="my-5 row justify-content-center p-2">
      <div class="col-sm-4">
        <div class="card shadow p-3 container-fluid rounded-3">
          <div class="card-title">
            <h3 class="text-center">SINPONIS</h3>
          </div>
          <form @submit.prevent="Login">
            <input
              v-model="email"
              type="email"
              class="form-control form-control-lg rounded-2 border-dark"
              placeholder="E-mail"
            />
            <br />
            <input
              v-model="password"
              type="password"
              class="form-control form-control-lg rounded-2 border-dark"
              placeholder="Password"
            />
            <br />
            <button
              type="submit"
              class="btn btn-outline-primary rounded-2 px-3 text-center"
            >
              <h5>Login</h5>
            </button>
          </form>
        </div>
      </div>
    </div>
    <div class="row">
      <div class="col">
        <div class="info text-center">
          <h6>PEMERINTAHAN DAERAH PROVINSI JAWA BARAT</h6>
          <h6>DINAS PENDIDIKAN</h6>
          <h6>CABANG DINAS PENDIDIKAN WILAYAH XII</h6>
          <h6>SMKN 4 TASIKMALAYA</h6>
        </div>
      </div>
    </div>
  </div>
</template> -->

<script setup>
useHead({
  title: "Sinponis",
  meta: [{ name: "description", content: "Halaman Login" }],
});

const supabase = useSupabaseClient();
const router = useRouter();
const email = ref("");
const password = ref("");
const errorMessage = ref("");

const Login = async () => {
  errorMessage.value = ""; // Reset error message before attempt
  if (!email.value || !password.value) {
    errorMessage.value = "Email and password are required";
    return;
  }

  const { data, error } = await supabase.auth.signInWithPassword({
    email: email.value,
    password: password.value,
  });

  if (error) {
    errorMessage.value = error.message; // Show specific error
  } else if (data) {
    router.push("/");
  }
};

definePageMeta({
  layout: "login-page",
});
</script>

<template>
  <div class="container">
    <div class="my-5 row justify-content-center p-2">
      <div class="col-sm-4">
        <div class="card shadow p-3 container-fluid rounded-3">
          <div class="card-title">
            <h3 class="text-center">SINPONIS</h3>
          </div>
          <form @submit.prevent="Login">
            <input
              v-model="email"
              type="email"
              class="form-control form-control-lg rounded-2 border-dark"
              placeholder="E-mail"
              required
            />
            <br />
            <input
              v-model="password"
              type="password"
              class="form-control form-control-lg rounded-2 border-dark"
              placeholder="Password"
              required
            />
            <br />
            <button
              type="submit"
              class="btn btn-outline-primary rounded-2 px-3 text-center"
            >
              <h5>Login</h5>
            </button>
            <div v-if="errorMessage" class="text-danger mt-2">
              {{ errorMessage }}
            </div>
          </form>
        </div>
      </div>
    </div>
    <div class="row">
      <div class="col">
        <div class="info text-center">
          <h6>PEMERINTAHAN DAERAH PROVINSI JAWA BARAT</h6>
          <h6>DINAS PENDIDIKAN</h6>
          <h6>CABANG DINAS PENDIDIKAN WILAYAH XII</h6>
          <h6>SMKN 4 TASIKMALAYA</h6>
        </div>
      </div>
    </div>
  </div>
</template>
