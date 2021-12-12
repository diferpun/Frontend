<template>
    <div class="header">
            
       
       <div class="collapse" id="navbarToggleExternalContent">
          <div class="bg-dark p-4">
            <h5 class="text-white h4">Participa!!!</h5>
            <p class="text-muted">Subasta y licita por el producto que desees.</p>
            <lu>
              <li>
             <Button label="Subastar" class="p-button-link" @click ="loadAuction()"/>
             </li>
             <li>
               <Button label="Licitar" class="p-button-link"  v-if="!is_auth"
                  @click="loadBid"/>
             </li>
             <li>
               <Button label="Cerrar Sesión" class="p-button-link"  v-if="!is_auth"
                  @click="this.$emit('logOut')"/>
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
  <div class="information" style="text-align: left">
    <Dialog  v-model:visible="display" :breakpoints="{'960px': '75vw', '640px': '100vw'}" :style="{width: '50vw'}">
      
       <div  style="width:60%; justify-content: center ;display: flex; flex-direction: column;margin-left: 20%">
         <div >
        <h2>Actualiza o elimina tu cuenta</h2>
        </div>
         <label for="value">Nombre:</label>
        <InputText type="text" v-model="firstnameUser" placeholder="Nombre" />
        
      
          <label for="value">Apellido:</label>
        <InputText type="text" v-model="lastnameUser" placeholder="Apellido" />
        
      
          <label for="value">Usuario:</label>
        <InputText type="text" v-model="usernameUser" placeholder="Usuario" />
        
          <label for="value">Contraseña:</label>
        <InputText type="text" v-model="password" placeholder="Contraseña (Digite la misma contraseña sino desea cambiarla)" />
        
          <label for="value">e-mail:</label>
        <InputText type="text" v-model="emailUser" placeholder="email" />
        <br>

        <div class="p-buttonset" style="align-items:center">
            <Button label="Actualizar" @click="loadUpdate()" icon="pi pi-refresh" :disabled="!userById.firstname || !userById.lastname || !userById.username || !password || !userById.email"/>
            <Button @click="loadDelete()" label="eliminar" icon="pi pi-trash" :disabled="!userById.firstname || !userById.lastname || !userById.username || !password || !userById.email" />
            <Button label="Cancelar" icon="pi pi-times" @click="display=false" />
        </div>
      </div>
        
        
    </Dialog>
      <div class="row" v-if="userId">
              <div class="col-4">
                <div class="card" style="width: 18rem; height: 100%;margin-botton: 1%">
                  <div class="card-body">
                    <h5 class="card-title">¡Bienvenid@ {{userById.firstname}}!</h5>
                    <h6 class="card-subtitle mb-2 text-muted">Tus datos:</h6>
                    <p class="card-text">Nombre: {{userById.firstname}} {{userById.lastname}}.</p>
                    <p class="card-text">email: {{userById.email}}.</p>
                    <a @click="display=true; loadUser()" class="card-link">Configuración</a>
                    
                  </div>
                </div>
              </div>
              <div id="tabla" class="col-8">
                <div style="text-align: left" >
                  <h2>Subastas Disponibles</h2>
                </div>
                <DataTable :value="auctions" :paginator="true" :rows="3" 
                    paginatorTemplate="CurrentPageReport FirstPageLink PrevPageLink PageLinks NextPageLink LastPageLink RowsPerPageDropdown"
                    :rowsPerPageOptions="[3]" responsiveLayout="scroll"
                    currentPageReportTemplate="Showing {first} to {last} of {totalRecords}" v-if="auctions.length > 0">
                    <Column field="auction_id" header="ID Subasta"></Column>
                    <Column field="product" header="ID Producto"></Column>
                    <Column field="base_offer" header="Precio Base"></Column>
                    <Column field="time_starting" header="Inicio"></Column>
                    <Column  field="time_ending" header="Cierre" ></Column>
                   
                    <template #paginatorstart>
                        <Button type="button" icon="pi pi-wallet" class="p-button-text" @click="loadBid()"/>
                    </template>
                    <template #paginatorend>
                        
                    </template>
                </DataTable>    
              </div>
        </div>
     
  </div>
  <div class="footer"><h2>Misión TIC 2022</h2></div>
  
</template>


<script>

import jwt_decode from "jwt-decode";
import Password from 'primevue/password';
import Button from 'primevue/button';
import gql from "graphql-tag";
import DataTable from 'primevue/datatable';
import Column from 'primevue/column';
import Dialog from 'primevue/dialog';
import InputText from  'primevue/inputtext';
import Swal from 'sweetalert2';
import Image from 'primevue/image';

export default {
  name: "Home",
  components: {
      Password,
      Button,
      DataTable,
      Dialog,
      Column,
      InputText,
      Image,
      
  },

  data: function () {
    

    return {
      
      userId: jwt_decode(localStorage.getItem("token_refresh")).user_id,
      userById: {
        username: "",
        firstname: "",
        lastname:"",
        email: "",
        
      },

      usernameUser: "",
      firstnameUser: "",
      lastnameUser:"",
      emailUser: "",
      password:"",
      isadmi:false,
      display:false,
      auctions :[],
      findauction:[],
    };
  },
  methods: {

      loadUser: function(){
      this.usernameUser = this.userById.username;
      this.firstnameUser = this.userById.firstname;
      this.lastnameUser = this.userById.lastname;
      this.emailUser = this.userById.email;

     },

    loadAuction: function(){
      this.$router.push({name: "auction"})

    },
    loadBid: function(){
      this.$router.push({name: "bid"})

    },

    loadDelete: function(){
        this.display =false;
        Swal.fire({
          title: 'Está seguro?',
          text: "No podrás revertir los cambios!",
          icon: 'warning',
          showCancelButton: true,
          confirmButtonColor: '#3085d6',
          cancelButtonColor: '#d33',
          confirmButtonText: 'Sí, modificar!'
        }).then((result) => {
          if (result.isConfirmed) {
              this.processDeleteUser();
          }
          else{
            this.display =true;
          }
          
        })
    },


    loadUpdate: function(){
        this.display = false;
        Swal.fire({
          title: 'Está seguro?',
          text: "No podrás revertir los cambios!",
          icon: 'warning',
          showCancelButton: true,
          confirmButtonColor: '#3085d6',
          cancelButtonColor: '#d33',
          confirmButtonText: 'Sí, modificar!'
        }).then((result) => {
          if (result.isConfirmed) {
              this.processUpdateUser();
          }
          else{
            this.display= true;
          }
        })
        
    },


    updatetable: function(){
      this.$apollo.query({
        query: gql`
        query {
            auctions {
                auction_id
                product
                base_offer
                time_starting
                time_ending
            }
        }
    `}) 
    },

 processDeleteUser: async function() {
      
      await this.$apollo
        .mutate({
          mutation: gql`
            mutation DeleteUser($deleteUserId: Int!) {
            deleteUser(id: $deleteUserId) {
              message
            }
          }
          `,
          variables: {
            deleteUserId: this.userId
            
          },
        })
        .then((result) => {
          Swal.fire({
            position: 'top-end',
            icon: 'success',
            title: result.data.deleteUser.message,
            showConfirmButton: false,
            timer: 2000
          })
          this.$emit('logOut');
        })
        .catch((error) => {

          Swal.fire({
            position: 'top-end',
            icon: 'warning',
            title:error,
            showConfirmButton: false,
            timer: 2000
          })

        });
    },


    processUpdateUser: async function() {
      
      await this.$apollo
        .mutate({
          mutation: gql`
            mutation UpdateUser($id: Int!, $firstname: String!, $lastname: String!, $username: String!, $password: String!, $email: String!, $isadmi: Boolean!) {
            updateUser(id: $id, firstname: $firstname, lastname: $lastname, username: $username, password: $password, email: $email, isadmi: $isadmi) {
              message
            }
          }
          `,
          variables: {
            id: this.userId,
            firstname: this.firstnameUser,
            lastname:this.lastnameUser,
            username:this.usernameUser,
            password: this.password,
            email: this.emailUser,
            isadmi: this.isadmi
            
          },
        })
        .then((result) => {
          
          Swal.fire({
            position: 'top-end',
            icon: 'success',
            title: 'actualizado!',
            showConfirmButton: false,
            timer: 2000
          })
        })
        .catch((error) => {
           Swal.fire({
            position: 'top-end',
            icon: 'success',
            title: error,
            showConfirmButton: false,
            timer: 2000
          })
          
        });
    },
  },
  
  apollo: {
    
    userById: {
      
      query: gql`
        query($id: Int!){userById(id: $id) {
        firstname
        lastname
        username
        email
        }
      }
      `,
      variables() {
        return {
          id: this.userId
        };
      }
    },
    auctions: {
      query: gql`
        query {
            auctions {
                auction_id
                product
                base_offer
                time_starting
                time_ending
            }
        }
    `
    },
  },
    created: function() {
      
      this.$apollo.queries.auctions.refetch();  
      this.$apollo.queries.userById.refetch(); 
      console.log(this.auctions);
      console.log(this.findauction);
    }
  
};
</script>


<style>
#tabla{
    border: 0.5px solid  transparent;
    border-radius: 5px;
    opacity: 0.8;
    height: 100%;
    
}
.col-4{
height:100%;
opacity: 0.8;
}


.information {
  margin-top: 0%;
  margin-bottom: 0%;
  padding: 0%;
  width: 100%;
  height: 100%;
  background: url("https://artesaniasdecolombia.com.co/Documentos/Glosario/833_artesania-contemporanea-o-neoartesania-1.jpg");
  display: flex;
  flex-direction: column;
  justify-content: center ;
  align-items: center;
}

.information h1 {
  font-size: 30px;
  color: #283747;
}

.information h2 {
  font-size: 20px;
  color: #ffffff;
}

.information span {
  color: crimson;
  font-weight: bold;
}



</style>
