<template>

  <div id="app" class="app">


    <div class="main-component">
      <router-view  
        v-on:completedLogIn="completedLogIn"
        v-on:completedSignUp="completedSignUp"
        v-on:logOut="logOut"
      >
      </router-view>
    </div>
  </div>

</template>


<script>
import Swal from 'sweetalert2';
export default {
  name: 'App',

  computed: {
    is_auth: {
      get: function() {
        return this.$route.meta.requiresAuth;
      },
      set: function() { }
    }
  },

  methods:{

    loadLogIn: function(){
      this.$router.push({name: "logIn"})
    },

    loadSignUp: function(){
      this.$router.push({name: "signUp"})
    },

    completedLogIn: function(data) {
			localStorage.setItem("username", data.username);
			localStorage.setItem("token_access", data.token_access);
			localStorage.setItem("token_refresh", data.token_refresh);
			this.loadHome();
    },

    completedSignUp: function(data) {
      Swal.fire(
        'Registro de Usuario',
        'Se ha registrado satisfactoriamente!!!',
        'success'
      );
			this.loadLogIn();
    },

    loadHome: function() {
      this.$router.push({ name: "home" });
    },

    loadAccount: function () {
			this.$router.push({ name: "account" });
		},

    loadTransaction: function(){
      this.$router.push({ name: "bid" });
    },

    logOut: function () {
			localStorage.clear();
      
      Swal.fire({
      title: 'Has finalizado sesión satisfactoriamente.  Vuelve pronto!',
      width: 350,
      padding: '0.5em',
      color: '#000000',
      background: '#000000 url("https://img.freepik.com/vector-gratis/diseno-fondo-blanco-estilo-perspectiva-3d-forma-hexagonal_1017-27558.jpg?size=626&ext=jpg")',
      backdrop: `
        rgba(0,0,123,0.4)
        url("https://i.pinimg.com/originals/2a/e4/dd/2ae4dde866c818d5ecee549cca05def2.gif")
        left top
        no-repeat
      `
    })
      this.loadLogIn();
		},
  }
}
</script>


<style>

  body{
    margin: 0 0 0 0;
  }

  /*.header{
    margin: 0%;
    padding: 0;
    width: 100%;
    height: 10vh; 
    min-height: 100px;

    background-color: #283747 ;
    color:#E5E7E9  ;

    display: flex;
    justify-content: space-between;
    align-items: center;
  }*/

  .header h1{
    width: 10%;
    text-align: center;
    color: #E5E7E9;
  }

  .header nav {
    height: 100%;
    width: 100%;

    display: flex;
    justify-content: space-around;
    align-items: center;

    font-size: 20px;
  }

  .header nav button{
    color: #E5E7E9;
    background: #000000;
    border: 1px solid #E5E7E9;

    border-radius: 5px;
    padding: 10px 20px;
  }

  .header nav button:hover{
    color: #000000;
    background: #E5E7E9;
    border: 1px solid #E5E7E9;
  }

  
  .main-component{
    height: 75vh;
    margin: 0%;
    padding: 0%;

    background: #FDFEFE ;
  }

 
  

  

</style>
