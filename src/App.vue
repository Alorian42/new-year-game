<template>
  <div id="app">
    <h2>Bar Chart Example</h2>
    <!-- These are the custom components we'll create -->
    <!-- Values for `my-box` are percentages of the width of the canvas. -->
    <!-- Each bar will take up an equal space of the canvas. -->
    <my-canvas style="width: 100%; height: 600px;">
      <my-box
        v-for="(obj, index) in chartValues"
        v-bind:key="obj.id"
        :x1="((index / chartValues.length) * 100)"
        :x2="((index / chartValues.length) * 100) + (100 / chartValues.length)"
        :y1="100"
        :y2="100 - obj.val"
        :color="obj.color"
        :value="obj.val"
      >
      </my-box>
    </my-canvas>
  </div>
</template>

<script>
import MyCanvas from './MyCanvas.vue';
import MyBox from './MyBox.vue';

export default {
  name: 'app',
  components: {
    MyCanvas,
    MyBox
  },

  data () {
    return {
      chartValues: [
        {id: 0, val: 24, color: 'red'},
        {id: 1, val: 32, color: '#0f0'},
        {id: 2, val: 66, color: 'rebeccapurple'},
        {id: 3, val: 1, color: 'green'},
        {id: 4, val: 28, color: 'blue'},
        {id: 5, val: 60, color: 'rgba(150, 100, 0, 0.2)'},
      ]
    }
  },

  // Randomly selects a value to randomly increment or decrement every 16 ms.
  // Not really important, just demonstrates that reactivity still works.
  mounted () {
    let dir = 1;
    let selectedVal = Math.floor(Math.random() * this.chartValues.length);

    setInterval(() => {
      if (Math.random() > 0.995) dir *= -1;
      if (Math.random() > 0.99) selectedVal = Math.floor(Math.random() * this.chartValues.length);

      this.chartValues[selectedVal].val = Math.min(Math.max(this.chartValues[selectedVal].val + dir * 0.5, 0), 100);
    }, 16);
  }
}
</script>

<style>
html, body {
  margin: 0;
  padding: 0;
}

#app {
  position: relative;
  height: 100vh;
  width: 100vw;
  padding: 20px;
  box-sizing: border-box;
}
</style>