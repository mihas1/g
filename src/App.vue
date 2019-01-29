<template>
  <div id="app">
    <Table v-bind:url="table.url"/>
  </div>
</template>

<script>
import Table from './components/Table.vue'

export default {
  name: 'app',
  data: function () {
    return {
      table: {
        url: './initialData.json'
      }
    }
  },
  components: {
    Table
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
</style>
