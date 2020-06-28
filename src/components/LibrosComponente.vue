<template>
  <div class='row lead my-1'>
    <div class='col-6'>{{libro.nombre}}</div>
    <div class='col-4'>{{libro.autor}}</div>
    <div class='col-2'>
      <div id='eliminar'  class='btn btn-primary'
           v-on:click='manejarClick($event,{indice:indice, id:id})'>Eliminar</div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'libros',
  props: ['libro','id','indice'],
  data: function () {
    return {
      test: 'test'
    }
  },
  methods: {
    manejarClick: function (evento,libroID) {
      if (evento.target.id==='eliminar'){
        this.$emit('eliminarLibro',libroID)
      }
    }
  }
}
</script>

<style>

</style>
