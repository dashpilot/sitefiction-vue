<template>
  <div class="username">
  <span v-if="loggedIn == true">


  <button class="btn btn-outline-dark mr-2" id="save" v-on:click="save">
    <span v-if="loading">
      <span class="spinner-border spinner-border-sm" role="status" aria-hidden="true"></span> &nbsp;
    </span>
  Save</button>


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
  width: 100px;
}

#user{
  padding-top: 3px;
  padding-bottom: 7px;
}
</style>

<script>
module.exports = {
    data: function() {
        return {
          loggedIn: false,
          displayName: '',
          showUser: false,
          userId: false,
          loading: false
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
          app.userId = user.uid;

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
          app.userId = false;

        }).catch(function(error) {
        console.log(error);
        });

      },
      save(){

        //this.editing_status = false; // this also updates the parent 'editing' status
        let app = this;

        app.loading = true;

        // hacky way of triggering the mode change
        if(document.getElementById('switch-normal').checked == false){
          document.getElementById('switch-normal').click();
        }

        document.querySelectorAll('.edit').forEach(function(e){
          e.removeAttribute('contentEditable');
        })


        setTimeout(function(){
        var headers = [];
        document.querySelectorAll('#sf-header .editable').forEach(function(e){
          headers.push(e.innerHTML);
        })
        var contents = [];
        document.querySelectorAll('#sf-main .editable').forEach(function(e){
          contents.push(e.innerHTML);
        })

        firebase.firestore().collection("users").doc(app.userId).set({
            headers: headers,
            contents: contents,
        })
        .then(function() {
            console.log("Document successfully written!");
            app.loading = false;
            document.querySelectorAll('.edit').forEach(function(e){
              e.contentEditable = true;
            })
        })
        .catch(function(error) {
            console.error("Error writing document: ", error);
        });
      }, 1000);

      },
      setHeaders: function (value) {
        this.$emit('update-headers', value);
      },
      setContents: function (value) {
        this.$emit('update-contents', value);
      }


    },
    mounted: function(){
      let app = this;
      firebase.auth().onAuthStateChanged(function(user) {
        if (user) {
          // User is signed in.
          app.loggedIn = true;
          app.displayName = user.displayName;
          app.userId = user.uid;


          var docRef = firebase.firestore().collection("users").doc(app.userId);


          docRef.get().then(function(doc) {
              if (doc.exists) {
                console.log("Document data:", doc.data());
                var mydoc = doc.data();

                let headers = [];
                mydoc.headers.forEach(function(item){
                  headers.push(item);
                })
                app.setHeaders(headers);

                let contents = [];
                mydoc.contents.forEach(function(item){
                  contents.push(item);
                })
                app.setContents(contents);
              
              } else {
                  // doc.data() will be undefined in this case
                  console.log("No such document!");
              }


          }).catch(function(error) {
              console.log("Error getting document:", error);
          });


        }
      });
    }
}


</script>
