<template>
  <div>
    <div class="row">
      <div class="col-md-4 col-sm-12"></div>
      <div class="col-md-4 col-sm-12 mt-5">
        <main class="form-signin w-100 m-auto">
          <form @submit.prevent="send">
            <h1 class="h3 mb-3 fw-normal">Formulario</h1>

            <div class="form-floating mt-1">
              <label for="floatingInput">Tipo de docuemento</label>
              <Dropdown v-model="empleado.tipoDocumentoId" :options="tipoDocumentoList" :placeholder="'Tipo de documento'"
                        option-label="nombre" option-value="id" class="w-100 p-inputtext-sm" @change="sendData"/>
            </div>

            <div class="form-floating mt-1">
              <label for="floatingInput">Documento</label>
              <InputText v-model="empleado.documento" placeholder="Número de documento" class="w-100 p-inputtext-sm"
                         @change="sendData"
              />
            </div>

            <div class="form-floating mt-1">
              <label for="floatingInput">Nombres</label>
              <InputText v-model="empleado.nombres" placeholder="Nombres" class="w-100 p-inputtext-sm"
                         @change="sendData"
              />
            </div>

            <div class="form-floating mt-1">
              <label for="floatingInput">Apellidos</label>
              <InputText v-model="empleado.apellidos" placeholder="Apellidos" class="w-100 p-inputtext-sm"
                         @change="sendData"
              />
            </div>

            <div class="form-floating mt-1">
              <label for="floatingInput">Áreas</label>
              <Dropdown v-model="empleado.areaId" :options="areaList" :placeholder="'Áreas'"
                        option-label="nombre" option-value="id" class="w-100 p-inputtext-sm" @change="loadSubarea"/>
            </div>

            <div class="form-floating mt-1">
              <label for="floatingInput">Subárea</label>
              <Dropdown v-model="empleado.subareaId" :options="subareaList" :placeholder="'Subáreas'"
                        option-label="nombre" option-value="id" class="w-100 p-inputtext-sm" :disabled="!areaLoaded"
                        @change="sendData"
              />
            </div>

            <button class="w-100 btn btn-lg btn-primary mt-5" type="submit" :disabled="!sendInfo">Guardar</button>
            <p class="mt-5 mb-3 text-muted">Carlos Ruiz 2022</p>
          </form>
        </main>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "formulario",
  data() {
    return {
      sendInfo: false,
      empleadoId: this.$route.query.Id,
      nuevo: true,
      empleado: {
        nombres: '',
        apellidos: '',
        documento: '',
        areaId: '',
        subareaId: '',
        tipoDocumentoId: ''
      },
      areaLoaded: false,
      tipoDocumentoList: [],
      areaList: [],
      subareaList: []
    }
  },
  async mounted() {
    if (this.empleadoId !== '0') {
      this.nuevo = false;
      const empleadoData = await this.$axios.$get('api/Empleado/' + this.empleadoId, {
        headers: {
          Authorization: 'Bearer ' + localStorage.getItem('token')
        }
      });
      if (empleadoData.statusCode === 200) {
        this.empleado = empleadoData.response;
      }
    }

    const tipoDocumentoData = await this.$axios.$get('cats/TipoDocumento');
    if (tipoDocumentoData.statusCode === 200) {
      this.tipoDocumentoList = tipoDocumentoData.response;
    }

    const areaData = await this.$axios.$get('cats/Area');
    if (areaData.statusCode === 200) {
      this.areaList = areaData.response;
    }

    await this.loadSubarea();
  },
  methods: {
    async send() {
      let guardar = null;

      if (this.nuevo) {
        guardar = await this.$axios.$post('api/Empleado', this.empleado, {
          headers: {
            Authorization: 'Bearer ' + localStorage.getItem('token')
          }
        });
      } else {
        guardar = await this.$axios.$put('api/Empleado/' + this.empleadoId, this.empleado, {
          headers: {
            Authorization: 'Bearer ' + localStorage.getItem('token')
          }
        });
      }

      if (guardar.statusCode === 200) {
        await this.$router.push('/empleados');
      }
    },
    sendData() {
      this.sendInfo = this.empleado.nombres !== '' && this.empleado.areaId !== '' &&
        this.empleado.subareaId !== '' && this.empleado.documento !== '' &&
        this.empleado.tipoDocumentoId !== '';
    },
    async loadSubarea() {
      this.sendData();
      this.areaLoaded = this.empleado.areaId !== '';
      if (this.areaLoaded) {
        const subareaData = await this.$axios.$get('cats/Subarea/Area/' + this.empleado.areaId);
        if (subareaData.statusCode === 200) {
          this.subareaList = subareaData.response;
        }
      }
    }
  }
}
</script>

<style scoped>

</style>
