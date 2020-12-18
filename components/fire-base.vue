<template>
  <div class="username">
  <span v-if="loggedIn == true">


  <button class="btn btn-outline-dark mr-2" id="save" v-on:click="save">Save</button>


  <button class="btn btn-outline-dark" id="user" v-on:click="showUser = true">
      <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" fill="currentColor" class="bi bi-person-fill" viewBox="0 0 16 16">
    <path fill-rule="evenodd" d="M3 14s-1 0-1-1 1-4 6-4 6 3 6 4-1 1-1 1H3zm5-6a3 3 0 1 0 0-6 3 3 0 0 0 0 6z"/>
  </svg>
  </button>

  </span>
  <span v-else>
  <button class="btn btn-outline-dark float-right" v-on:click="login">Log In</button>
  </span>


  <transition name="fade">
  <div class="backdrop" v-if="showUser">

<div class="modal" tabindex="-1">
  <div class="modal-dialog">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title">Your Account</h5>
        <button type="button" class="close" v-on:click="showUser = false">&times;</button>
      </div>
      <div class="modal-body">
        Logged in as: <b>{{displayName}}</b>
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-primary" v-on:click="logout">Log Out</button>
      </div>
    </div>
  </div>
</div>
  </div>
  </transition>


</div>
</template>

<style>
.username{
  padding-right: 20px;
  float: right;
}

#save{
  width: 80px;
}

#user{
  padding-top: 3px;
  padding-bottom: 7px;
}
</style>

<script>
module.exports = {
    props: ['editing'],
    data: function() {
        return {
          loggedIn: false,
          displayName: '',
          showUser: false
        }
    },
    methods: {
      login() {
        let app = this;
        firebase.auth().signInWithPopup(provider).then(function(result) {
          // This gives you a Google Access Token. You can use it to access the Google API.
          var token = result.credential.accessToken;
          // The signed-in user info.
          var user = result.user;

          //console.log(user);
          console.log(user.displayName);
          app.loggedIn = true;
          app.displayName = user.displayName;

        }).catch(function(error) {
          console.log(error);
        });
      },
      logout() {
        let app = this;
        firebase.auth().signOut().then(function() {
          // Sign-out successful.
          app.loggedIn = false;
          app.displayName = '';
          app.showUser = false;

        }).catch(function(error) {
        console.log(error);
        });

      },
      save(){
        var ref = firebase.storage().ref().child('data/test.txt');
        var message = 'This is my message.';
        ref.putString(message).then(function(snapshot) {
          console.log('Uploaded a raw string!');
        });
      }
    },
    mounted: function(){
      let app = this;
      firebase.auth().onAuthStateChanged(function(user) {
        if (user) {
          // User is signed in.
          app.loggedIn = true;
          app.displayName = user.displayName;
        }
      });
    }
}


</script>
