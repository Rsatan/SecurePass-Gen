<template>
  <div id="app">
    <SecurePass />
  </div>
</template>

<script>
import SecurePass from './components/SecurePass-Gen.vue';

export default {
  name: 'App',
  components: {
    SecurePass
  }
};
</script>

<style>
body {
  font-family: Arial, sans-serif;
  background-color: #1d1d1d;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  margin: 0;
  color: #fff;
}

#app {
  width: 100%;
  max-width: 600px;
}
</style>
