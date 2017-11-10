<template>
  <div id="app" class="container-fluid">
    <div class="col-md-2"></div>
    <div class="col-md-8">
      <h2>Monarch Autocomplete</h2>
      <autocomplete v-model="selection"></autocomplete>
    </div>
    <div class="col-md-2"></div>
  </div>
</template>

<script>
  import Autocomplete from './components/AutoComplete';

  export default {
    name: 'app',
    components: {
      autocomplete: Autocomplete,
    },
    data() {
      return {
        selection: '',
      };
    },
  };
</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }
</style>
