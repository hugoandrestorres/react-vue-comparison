<template>
  <div id="app">
    <header>
      <h1>Vue.js Dog Slider</h1>
      <p>A slider component that fetches random dog images from an API</p>
    </header>
    <main>
      <DogSlider />
    </main>
  </div>
</template>

<script>
import DogSlider from './components/DogSlider.vue'

export default {
  name: 'App',
  components: {
    DogSlider
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 0;
  padding: 20px;
}

header {
  margin-bottom: 30px;
}

h1 {
  color: #42b883;
  margin-bottom: 10px;
}

p {
  color: #666;
  margin-bottom: 0;
}
</style>

