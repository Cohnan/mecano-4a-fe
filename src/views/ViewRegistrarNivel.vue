<template>
  <div class="view-registrar-nivel">
    <h1>Registrar Nivel</h1>

    <form v-on:submit.prevent="procesar">
      <input type="text" placeholder="# Nivel" v-model="nuevoNivel.id" />
      <input type="text" placeholder="Nombre" v-model="nuevoNivel.nombre" />
      <input
        type="text"
        placeholder="Descripcion"
        v-model="nuevoNivel.descripcion"
      />
      <input type="file" v-on:change="codificarImagenComoURL" />
      <button id="boton-submit-nivel" type="submit" disabled>
        Registrar Nivel
      </button>
    </form>

    <h2>Mostrar imagen subida</h2>

    <img :src="imagenCodificada" :key="imagenSubida" />
  </div>
</template>

<script>
import gql from "graphql-tag";

export default {
  name: "ViewRegistrarNivel",
  data: function () {
    return {
      nuevoNivel: {
        id: "",
        nombre: "",
        descripcion: "",
        imagen: "",
      },

      imagenSubida: false,
      imagenCodificada: "",
    };
  },
  methods: {
    codificarImagenComoURL: async function (evento) {
      document.getElementById("boton-submit-nivel").disabled = true;
      this.imagenSubida = false;

      var archivo = evento.target.files[0];

      let imagenComoUrl = await this.leerArchivoAsync(archivo);

      document.getElementById("boton-submit-nivel").disabled = false;
      this.imagenSubida = true;
      console.log(imagenComoUrl)
      this.imagenCodificada = imagenComoUrl;
      this.nuevoNivel.imagen = imagenComoUrl;
      this.nuevoNivel.id = parseInt(this.nuevoNivel.id)
    },

    leerArchivoAsync: function (file) {
      return new Promise((resolve, reject) => {
        let reader = new FileReader();

        reader.onload = () => {
          resolve(reader.result);
        };

        reader.onerror = reject;

        reader.readAsDataURL(file);
      });
    },

    procesar: async function () {
        // console.log("Web tratara de registrar el siguiente nivel", this.nuevoNivel)
      await this.$apollo.mutate({
        mutation: gql`
          mutation RegistrarNivel($nivel: NivelIn!) {
            registrarNivel(nivel: $nivel) {
              id
              nombre
              descripcion
              imagen
            }
          }
        `,
        variables: {
          nivel: this.nuevoNivel,
        },
      }).then(respuesta => {
        console.log(`respuesta ${respuesta}`);
        alert("Registro de nivel exitoso")
        }).catch(error => {console.log(`error ${error}`)});
    },
  },
};
</script>