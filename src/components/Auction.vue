<template>
<div class="header">
       <div class="collapse" id="navbarToggleExternalContent">
          <div class="bg-dark p-4">
            <h5 class="text-white h4">Participa!!!</h5>
            <p class="text-muted">Subasta y licita por el producto que desees.</p>
            <lu>
              <li>
               <Button label="Licitar" class="p-button-link"   @click="loadBid()"/>
             </li>
             <li>
               <Button label="Atrás" class="p-button-link"   @click="loadHome()"/>
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
<div class="body">
              <Dialog  v-model:visible="display" :breakpoints="{'960px': '75vw', '640px': '100vw'}" :style="{width: '50vw'}">
                  
                  <div  style="width:60%; justify-content: center ;display: flex; flex-direction: column;margin-left: 20%">
                    <div >
                    <h2>Actualiza la subasta</h2>
                    </div>
                    <label for="value">Id de la subasta:</label>
                    <input type="number" v-model="id_auction" placeholder="Id de la subasta" :disabled="true" />
                    
                  
                      <label for="value">Id del producto:</label>
                    <InputText type="text" v-model="id" placeholder="Id del producto"  :disabled="true"/>
                    
                  
                      <label for="value">Precio base de la subasta:</label>
                    <InputNumber v-model="basePrice" mode="currency" currency="COP" locale="en-US" style="width:100%"/>
                    
                    
                      <label for="value">Fecha de inicio de la subasta:</label>
                    <input type="date" v-model="time_starting" placeholder="Fecha de inicio." />
                    
                      <label for="value">Fecha de cierre de la subasta:</label>
                    <input type="date" v-model="time_ending" placeholder="Fecha de cierre." />
                    <br>

                    <div class="p-buttonset" style="align-items:center">
                        <Button style="margin:0.2%" label="Actualizar"  icon="pi pi-refresh" @click="updateAuction();display=false"/>
                
                        <Button style="margin:0.2%" label="Cancelar" icon="pi pi-times" @click="display=false" />
                    </div>
                  </div>
                    
                    
                </Dialog>
              
              
                    
                    <div id="subasta"  class="mb-3; " >
                        <h2>Subasta tu Producto</h2>
                        <div style="width:60%">
                          <InputText type="text" v-model="id"  placeholder="Id del Producto" />
                            
                            <Button icon="pi pi-search" class="p-button-rounded p-button-success p-button-text" @click="findProduct()" />
                            <br>  
                        
                            <InputText type="text" v-model="productName"  placeholder="Nombre Producto" :disabled="productFound==''"/>
                            <br>
                            <br> 
                            <InputNumber v-model="basePrice" mode="currency" currency="COP" locale="en-US" style="width:100%" :disabled="productFound==''"/>
                            
                            <br> 
                            <Dropdown style="width:100%" v-model="category" :options="categories" optionLabel="label" optionValue="value" placeholder="Seleccionar categoria" :disabled="productFound==''"/>
                            <br> 
                            <br> 
                            <input style="width:100%" type="date" v-model="time_starting" placeholder="Fecha de Inicio" :disabled="productFound==''" />
                            <br> 
                            <br> 
                              <input style="width:100%" type="date" v-model="time_ending" placeholder="Fecha de Cierre" :disabled="productFound==''" />
                            <br>
                            <br>
                            <Button class="p-button-sm" style="margin:0.9%" @click="createAuction()" label="Subastar" :disabled="!id||!category||!basePrice||!productName||!time_starting|| !time_ending || productFound==''" />
                            <Button class="p-button-sm" style="margin:0.9%" @click="display=true" label="Modificar" :disabled=" productFound!=''" />
                            <Button class="p-button-sm" style=" margin:0.9%;" @click="deleteAuction()" label="Eliminar" :disabled=" productFound!=''" />
                        </div>
                      </div>  
        
</div>
    
  
<div class="footer"><h2>Misión TIC 2022</h2></div>
</template>


<script>
import gql from "graphql-tag";
import Dropdown from 'primevue/dropdown';
import InputNumber from 'primevue/inputnumber';
import InputText from 'primevue/inputtext';
import Calendar from 'primevue/calendar';
import Button from 'primevue/button';
import Carousel from 'primevue/carousel';
import Dialog from 'primevue/dialog';
import Swal from 'sweetalert2';

export default {
  name: "Auction",
  components: {
      Dropdown,
      Calendar,
      Button,
      InputText,
      InputNumber,
      Carousel,
      Dialog,
  },

  data: function () {
    return {
      display:false,
      username: localStorage.getItem("username") || "none",
      products: [],
      productFound:'',
      id_auction:0,
      id:"",
      productName:"",
      basePrice:0,
      category:"",
      time_starting: "",
      time_ending:"",
      auctions:[],
      
      categories:[
        {label: 'Lujo', value: 'Lujo'},
        {label: 'Decorativo', value: 'Decorativo'},
        {label: 'Fantasía', value: 'Fantasía'}
      ],
      
    };
  },
  
 methods: {
      loadBid: function(){
          this.$router.push({name: "bid"})

        },
      loadHome: function(){
      this.$router.push({name: "home"})
      },
      
      findProduct: async function(){
      await this.$apollo
        .mutate({
          mutation: gql`
            query ProductById($productByIdId: String!) {
            productById(id: $productByIdId) {
              id
              productName
              basePrice
              category
            }
          }
          `,
          variables: {
            productByIdId: this.id,
            
          },
        })
        .then((result) => {
          this.productFound='';
          this.productName= result.data.productById.productName;
          this.basePrice=   result.data.productById.basePrice;
          this.category=  result.data.productById.category;
          console.log(typeof this.basePrice);
          console.log(this.basePrice);
          this.listAuction();
          
        })
        .catch((error) => {
          this.productFound = this.id;
          this.productName= '';
          this.basePrice=0;
          this.category= '';
          this.time_starting='';
          this.time_ending='';
          Swal.fire({
            position: 'top-end',
            icon: 'warning',
            title: 'Producto no encontrado!!!',
            showConfirmButton: false,
            timer: 2000
          });
          
        });
        },


      updateAuction: async function(){
        await this.$apollo
          .mutate({
            mutation: gql`
              mutation UpdateAuction($auctionId: Int!, $product: String!, $baseOffer: Float!, $timeStarting: String!, $timeEnding: String!) {
              updateAuction(auction_id: $auctionId, product: $product, base_offer: $baseOffer, time_starting: $timeStarting, time_ending: $timeEnding) {
                message
              }
            }
            `,
            variables: {
              auctionId: this.id_auction,
              product: this.id,
              baseOffer: this.basePrice,
              timeStarting:this.time_starting,
              timeEnding:this.time_ending,
            },
          }).then((result) => {
            Swal.fire({
            position: 'top-end',
            icon: 'success',
            title:  'Subasta actualizada!.',
            showConfirmButton: false,
            timer: 2000
          })
          }).catch((error) => {
            console.log(typeof this.id_auction);
            console.log(this.id_auction);
            console.log(typeof this.id);
            console.log(this.id);
            console.log(typeof this.basePrice);
            console.log(this.basePrice);
            console.log(typeof this.time_starting);
            console.log(this.time_starting);
            alert(error);
          });
        },
        

        listAuction: async function(){
          await this.$apollo
          .mutate({
            mutation: gql`
              query {
              auctions {
                auction_id
                product
                base_offer
                time_starting
                time_ending
              }
            }
            `,
           
          })
          .then((result) => {
            this.auctions = result.data.auctions.filter(auction => auction.product == this.id);
            this.basePrice=this.auctions[0].base_offer;
            this.time_starting= this.auctions[0].time_starting;
            this.time_ending= this.auctions[0].time_ending;
            this.id_auction= this.auctions[0].auction_id;
            console.log(this.auctions[0].auction_id);
            Swal.fire({
            position: 'top-end',
            icon: 'success',
            title: 'Se ha encontrado la subasta.',
            showConfirmButton: false,
            timer: 2000
          });
          })
          .catch((error) => {
            Swal.fire({
                  position: 'top-end',
                  icon: 'warning',
                  title: 'No se ha encontrado la subasta.',
                  showConfirmButton: false,
                  timer: 2000
                });
          });
      
      },
        deleteAuction: async function(){
          await this.$apollo
          .mutate({
            mutation: gql`
              mutation DeleteProductAuction($idProduct: String!, $idAuction: Int!) {
              deleteProductAuction(id_product: $idProduct, id_auction: $idAuction) {
                message
              }
            }
            `,
            variables: {
              idAuction: this.id_auction,
              idProduct:this.id,
            },
          })
          .then((result) => {
            
            this.id='';
            this.productName= '';
            this.basePrice='';
            this.category= '';
            this.time_starting='';
            this.time_ending='';
            Swal.fire({
            position: 'top-end',
            icon: 'success',
            title: result.data.deleteProductAuction.message,
            showConfirmButton: false,
            timer: 2000
          });
          })
          .catch((error) => {
            Swal.fire({
            position: 'top-end',
            icon: 'warning',
            title: 'No se pudo eliminar la subasta y/o producto.',
            showConfirmButton: false,
            timer: 2000
          });
          });
      
      },

        createAuction: async function(){
          await this.$apollo
          .mutate({
            mutation: gql`
              mutation NewProductWithAuction($id: String!, $productName: String!, $basePrice: Float!, $category: String!, $time_starting: String!, $time_ending: String!) {
              newProductWithAuction(id: $id, productName: $productName, basePrice: $basePrice, category: $category, time_starting: $time_starting, time_ending: $time_ending) {
                message
              }
            }
            `,
            variables: {
              id: this.id,
              productName : this.productName,
              basePrice: this.basePrice,
              category: this.category,
              time_starting: this.time_starting,
              time_ending: this.time_ending

            },
          })
          .then((result) => {
            Swal.fire({
            position: 'top-end',
            icon: 'success',
            title: result.data.newProductWithAuction.message,
            showConfirmButton: false,
            timer: 2000
          });
          })
          .catch((error) => {
            this.id='';
            this.productName= '';
            this.basePrice='';
            this.category= '';
            this.time_starting='';
            this.time_ending='';
           Swal.fire({
            position: 'top-end',
            icon: 'warning',
            title: "No se pudo crear la subasta ni el producto.",
            showConfirmButton: false,
            timer: 1000
          }) 

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
    }, 
  },

  created: function () {
    this.$apollo.queries.products.refetch();
    this.productFound='';
    console.log(this.products);
  }
};
</script>




<style>
#subasta {
  padding: 30px;
  border: 2px solid rgba(0, 0, 0, 0.3);
  opacity: 0.8;
  margin-left: 35%;
  
  background-color: whitesmoke;
  width:40%;
  align-items: center; 
  height:100%;
  display: flex;
  flex-direction: column;
  justify-content: center ;

  
  
  
}

#subasta h2 {
  font-size: 20px;
  color: #283747;
}
#subasta span {
  color: rgb(40, 20, 220);
  font-weight: bold;
}
.footer{
  height:5%;
  width: 100%;

}
.body{
  height:100%;
  background: url("https://sieteartesanos.com/wp-content/uploads/2020/10/Artesanias-Guacamaya.jpg");
}
.footer h2{
    width: 100%;
    height: 10%;
    padding-top: 2%;
}
</style>