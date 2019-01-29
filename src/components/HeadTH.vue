<template>
  <td
    v-bind:class="itemKey === sorting.active ? 'active ' + sorting.dir : ''"
  >
    <div>
      <input
        type="text"
        v-bind:placeholder="item"
        v-on:input="filter(itemKey, $event)"
      >
    </div>
    <div
      class="sortBtn"
      v-on:click="sort(itemKey)"
    >
      &nbsp;
    </div>
  </td>
</template>

<script>
  export default {
    name: 'HeadTH',
    props: {
      sorting: Object,
      item: String,
      itemKey: String
    },
    methods: {
      filter(itemKey, event) {
        this.$emit('filter', itemKey, event);
      },
      sort(itemKey) {
        this.$emit('sort', itemKey);
      }
    }
  }
</script>

<style>
  .sortBtn {
    content: '';
    position: absolute;
    width: 40px;
    height: 10px;
    right: 0;
    top: 50%;
    transform: translateY(-50%);
  }

  .sortBtn::before {
    content: '';
    width: 0;
    height: 0;
    transform: translateY(-50%);
    border-style: solid;
    position: absolute;
    right: 10px;
    top: 50%;
    opacity: 0.3;
    transition: opacity .3s;
    border-width: 6px 6px 0 6px;
    border-color: #007bff transparent transparent transparent;
  }

  .sortBtn:hover::before {
    opacity: 0.5;
  }

  .active .sortBtn::before {
    opacity: 1;
  }

  .desc .sortBtn::before {
    border-width: 6px 6px 0 6px;
    border-color: #007bff transparent transparent transparent;
  }

  .asc .sortBtn::before {
    border-width: 0 6px 6px 6px;
    border-color: transparent transparent #007bff transparent;
  }
</style>