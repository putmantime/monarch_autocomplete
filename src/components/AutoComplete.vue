<template>
  <div class="autocomplete" style="position:relative" v-bind:class="{'open':open}">
    <!--<div v-show="loading" ><img src="../../static/img/Infinity.gif"></div>-->
    <input v-bind:class="{'loading': loading}"
           class="form-control"
           type="text"
           v-model="value"
           v-on:input="debounceInput"
           @input="updateValue($event.target.value)"
           @keydown.enter='enter'
           @keydown.down='down'
           @keydown.up='up'
           placeholder="search..."
    >
    <div class="dropdown-menu list-group" style="width: 100%; max-height: 400px; overflow: auto; border-top-left-radius: 0">
      <ul  v-for="(suggestion, index) in suggestions"
      >
        <li @click="suggestionClick(index)"
            v-bind:class="{'active': isActive(index)}"
            v-on:mouseover="mouseOver(index)"
            class="list-group-item list-group-item-action list-group-item-light"
        >
            <strong>{{ suggestion.label[0] }}</strong><br/>
            <small>{{ suggestion.category }}</small>
            <small>{{ suggestion.id }}</small>
        </li>
      </ul>
    </div>
  </div>
</template>

<script type="text/babel">
  import axios from 'axios';
  import _ from 'lodash';

  export default {
    name: 'AutoComplete',
    data() {
      return {
        value: '',
        suggestions: [],
        open: false,
        current: 0,
        loading: false,
      };
    },
    methods: {
      debounceInput: _.debounce(
        // eslint-disable-next-line
        function () {
          if (this.value) {
            this.loading = true;
            const baseUrl = 'https://beta.monarchinitiative.org/autocomplete/{term}.json';
            const searchUrl = baseUrl.replace('{term}', this.value);
            axios.get(searchUrl)
              .then((resp) => {
                this.suggestions = resp.data;
                this.open = true;
                this.loading = false;
              })
              .catch((err) => {
                // eslint-disable-next-line
                console.log(err);
              });
          } else {
            this.suggestions = [];
          }
        }, 500, { leading: false, trailing: true }),
      updateValue(value) {
        if (this.open === false) {
          this.current = 0;
        }
        this.$emit('input', value);
      },
      // When enter pressed on the input
      enter() {
        this.value = this.suggestions[this.current].label[0];
        this.selection = this.suggestions[this.current];
        this.open = false;
        const category = this.suggestions[this.current].category;
        // eslint-disable-next-line
        const curie = this.suggestions[this.current].id;
        // eslint-disable-next-line
        window.location = 'https://monarchinitiative.org/' + category + '/' + curie;
      },
      // When up pressed while suggestions are open
      up() {
        if (this.current > 0) {
          this.current -= 1;
        }
      },
      // When down pressed while suggestions are open
      down() {
        if (this.current < this.suggestions.length - 1) {
          this.current += 1;
        }
      },
      // For highlighting element
      isActive(index) {
        return index === this.current;
      },
      mouseOver(index) {
        this.current = index;
      },
      // When one of the suggestion is clicked
      suggestionClick(index) {
        this.value = this.suggestions[index].label[0];
        this.open = false;
        // eslint-disable-next-line
        const category = this.suggestions[index].category;
        // eslint-disable-next-line
        const curie = this.suggestions[index].id;
        // eslint-disable-next-line
        window.location = 'https://monarchinitiative.org/' + category + '/' + curie;
      },
    },
    watch: {
      // eslint-disable-next-line
      value: function () {
        if (!this.value) {
          this.open = false;
        }
      },
    },
  };
</script>

<style scoped>
  html, body {
    height: 100%;
  }

  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    display: flex;
    justify-content: center;
    padding-top: 100px;
    height: 100%;
  }

  ul {
    list-style-type: none;
    padding: 0;
  }

  li {
    margin: 0 10px;
  }

  input {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
  }

  .autocomplete-input {
    position: relative;
    height: 300px;
  }
  .loading {
    background-color: #ffffff;
    background-image: url("static/img/Infinity.gif");
    background-size: 25px 25px;
    background-position: 99%;
    background-repeat: no-repeat;
  }
</style>
