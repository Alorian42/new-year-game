<template>
  <div id="app" @keyup.37="move('left')" @keyup.39="move('right')">
    <my-canvas style="width: 100%; height: 100%;">
      <tile
        :map="map"
      >
      </tile>
      <wisp
        :x="playerX"
        :y="playerY"
      >
      </wisp>
    </my-canvas>
  </div>
</template>

<script>
import MyCanvas from './MyCanvas.vue';
import Tile from './Tile.vue';
import Wisp from './Wisp.vue';

export default {
  name: 'app',
  components: {
    MyCanvas,
    Tile,
    Wisp
  },

  data () {
    return {
      playerX: 100,
      playerY: 200
    }
  },

  // Randomly selects a value to randomly increment or decrement every 16 ms.
  // Not really important, just demonstrates that reactivity still works.
  mounted () {
    /*setInterval(function() {
      this.playerX += 5;
    }, 100);*/
  },
  methods: {
    move: function(dir) {
      console.log(dir);
      if (dir === 'left') {
        this.playerX -= 5;
      }
      else if (dir === 'right') {
        this.playerX += 5;
      }
    }
  },

  computed: {
    map: function() {
      const availTiles = [0, 1, 2];
      const len = 18;

      let arrays = [
        [1,1,1,1,1,1,1,1,1,],
        [1,1,1,1,1,1,1,1,1,],
        [0,0,0,0,0,0,0,0,0,],
        [0,0,0,0,0,0,0,0,0,],
        [0,0,0,0,0,0,0,0,0,],
        [0,0,0,0,0,0,0,0,0,],
        [0,0,0,0,0,0,0,0,0,],
        [2,2,2,2,2,2,2,2,2,],
        [2,2,2,2,2,2,2,2,2,],
        [0,0,0,0,0,0,0,0,0,],
      ];
      
      return arrays;
    }
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
  padding: 0px;
}
</style>