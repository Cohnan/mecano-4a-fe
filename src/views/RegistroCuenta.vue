<template>

    <div class="signUp_user">


        <div class="container_signUp_user">
             <h2>Registro</h2> 
            <form v-on:submit.prevent="processSignUp" >
                
                <input type="text" v-model="users.nombre" placeholder="Nombre *" 
                    minlength="4" maxlength="100" v-on:blur="trimSpaces($event, users.nombre)" required>
                <img   class="nombre" src="../../Imagenes/otrosIconos/tele.svg"/>
                <br>

                <input type="text" v-model="users.usuario" placeholder="Usuario *" 
                    minlength="4" maxlength="100" v-model.trim="users.usuario" pattern="[^\s]*" oninvalid="this.setCustomValidity('El nombre de usuario no permite espacios en blanco y debe tener al menos 4 caracteres.')" required>
                <br>

                <input type="email" v-model="users.correo" placeholder="Correo *" 
                    minlength="4" maxlength="100" v-model.trim="users.correo" required>
                <br>
                
                <input type="number" v-model="users.telefono" placeholder="Teléfono" 
                    min=0>
                <br>

                <input type="text" v-model="users.pais" placeholder="País">
                <br>

                <input type="text" v-model="users.departamento" placeholder="Estado/Departamento">
                <br>

                <input type="text" v-model="users.ciudad" placeholder="Ciudad">
                <br>      

                <input type="password" v-model="users.password" placeholder="Contraseña *" 
                    minlength="6" maxlength="100" required>
                <br> 
                
                <label for="es_admin"> ¿Administrador? </label>
                <input class="check" type="checkbox" v-model="users.is_staff"  id="es_admin"
                    placeholder="¿Eres administrador?">
                <br/>
            
                <button type="submit">Registrarse</button>
            </form>
        </div>

    </div>

</template>

<script>
import gql from "graphql-tag";

export default {
    name: "user",

    data: function() {
        return {
        users: {
            nombre: null,
            usuario: null,
            correo: null,
            telefono: null,
            pais: null,
            departamento: null,
            ciudad: null,
            password: null,
            is_staff: false,

         },
        };
    },

    mounted: function() {
        document.querySelector("#toggleLog").checked = true;
    },

    methods: {
        processSignUp: async function() {
            await this.$apollo
            .mutate({
                mutation: gql`
                mutation($registroInput : UsuarioIn!) {
                    registrarUsuario(registroInput:$registroInput) {
                    refresh
                    access
                    }
                }
                `,
            variables: {
                registroInput: this.users,
            },
        })
            .then((result) => {
            let dataLogIn = {
                usuario: this.users.usuario,
                token_access: result.data.registrarUsuario.access,
                token_refresh: result.data.registrarUsuario.refresh,
                };

            this.$emit("registroExitoso", dataLogIn);
            alert("Registro exitoso: " + this.users.usuario);

            })
            .catch((error) => {
            alert("Error registrando al usuario. " + error);
            console.log(JSON.stringify(error))
            });

        },

        trimSpaces: function(evento, valor) {
            evento.srcElement.value = evento.srcElement.value.trim();
        }
    },
}
</script>


<style scoped>

.signUp_user{
    margin: -8px;
    padding: 0;
    height: 100vh;
    background-image: url(../../Imagenes/cieloHome.jpg);
    background-size: cover;
    position: relative;
    background-repeat: no-repeat;      
    overflow: hidden;
    display: flex;
    justify-content: center;
    align-items: center;
    top: -8rem;

}
 
.container_signUp_user {
    position: relative;
    bottom: 20pt;
    left: 10pt;
    
    width: 50vw;
    height: 60vh;
    
   
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
}
 
 
.signUp_user form{
    width: 20vw;
}
 
.signUp_user input{
    width: 20vw;
    height: 3vh;

    box-sizing: border-box;
    padding: 10pt 20pt;
    margin: 3pt 0;
    background-color: transparent;
    border: 1px solid rgb(217,217,217);
    border-radius: 30px;
    font-family: Questa Grande;
    text-shadow:black 0.1em 0.1em 0.2em;
    box-shadow: 0 0 10px rgb(235, 245, 239);
}
 
.signUp_user button{
    position:absolute;
    left: calc(50% - 5vw);
    width: 10vw;
    height: 5vh;
    font-family: Questa Grande;
    color: #E5E7E9;
    background:turquoise;
    border: none;
    box-shadow: 1px 1px 1px 1px rgb(248, 252, 252);
    border-radius: 16px;
    align-content: center;
}
 
.signUp_user button:hover{
    color: #E5E7E9;
    background: rgb(30, 212, 185);
}

 .signUp_user label {
    display: flex;
    justify-content: center;
    align-items: center;
    color:white;
    font-size: 1em;
}

 .signUp_user h2 {
    display: flex;
    justify-content: center;
    align-items: center;
    color:white;
    font-size: 20px;
}

.nombre{
     position: absolute; top: 2px; right: 5px;
}

input {
    color:#E5E7E9;
    
}

input[type=checkbox] {
    box-shadow: none;
}

.required:after {
    content:" *";
    color: red;
}


</style>