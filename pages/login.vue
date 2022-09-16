<template>
  <div>
    <div class="row">
      <div class="col-md-4 col-sm-12"></div>
      <div class="col-md-4 col-sm-12 mt-5">
        <main class="form-signin w-100 m-auto">
          <form @submit.prevent="send">
            <h1 class="h3 mb-3 fw-normal">Inicio de sesión</h1>

            <div class="form-floating">
              <label for="floatingInput">Usuario</label>
              <input type="text" class="form-control"
                     id="floatingInput" placeholder="user.name" v-model="username" @keyup="setData">
            </div>
            <div class="form-floating">
              <label for="floatingPassword">Contraseña</label>
              <input type="password" class="form-control"
                     id="floatingPassword" placeholder="Contraseña" v-model="password" @keyup="setData">
            </div>

            <button class="w-100 btn btn-lg btn-primary mt-5" type="submit" :disabled="!senfInfo">Iniciar Sesión</button>
            <p class="mt-5 mb-3 text-muted">Carlos Ruiz 2022</p>
          </form>
        </main>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "login",
  data() {
    return {
      username: '',
      password: '',
      senfInfo: false
    }
  },
  methods: {
    setData() {
      this.senfInfo = this.password !== '' && this.username !== '';
    },
    async send() {
      const data = await this.$axios.$post('api/auth/login', {
        username: this.username,
        password: this.password
      });

      if (data.statusCode === 200) {
        this.$router.push('/empleados');
        localStorage.setItem('token', data.response.token);
      }
    }
  }
}
</script>

<style scoped>

</style>
