<template>
  <div class='row lead my-1'>
    <div class='col-6 text-align-left'>{{gasto.nombre}}</div>
    <div class='col-2'>{{gasto.monto}}</div>
    <div class='col-2'>{{gasto.tipo}}</div>
    <div class='col-2'>
      <div id='editar'  class='btn btn-primary fa fa-pencil-square m-1'
           v-on:click='manejarClick($event,{indice:indice, id:id})'></div>
      <div id='eliminar' class='btn btn-primary fa fa-trash m-1'
           v-on:click='manejarClick($event,{indice:indice, id:id})'></div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'gastos',
  props: ['gasto','id','indice'],
  data: function () {
    return {
      test: 'test'
    }
  },
  methods: {
    manejarClick: function (evento,gastoID) {
      if (evento.target.id==='editar'){
        this.$emit('editarGasto',gastoID)
      }
      if (evento.target.id==='eliminar'){
        this.$emit('eliminarGasto',gastoID)
      }
    }
  }
}
</script>

<style>

</style>
