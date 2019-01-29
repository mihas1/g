<template>
  <div class="pagination">
    <span
      v-for="page in pagesTotal"
      :key="page"
      v-bind:class="(page - 1) === currPage ? 'active' : ''"
      v-on:click="toPage(page - 1)"
    >
      {{page}}
    </span>
  </div>
</template>

<script>
  export default {
    name: 'Pagination',
    props: {
      currPage: Number,
      pagesTotal: Number,
    },
    methods: {
      toPage(page) {
        this.$emit('change-page', page);
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .pagination > span {
    display: inline-block;
    cursor: pointer;
    padding: 5px;
  }

  .pagination .active {
    font-weight: bold;
  }
</style>