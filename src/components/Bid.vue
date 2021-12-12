<template>
<div class="header">
       <div class="collapse" id="navbarToggleExternalContent">
          <div class="bg-dark p-4">
            <h5 class="text-white h4">Participa!!!</h5>
            <p class="text-muted">Subasta y licita por el producto que desees.</p>
            <lu>
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
<div class="body2">
              <Dialog  v-model:visible="display" :breakpoints="{'960px': '75vw', '640px': '100vw'}" :style="{width: '45vw'}">
                  <div id="tabla" class="col-8" v-if="bidsByUser.length > 0">
                    <div style="text-align: left" >
                      <h2>Subastas Disponibles</h2>
                    </div>
                    <DataTable style="width:45vw" :value="bidsByUser" :paginator="true" :rows="3" 
                        paginatorTemplate="CurrentPageReport FirstPageLink PrevPageLink PageLinks NextPageLink LastPageLink RowsPerPageDropdown"
                        :rowsPerPageOptions="[3]" responsiveLayout="scroll"
                        currentPageReportTemplate="Showing {first} to {last} of {totalRecords}" >
                        <Column field="bid_id" header="ID Puja"></Column>
                        <Column field="user" header="ID Usuario"></Column>
                        <Column field="auction" header="ID subasta"></Column>
                        <Column field="offer" header="Oferta"></Column>
                        <Column  field="bid_time" header="Fecha de la puja" ></Column>
                      
                        <template #paginatorstart>
                            <Button type="button" icon="pi pi-wallet" class="p-button-text" @click="loadBid()"/>
                        </template>
                        <template #paginatorend>
                            
                        </template>
                    </DataTable>    
                  </div>
                  <div class="alert alert-danger" role="alert" v-else>
                    No existe pujas con relación a esta subasta!
                  </div>              
                </Dialog>
              
              
                    
                    <div id="subasta"  class="tabla " style="height:80%; width:35%; " >
                        <h2>Licita por el Producto que desees </h2>
                        <div style="width:100%;align-items=center;">
                          <label for="value">ID Usuario:</label>
                            <InputText type="number" v-model="id_user"  placeholder="Id del Usuario:" :disabled="true" />
                            <br>  
                            <label for="value">Subasta:</label>
                            <Dropdown @click="listAuction();loadPriceAuction()" style="width:100%" v-model="id_auction" :options="auctions" optionLabel="label" optionValue="value" placeholder="Seleccionar subasta" />
                            <br> 
                            <label for="value">Oferta:</label>
                            <InputNumber v-model="offer" mode="currency" currency="COP" locale="en-US" style="width:100%"  />
                            
                            <br> 
                            <br>
                            <Button class="p-button-sm" style="margin:0.9%" @click="createAuction()" label="Pujar" :disabled="!id_auction||!offer||basePrice>=offer"  />
                            <Button class="p-button-sm" style="margin:0.9%" @click="display=true; listBids(); loadPriceAuction()" label="Consultar Pujas"  :disabled="!id_auction"/>
                            <Button class="p-button-sm" style=" margin:0.9%;" @click="deleteBid()" label="Eliminar" :disabled="!id_auction||!offer" />
                        </div>
                      </div>  
        
</div>
    
  
<div class="footer"><h2>Misión TIC 2022</h2></div>
</template>


<script>
import gql from "graphql-tag";
import jwt_decode from "jwt-decode";
import Dropdown from 'primevue/dropdown';
import InputNumber from 'primevue/inputnumber';
import InputText from 'primevue/inputtext';
import Calendar from 'primevue/calendar';
import Button from 'primevue/button';
import Carousel from 'primevue/carousel';
import Dialog from 'primevue/dialog';
import Swal from 'sweetalert2';
import DataTable from 'primevue/datatable';
import Column from 'primevue/column';

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
      DataTable,
      Column,
  },

  data: function () {
    return {
      id_user: jwt_decode(localStorage.getItem("token_refresh")).user_id,
      id_auction: '',
      offer:'',
      basePrice:'',
      display:false,
      username: localStorage.getItem("username") || "none",
      auctions:[],
      bidsByUser:[],
    };
  },
  
 methods: {
      loadBid: function(){
          this.$router.push({name: "bid"})

        },
      loadHome: function(){
      this.$router.push({name: "home"})
      },
       loadPriceAuction: function(){
          if(this.id_auction!=''){
            this.priceAuction();
          }
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
              }
            }
            `,
           
          })
          .then((result) => {
            this.auctions = result.data.auctions.map(auction => {
                return {
                    label: `ID Subasta: ${auction.auction_id}`,
                    value: auction.auction_id
                }
            });

          })
          .catch((error) => {
       
            alert("No se pudo encontrar la subasta.");
          });
      
      },
      priceAuction: async function(){
          await this.$apollo
          .mutate({
            mutation: gql`
              query AuctionById($auctionByIdId: Int!) {
              auctionById(id: $auctionByIdId) {
                base_offer
              }
            }
            `,
            variables: {
              auctionByIdId: this.id_auction,
            },
           
          })
          .then((result) => {
            this.offer = result.data.auctionById.base_offer;
             this.basePrice = result.data.auctionById.base_offer;
          })
          .catch((error) => {
              Swal.fire({
            position: 'top-end',
            icon: 'warning',
            title: "No se pudo encontrar la subasta.",
            showConfirmButton: false,
            timer: 2000
          });
          });
      
      },

        deleteBid: async function(){
          await this.$apollo
          .mutate({
            mutation: gql`
              mutation DeleteBid($deleteBidUser2: Int!, $deleteBidAuction2: Int!) {
              deleteBid(user: $deleteBidUser2, auction: $deleteBidAuction2) {
                message
              }
            }
            `,
            variables: {
              deleteBidUser2: this.id_user,
              deleteBidAuction2:this.id_auction
            },
          })
          .then((result) => {
            this.id_auction= '';
            this.offer='';
            Swal.fire({
            position: 'top-end',
            icon: 'success',
            title: "se eliminaron tus pujas, sigue participando!!!",
            showConfirmButton: false,
            timer: 2000
          });
          })
          .catch((error) => {
            
            alert("No se pudo eliminar las pujas de tu cuenta.");
          });
      
      },

        createAuction: async function(){
          await this.$apollo
          .mutate({
            mutation: gql`
              mutation NewBid($user: Int!, $auction: Int!, $offer: Float!) {
              newBid(user: $user, auction: $auction, offer: $offer) {
                message
              }
            }
            `,
            variables: {
              user: this.id_user,
              auction : this.id_auction,
              offer: this.offer
            },
          })
          .then((result) => {
            this.id_auction= '';
            this.offer='';
            Swal.fire({
            position: 'top-end',
            icon: 'success',
            title: "se realizó con exito  tu puja, sigue participando!!!",
            showConfirmButton: false,
            timer: 2000
          });
            
          })
          .catch((error) => {
            
            alert("No se pudo crear la puja por ser inferior al precio base o ya expiró la misma.");
          });
      
      },
       listBids: async function(){
          await this.$apollo
          .mutate({
            mutation: gql`
            query BidByUserAuction($user: Int!, $auction: Int!) {
              bidByUserAuction(user: $user, auction: $auction) {
                bid_id
                user
                auction
                offer
                bid_time
              }
            }
            `,
            variables: {
              user: this.id_user,
              auction:this.id_auction
            },
          })
          .then((result) => {
            this.bidsByUser= result.data.bidByUserAuction;
            
          })
          .catch((error) => {
            alert(error,"no se encontró pujas asociada al usuario.");
          });
      
      },
  },



  created: function () {
    this.listAuction();
    this.loadPriceAuction(); 
    
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
.body2{
  height:100%;
  background: url("https://artesaniasdecolombia.com.co/Documentos/Contenido/34181_catalogo-productos-artesanias-colombia-2020-g.jpg");
}
.footer h2{
    width: 100%;
    height: 10%;
    padding-top: 2%;
}
</style>