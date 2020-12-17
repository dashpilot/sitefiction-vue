<template>
  <div class="float-right">
<button class="btn btn-outline-dark" id="save" v-on:click="showSave = true">Save</button>

  <transition name="fade">
  <div class="backdrop" v-if="showSave">

<div class="modal" tabindex="-1">
  <div class="modal-dialog">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title">Save your design</h5>
        <button type="button" class="close" v-on:click="showSave = false">&times;</button>
      </div>
      <div class="modal-body">
        <p>Modal body text goes here.</p>
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-primary" v-on:click="save">Download</button>
      </div>
    </div>
  </div>
</div>


  </div>
  </transition>
</div>
</template>

<style>
.backdrop{
  background-color: rgba(0,0,0,0.5);
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 9999;
}

.modal{
  display: block;
  margin-top: 5%;
}


</style>

<script>
module.exports = {
    props: ['editing'],
    data: function() {
        return {
          showSave: false
        }
    },
    methods: {
      save() {

        let opts = {};
        opts.html = document.querySelector('#sf-main').innerHTML;

        fetch('api/save', {
            method: 'post',
            body: JSON.stringify(opts)
          }).then(function(response) {
            return response.json();
          }).then(function(res) {
            console.log(res);
          });

      },
    },
}
</script>
