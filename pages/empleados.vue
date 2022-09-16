<template>
  <div>
    <div class="mt-5 p-5">
      <div class="">
        <NuxtLink :to="{ path: 'formulario', query: { Id: '0' } }" class="btn btn-primary mb-2">Nuevo</NuxtLink>
      </div>
      <DataTable :value="empleados" :paginator="true" :rows="10"
                 paginatorTemplate="FirstPageLink PrevPageLink PageLinks NextPageLink LastPageLink CurrentPageReport RowsPerPageDropdown"
                 :rowsPerPageOptions="[10,25,50]" :filters.sync="filters" filterDisplay="menu"
                 currentPageReportTemplate="Mostrando {first} a {last} de {totalRecords} registros"
                 :globalFilterFields="['nombres']"
      >
        <template #header>
          <div class="flex justify-content-between align-items-center">
            <h5 class="m-0">Empleados</h5>
            <span class="p-input-icon-left">
                <i class="pi pi-search" />
                <InputText v-model="filters['global'].value" placeholder="Keyword Search" />
            </span>
          </div>
        </template>
        <Column field="nombres" header="Nombres"></Column>
        <Column field="apellidos" header="Apellidos"></Column>
        <Column field="documento" header="Documento"></Column>
        <Column header="Acciones">
          <template #body="{data}">
            <NuxtLink :to="{ path: 'formulario', query: { Id: data.id } }" class="btn btn-link">Editar</NuxtLink> &nbsp;
            <button class="btn-link btn" @click="remove(data.id)">Eliminar</button>
          </template>
        </Column>
      </DataTable>
    </div>
  </div>
</template>

<script>
import {FilterMatchMode,FilterOperator} from 'primevue/api';

export default {
  name: "empleados",
  data() {
    return {
      empleados: null,
      filters: {
        'global': {value: null, matchMode: FilterMatchMode.CONTAINS},
      }
    }
  },
  async mounted() {
    await this.loadData();
  },
  methods: {
    async remove(id) {
      const eliminarData = await this.$axios.$delete('api/empleado/' + id, {
        headers: {
          Authorization: 'Bearer ' + localStorage.getItem('token')
        }
      });

      if (eliminarData.statusCode === 200) {
        await this.loadData();
      }
    },
    async loadData() {
      const data = await this.$axios.$get('api/empleado', {
        headers: {
          Authorization: 'Bearer ' + localStorage.getItem('token')
        }
      });

      if (data.statusCode === 200) {
        this.empleados = data.response;
      }
    }
  }
}
</script>

<style scoped>

</style>
