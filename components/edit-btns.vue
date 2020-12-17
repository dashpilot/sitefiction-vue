<template>
<div v-if="editing == false" class="sf-edit-btns">
  <div class="sf-edit-btn" v-on:click="moveDown">
    <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-caret-down-fill" viewBox="0 0 16 16">
      <path d="M7.247 11.14L2.451 5.658C1.885 5.013 2.345 4 3.204 4h9.592a1 1 0 0 1 .753 1.659l-4.796 5.48a1 1 0 0 1-1.506 0z" />
    </svg>
  </div>
  <div class="sf-edit-btn" v-on:click="deleteItem">
    <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-trash" viewBox="0 0 16 16">
      <path d="M5.5 5.5A.5.5 0 0 1 6 6v6a.5.5 0 0 1-1 0V6a.5.5 0 0 1 .5-.5zm2.5 0a.5.5 0 0 1 .5.5v6a.5.5 0 0 1-1 0V6a.5.5 0 0 1 .5-.5zm3 .5a.5.5 0 0 0-1 0v6a.5.5 0 0 0 1 0V6z" />
      <path fill-rule="evenodd"
        d="M14.5 3a1 1 0 0 1-1 1H13v9a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V4h-.5a1 1 0 0 1-1-1V2a1 1 0 0 1 1-1H6a1 1 0 0 1 1-1h2a1 1 0 0 1 1 1h3.5a1 1 0 0 1 1 1v1zM4.118 4L4 4.059V13a1 1 0 0 0 1 1h6a1 1 0 0 0 1-1V4.059L11.882 4H4.118zM2.5 3V2h11v1h-11z" />
    </svg>
  </div>
</div>
</template>


<script>
module.exports = {
    props: ['editing'],
    data: function() {
        return {
          query: '',
          photos: [],
        }
    },
    methods: {
      deleteItem(e) {
        let element = e.target.closest('.editable');
        let r = confirm('Are you sure you want to delete this item?');
        if (r == true) {
          element.remove();
        }
      },
      moveDown(e) {
        let element = e.target.closest('.editable');
        if (element.nextElementSibling)
          element.parentNode.insertBefore(element.nextElementSibling, element);
      },
    },
}
</script>
