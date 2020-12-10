<template>
  <div id="app">
    <div class="form">
      <Basic v-show="page === 1" :page="page" @changeButton="changeButton"/>
      <Address v-show="page === 2" :page="page" @changeButton="changeButton"/>
      <Document v-show="page === 3" :page="page" @changeButton="changeButton"/>
      <div v-if="over" class="result">
        <h1>Клиент создан!</h1>

        <pre>{{ form }}</pre>
      </div>
    </div>
  </div>
</template>

<script>
import Basic from './components/Basic.vue';
import Address from './components/Address.vue';
import Document from './components/Document.vue';

export default {
  name: "App",
  components: {Basic, Address, Document},
  data() {
    return {
      page: 1,
      over: false,
      form: {},
    }
  },

  methods: {
    changeButton(button, formData, over) {
      this.form = {...this.form, ...formData}
      button === 'buttonNext' ? this.page++ : this.page--
      if(over) {
        this.over = true
      }
    }
  }
};
</script>

<style>
#app {
  font-family: "Lucida Grande", "Lucida Sans Unicode", "Lucida Sans", "DejaVu Sans",
    Verdana, sans-serif;
}
</style>
