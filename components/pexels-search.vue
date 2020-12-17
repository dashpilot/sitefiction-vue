<template>

<span v-if="edit_img">

<div class="label">Pexels Image Search!</div>
<div class="input-group">
  <input type="text" class="form-control" placeholder="e.g. mountain" v-model="query">
  <div class="input-group-append">
    <button class="btn btn-outline-secondary" type="button" id="button-addon2" v-on:click="searchPexels">
      <span class="spinner-border spinner-border-sm" role="status" aria-hidden="true" id="loading" style="display: none;"></span>
      Search</button>
  </div>
</div>

<span v-for="photo in photos">
  <img :src="photo.url" class="img-fluid mt-3 sf-thumb" v-on:click="setImage" />
</span>

</span>

</template>

<style>
.sf-thumb{
  cursor: pointer;
}
</style>

<script>
module.exports = {
    props: ['edit_img'],
    data: function() {
        return {
          query: '',
          photos: [],
        }
    },
    methods: {
      searchPexels() {
        console.log('searching ' + this.query);
        let app = this;
        fetch("https://api.pexels.com/v1/search?query=" + this.query, {
            headers: {
              Authorization: '563492ad6f9170000100000193a694091f5340f3963db995f31bb5e6'
            }
          })
          .then(response => response.json())
          .then(function(result) {
            result.photos.forEach(function(item) {
              app.photos.push({
                url: item.src.landscape
              })
            })
          })
          .catch(err => console.log(err))
      },
      setImage(e) {
        let url = e.target.src;
        let target = this.edit_img;
        console.log(target);
        if (target.tagName == 'IMG') {
          target.src = url;
        } else {
          target.style.backgroundImage = 'url(' + url + ')';
        }

      }
    },
}
</script>
