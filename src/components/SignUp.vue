<template>
    <div class="header">
       
       <div class="collapse" id="navbarToggleExternalContent">
          <div class="bg-dark p-4">
            <h5 class="text-white h4">Participa!!</h5>
            <p class="text-muted">Subasta y licita por el producto que desees.</p>
            <lu>
             <li>
               <Button label="Iniciar Sesión" class="p-button-link"  v-if="!is_auth"
                  @click="loadLogIn()"/>
             </li>
             </lu>
          </div>
        </div>
        <nav class="navbar navbar-dark bg-dark">
          <div class="container-fluid">
            <h1>SubastaYA</h1>
            <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarToggleExternalContent" aria-controls="navbarToggleExternalContent" aria-expanded="false" aria-label="Toggle navigation">
              <span class="navbar-toggler-icon"></span>
            </button>
          </div>
        </nav>
    </div>
    <div class="signUp_user">
        <div class="container_signUp_user">
            <h3>Registrarse</h3>

            <form v-on:submit.prevent="processSignUp" >
                <input type="text" v-model="firstname" placeholder="Nombre">
                <br>
                <input type="text" v-model="lastname" placeholder="Apellido">
                <br>
                <input type="text" v-model="username" placeholder="Usuario">
                <br>
                <Password  style="width:100%" v-model="password" toggleMask placeholder="Contraseña"/>
                <br>
                <input type="email" v-model="email" placeholder="email">
                <br>
                <Button label="Save" icon="pi pi-check" :disabled="!firstname ||!lastname || !username || !password ||!email"
                @click="processSignUp()" />
                
            </form>
        </div>

    </div>
    <div class="footer"><h2>Misión TIC 2022</h2></div>

</template>


<script>
import gql from "graphql-tag";
import Password from 'primevue/password';
import Button from 'primevue/button';

export default {
    name: "SignUp",
    components: {
      Password,
      Button
    },
    data: function() {
        return {
            firstname: "",
            lastname: "",
            username: "",
            password: "",
            name: "",
            email: "",
            isadmi: false
        
        };
    },
  methods: {
    loadLogIn: function(){
        this.$router.push({name: "logIn"})
    },
    processSignUp: async function() {
      await this.$apollo
        .mutate({
          mutation: gql`
            mutation($firstname: String!, $lastname: String!, $username: String!, $password: String!, $email: String!, $isadmi: Boolean!){
            newUser(firstname: $firstname, lastname: $lastname, username: $username, password: $password, email: $email, isadmi: $isadmi) {
              refresh
              access
            }
          }
          `,
          variables: {
            firstname: this.firstname,
            lastname: this.lastname,
            username: this.username,
            password: this.password,
            email: this.email,
            isadmi: this.isadmi,
          },
        })
        .then((result) => {
          let dataLogIn = {
            username: this.username,
            token_access: result.data.newUser.access,
            token_refresh: result.data.newUser.refresh,
          };

          this.$emit("completedSignUp", dataLogIn);
        })
        .catch((error) => {
          alert(error, "ERROR: Fallo en el registro.");
        });
    },

  },
}
</script>


<style>


    .signUp_user{
        margin: 0;
        padding: 0%;
        height: 100%;
        width: 100%;
        background: url("https://artesaniasdecolombia.com.co/PortalAC/images/laboratorio-altiplano-artesanias-colombia-2016.jpg");
        display: flex;
        justify-content: center;
        align-items: center;
    }

    .container_signUp_user {
        border: 3px solid  #000000;
        border-radius: 10px;
        width: 30%;
        height: 80%;
        background-color: whitesmoke ;
        opacity: 0.7;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
    }

    .signUp_user h2{
        color: #000000;

    }

    .signUp_user form{
        width: 70%;
        
        
    }

    .signUp_user input{
        height: 40px;
        width: 100%;

        box-sizing: border-box;
        padding: 10px 20px;
        margin: 5px 0;

        border: 1px solid #000000;
    }

    .signUp_user button{
        width: 100%;
        height: 40px;

        color: #E5E7E9;
        background: #000000;
        border: 1px solid #E5E7E9;

        border-radius: 5px;
        padding: 10px 25px;
        margin: 5px 0 25px 0;
    }

    .signUp_user button:hover{
        color: #E5E7E9;
        background: rgb(58, 113, 231);
        border: 1px solid #004691;
    }
    .footer{
    margin: 0;
    padding: 0;
    width: 100%;
    height: 10vh;
    min-height: 100px; 

    background-color: #000000;
    color: #E5E7E9;
    display: flex;
    justify-content: center;
    align-items: center;

  }
  .footer h2{
     width: 100%;
    height: 10%;
    padding-top: 2%;
    
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .header h1 {
    height:0.3px;
    padding: 0.5%;
  }
  

</style>