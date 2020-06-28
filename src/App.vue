<template>
  <div v-if="logon" id="app" class="container">
    <div class="row text-primary">
      <div class="col-4"></div>
      <div class="col-4">
        <h1>Lista de gastos</h1>
      </div>
      <div class="col-3"></div>
      <div class="col-1">
        <span id="ocultar" v-on:click="manejarClick($event)" v-bind:class="[btnpre, btnhide]"></span>
      </div>
    </div>
    <div v-show="show" class="row border rounded text-primary lead p-2 mt-3">
      <div class="col-3"></div>
      <div class="col-3">Nombre del gasto</div>
      <div class="col-3">
        <input v-model="nombreGasto" class="text-primary" type="text" />
      </div>
      <div class="col-3"></div>

      <div class="col-3"></div>
      <div class="col-3">Monto del gasto</div>
      <div class="col-3">
        <input v-model="montoGasto" class="text-primary" type="text" />
      </div>
      <div class="col-3"></div>

      <div class="col-3"></div>
      <div class="col-3">Tipo de gasto</div>
      <div class="col-3">
        <input v-model="tipoGasto" class="text-primary" type="text" />
      </div>
      <div class="col-3"></div>

      <div class="col-12 p-2">
        <div
          id="agregar"
          v-bind:class="[btnpre, btnhideadd]"
          v-on:click="manejarClick($event)"
        >Agregar gasto</div>
        <div
          id="actualizar"
          v-bind:class="[btnpre, btnhideupd]"
          v-on:click="manejarClick($event)"
        >Actualizar gasto</div>
      </div>
    </div>
    <div class="container mt-2">
      <div
        id="filtroHogar"
        v-bind:class="['btn', btnselected]"
        v-on:click="manejarClick($event)"
      >Hogar</div>
      <div
        id="filtroSalud"
        v-bind:class="['btn', btnselected]"
        v-on:click="manejarClick($event)"
      >Salud</div>
      <div
        id="filtroTrabajo"
        v-bind:class="['btn', btnselected]"
        v-on:click="manejarClick($event)"
      >Trabajo</div>
    </div>
    <div class="container border rounded text-primary mt-4">
      <div class="row lead border rounded font-weight-bold">
        <div class="col-6">Nombre del gasto</div>
        <div class="col-2">Monto del gasto</div>
        <div class="col-2">Tipo de gasto</div>
        <div class="col-2">Acción</div>
      </div>
      <gastosComponente
        v-for="(gasto,index) in gastos"
        v-bind:gasto="gasto"
        v-bind:id="gasto.id"
        v-bind:indice="index"
        v-bind:key="index"
        v-on:editarGasto="editar($event)"
        v-on:eliminarGasto="eliminar($event)"
      ></gastosComponente>
      <div class="row lead border rounded font-weight-bold">
        <div class="col-6">Total</div>
        <div class="col-6">{{ gastosTotales }}</div>
      </div>
    </div>
  </div>
  <div v-else>
    <loginForm v-bind:firebase="firebase" v-on:ingresoCorrecto="ingresoCorrecto($event)"></loginForm>
  </div>
</template>

<script>
import firebase from "firebase";
import "firebase/firestore";
import gastosComponente from "./components/GastosComponente.vue";
import loginForm from "./components/loginForm.vue";

export default {
  name: "app",
  data: function() {
    return {
      gastos: [],
      gastoeditar: [],
      nombreGasto: "",
      montoGasto: "",
      tipoGasto: "",
      coleccion: {},
      logon: false,
      show: true,
      btnhide: "fa fa-minus",
      btnpre: "btn btn-primary",
      btnselected: "btn-outline-primary",
      btnhideadd: "",
      btnhideupd: "d-none",
      firebase: "",
      idUsuario: "",
      db: "",
      idedit: ""
    };
  },
  computed: {
    gastosTotales: function() {
      return this.gastos.reduce((total, item) => {
        return total + Number(item.monto);
      }, 0);
    }
  },
  methods: {
    manejarClick: function(evento) {
      if (evento.target.id === "agregar") {
        const gastoData = {
          nombre: this.nombreGasto,
          monto: this.montoGasto,
          tipo: this.tipoGasto
        };
        this.coleccion
          .add(gastoData)
          .then(docReference => {
            this.gastos.unshift({
              id: docReference.id,
              nombre: gastoData.nombre,
              monto: gastoData.monto,
              tipo: gastoData.tipo
            });
          })
          .catch(Errro => {
            alert(
              "No se pudo agregar el libro al sistema. Error: " + Errro.message
            );
          });
        this.nombreGasto = "";
        this.montoGasto = "";
        this.tipoGasto = "";
      }
      if (evento.target.id === "ocultar") {
        if (this.btnhide == "fa fa-minus") {
          this.btnhide = "fa fa-plus";
        } else {
          this.btnhide = "fa fa-minus";
        }
        this.show = !this.show;
      }
      if (evento.target.id === "actualizar") {
        console.log("actualizando item " + this.idedit);
        this.btnhideupd = "d-none";
        this.btnhideadd = "";

        var docupdate = this.db.collection("/gastos/").doc(this.idedit);

        docupdate
          .update({
            nombre: this.nombreGasto,
            monto: this.montoGasto,
            tipo: this.tipoGasto
          })
          .then(function() {
            console.log("gasto actualizado!");
          })
          .catch(function(error) {
            console.error("Error actualizando documento: ", error);
          });
        
        this.gastos.splice();
        this.coleccion = this.db.collection("/gastos/");
        this.coleccion.get().then(gastos => {
          gastos.forEach(gasto => {
            this.gastos.push({
              id: gasto.id,
              nombre: gasto.data().nombre,
              monto: gasto.data().monto,
              tipo: gasto.data().tipo
            });
          });
        });

        this.nombreGasto = "";
        this.montoGasto = "";
        this.tipoGasto = "";
      }
      if (evento.target.id === "filtroHogar"){
        console.log("ingresando filtro Hogar");
      }
      if (evento.target.id === "filtroSalud"){
        console.log("ingresando filtro Salud");
      }
      if (evento.target.id === "filtroTrabajo"){
        console.log("ingresando filtro Trabajo");
      }
    },
    editar: function(gastoID) {
      this.gastoeditar = this.db.collection("/gastos/");
      this.gastoeditar.get().then(gastos => {
        gastos.forEach(gasto => {
          if (gastoID.id == gasto.id) {
            this.idedit = gasto.id;
            this.nombreGasto = gasto.data().nombre;
            this.montoGasto = gasto.data().monto;
            this.tipoGasto = gasto.data().tipo;
          }
        });
      });
      this.btnhideadd = "d-none";
      this.btnhideupd = "";
    },
    eliminar: function(gastoID) {
      this.coleccion.doc(gastoID.id).delete();
      this.gastos.splice(gastoID.indice, 1);
      this.btnhideupd = "d-none";
      this.btnhideadd = "";
    },
    ingresoCorrecto: function(usuario) {
      console.log("User: " + usuario);
      this.idUsuario = usuario;
      this.logon = true;
      this.coleccion = this.db.collection("/gastos/");
      this.coleccion.get().then(gastos => {
        gastos.forEach(gasto => {
          this.gastos.push({
            id: gasto.id,
            nombre: gasto.data().nombre,
            monto: gasto.data().monto,
            tipo: gasto.data().tipo
          });
        });
      });
    }
  },
  components: {
    gastosComponente,
    loginForm
  },
  beforeMount: function() {
    var config = {
      apiKey: "YOUR_API_KEY",
      authDomain: "XXXXXX.firebaseapp.com",
      databaseURL: "https://XXXXXX.firebaseio.com",
      projectId: "XXXX",
      storageBucket: "xxx",
      messagingSenderId: "YOUR_messagingSenderId",
      appId: "APP_ID",
      measurementId: "G-XXXXXX"
    };
    firebase.initializeApp(config);
    this.db = firebase.firestore();
    this.firebase = firebase;
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
