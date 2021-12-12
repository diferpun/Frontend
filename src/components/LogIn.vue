<template>
     <div class="header">
       
       <div class="collapse" id="navbarToggleExternalContent">
          <div class="bg-dark p-4">
            <h5 class="text-white h4">Participa!!!</h5>
            <p class="text-muted">Subasta y licita por el producto que desees.</p>
            <lu>
              <li>
             <Button label="Productos Subastados"  class="p-button-link" @click="this.display=true" />
              <Dialog v-model:visible="display" :breakpoints="{'960px': '75vw', '640px': '100vw'}" :style="{width: '50vw'}">
                    <Carousel style="width:100%" :value="products" :numVisible="2" :numScroll="2" :responsiveOptions="responsiveOptions">
                          <template #header>
                              <h5>Productos</h5>
                          </template>
                          <template #item="slotProps">
                              <div class="product-item">
                                  <div class="product-item-content">
                                      <div class="p-mb-3">
                                          <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSlZfBWFwu31p_0m25NwZgQk891563FqgBDrVbKRVopIsmN_vkdUTCygzdhZRl3hcOgkBs&usqp=CAU" :alt="slotProps.data.id" class="product-image" />
                                      </div>
                                      <div>
                                          <h4 class="p-mb-1">{{slotProps.data.productName}}</h4>
                                          <h6 class="p-mt-0 p-mb-3">${{slotProps.data.basePrice}}</h6>
                                          <span :class="'product-badge status-'+slotProps.data.category.toLowerCase()">{{slotProps.data.category}}</span>
                                          <div  style="align-items: center" class="car-buttons p-mt-5">
                                              
                                              <Button @click="loadLogIn()" icon="pi pi-star-fill" class="p-button-success p-button-rounded p-mr-2" />
                                             
                                          </div>
                                      </div>
                                </div>
                              </div>
                            </template>
                        </Carousel>   
                      </Dialog>
                  
             </li>
             <li>
               <Button label="Registrate" class="p-button-link"  v-if="!is_auth"
                  @click="loadSignUp()"/>
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
      <div class="logIn_user">
       
          
          <div class="container_logIn_user">
              <h2>Iniciar sesión</h2>

              <form v-on:submit.prevent="processLogInUser" >
                
                  <InputText type="text" v-model="username" placeholder="Usuario" />
                  <br>
                  
                  <input type="password" class="form-control" id="password" v-model="password"  placeholder="Contraseña">
                  <br>
                  <Button label="Iniciar Sesión" 
                  @click="processLogInUser()"
                  :disabled="!username || !password"/>
                  <br>
                  <div class="mt-3" style="text-align: right">
                  <span>¿Eres nuevo en SubastaYa?</span>
                  <Button label="Registrate" class="p-button-link" v-if="!is_auth" @click="loadSignUp()"/>
                  </div>
              </form>
          </div>

      </div>
    <div class="footer"><h2>Misión TIC 2022</h2></div>
    

</template>


<script>
import gql from "graphql-tag";
import Password from 'primevue/password';
import Button from 'primevue/button';
import Swal from 'sweetalert2';
import InputText from 'primevue/inputtext';
import Dialog from 'primevue/dialog';
import Carousel from 'primevue/carousel';
import Message from 'primevue/message';

export default {
  name: "LogIn",
  components: {
      Password,
      Button,
      InputText,
      Dialog,
      Carousel,
      Message,
    },

  data: function() {
    return {
      
      display: false,
        products:[],
        username: "",
        password: "",
      
    };
  },

  methods: {
    loadLogIn: function(){
      this.display =false;
      this.$router.push({name: "logIn"})
    },
    loadSignUp: function(){
      this.$router.push({name: "signUp"})
    },
    processLogInUser: async function() {
      Swal.fire({
                title: 'Iniciando sesión',
                html: 'Estamos cargando tu Información.',
                allowOutsideClick: false,
                didOpen: () => {
                    Swal.showLoading();
                }
      });
      console.log(this.username)
      console.log(this.password)
      await this.$apollo
        .mutate({
          mutation: gql`
            mutation($username: String!, $password: String!){
              loginUser(username: $username, password: $password){
                access
                refresh
          }
        }
          `,
          variables: {
            username: this.username,
            password: this.password
          },
        })
        .then((result) => {
          Swal.close();
          let dataLogIn = {
            username: this.username,
            token_access: result.data.loginUser.access,
            token_refresh: result.data.loginUser.refresh

          };

          this.$emit("completedLogIn", dataLogIn);
        })
        .catch((error) => {
          Swal.close();
          alert(error,"ERROR 401: Credenciales Incorrectas.");
        });
    },
  },
  apollo: {
    products: {
      query: gql`
        query {
          products {
            id
            productName
            basePrice
            category
            
            }
          }
          `
      }
  },
  created: function () {
    this.$apollo.queries.products.refetch();
  }
};
</script>


<style>
    .p-inputtext, .p-password-panel{
      width:100%;
    }
    
    .logIn_user{
        margin-top: 0%;
        margin-bottom: 0%;
        padding: 0%;
        
        height: 100%;
        width: 100%;
        background: url("https://image.made-in-china.com/202f0j10hwvTWPtJgipY/White-and-Black-Pachira-Macrocarpa-Resin-Tree-Sculpture-Home-Decoration.jpg");
        display: flex;
        justify-content: center;
        align-items: center;
    }

    .container_logIn_user {
        border: 3px solid  #000000;
        border-radius: 10px;
        width: 30%;
        height: 80%;
        margin-top: 1%;
        margin-bottom: 1%;
        opacity: 0.7;
        background-color: whitesmoke ;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
    }

    .logIn_user h2{
        color: #000000;

    }

    .logIn_user form{
        width: 70%;
        
    }

    .logIn_user input{
        height: 40px;
        width: 100%;

        box-sizing: border-box;
        padding: 10px 20px;
        margin: 5px 0;

        border: 1px solid #000000;
    }

    .logIn_user button{
        width: 100%;
        height: 40px;

        color: #E5E7E9;
        background: #000000;
        border: 1px solid #E5E7E9;

        border-radius: 5px;
        padding: 10px 25px;
        margin: 5px 0;
    }

    .logIn_user button:hover{
        color: #E5E7E9;
        background: rgb(69, 39, 238);
        border: 1px solid #0057b4;
    }
    .footer{
    
    padding: 0;
    width: 100%;
    height: 10vh;
    min-height: 100px;
    margin-top: 6%; 

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