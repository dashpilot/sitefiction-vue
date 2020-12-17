<template>

<span v-if="edit_img">

<div id="pexels-search">

<div class="label">Image URL</div>
<input type="text" class="form-control mb-3" v-on:keyup="setImageUrl">

<div class="label">Pexels Image Search</div>
<div class="input-group">
  <input type="text" class="form-control" placeholder="e.g. mountain" v-model="query" id="query">
  <div class="input-group-append">
    <button class="btn btn-outline-secondary" type="button" id="query-search" v-on:click="searchPexels">
      <span v-if="loading">
        <span class="spinner-border spinner-border-sm" role="status" aria-hidden="true"></span> &nbsp;
      </span> Search

    </button>
  </div>
</div>

<span v-for="photo in photos">
  <img :src="photo.url" class="img-fluid mt-3 sf-thumb" v-on:click="setImage" />
</span>
</div>

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
          loading: false,
        }
    },
    methods: {
      searchPexels() {
        console.log('searching ' + this.query);
        this.photos = [];
        this.loading = true;
        let app = this;
        fetch("https://api.pexels.com/v1/search?query=" + this.query, {
            headers: {
              Authorization: '563492ad6f9170000100000193a694091f5340f3963db995f31bb5e6'
            }
          })
          .then(response => response.json())
          .then(function(result) {
            setTimeout(function(){
              app.loading = false;
            }, 1000);

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
      },
      setImageUrl(e) {
        let url = e.target.value;
        let target = this.edit_img;
        if (target.tagName == 'IMG') {
          target.src = url;
        } else {
          target.style.backgroundImage = 'url(' + url + ')';
        }
      }
    },
    mounted: function(){
      this.photos = [];
      document.body.addEventListener('keypress', function (e) {
          if (e.key === 'Enter') {
            document.querySelector('#query-search').click();
          }
      });
    }
}
</script>
