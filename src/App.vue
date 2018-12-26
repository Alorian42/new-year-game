<template>
  <div id="app" @keyup.37="move('left')" @keyup.39="move('right')">
    <div v-if="!isAlive">
      <input type="text" v-model="nickname">
      <button @click="startGame">Start Game</button>
      <button @click="loadBoards">Leader Boards</button>
      <ol>
        <li v-for="leader in leaders" v-bind:key="leader.id">{{ leader.name }} : {{ leader.score }}</li>
      </ol>
    </div>
    <div v-if="isAlive">
      Life: {{ lifes }}
      Score: {{ score }}
    </div>
    <my-canvas style="width: 100%; height: 100%;" v-if="isAlive">
      <tile
        :map="map"
        :frame="frame"
      >
      </tile>
      <wisp
        :frame="frame"
        :wisps="wisps"
      >
      </wisp>
      <player
        :frame="frame"
        :x="playerX"
        :y="playerY">
      </player>
      <projectiles
        :frame="frame"
        :projectiles="projectiles">
      </projectiles>
    </my-canvas>
  </div>
</template>

<script>
import MyCanvas from './MyCanvas.vue';
import Tile from './Tile.vue';
import Wisp from './Wisp.vue';
import Player from './Player.vue';
import Projectiles from './Projectiles.vue';

export default {
  name: 'app',
  components: {
    MyCanvas,
    Tile,
    Wisp,
    Player,
    Projectiles
  },

  data () {
    return {
      leaders: [],
      nickname: 'Player',
      lifes: 30,
      score: 0,
      isAlive: false,
      playerX: 250,
      playerY: 650,
      playerSpeed: 15,
      frame: 0,
      map: [
            [1,1,1,1,1,1,1,1,1,],
            [1,1,1,1,1,1,1,1,1,],
            [0,0,0,0,0,0,0,0,0,],
            [0,0,0,0,0,0,0,0,0,],
            [0,0,0,0,0,0,0,0,0,],
            [0,0,0,0,0,0,0,0,0,],
            [0,0,0,0,0,0,0,0,0,],
            [0,0,0,0,0,0,0,0,0,],
            [0,0,0,0,0,0,0,0,0,],
            [2,2,2,2,2,2,2,2,2,],
            [0,0,0,0,0,0,0,0,0,],
            [0,0,0,0,0,0,0,0,0,],
      ],
      wisps: [],
      wispId: 0,
      maxAddSpeed: 1,
      projectiles: [],
      cooldown: 0,
      projectileId: 0,
      barricadeRange: 550,
      playtime: 0,
      justStarted: true,
    }
  },

  mounted () {
    document.addEventListener('keydown', (event) => {
      const keyName = event.key;

      if (keyName === 'ArrowLeft') {
        event.preventDefault();
        this.move('left');
        return;
      }
      if (keyName === 'ArrowRight') {
        event.preventDefault();
        this.move('right');
        return;
      }
      if (keyName === ' ') {
        event.preventDefault();
        this.shoot();
        return;
      }
    });

    setInterval(this.gameLoop, 16);
  },
  methods: {
    loadBoards: function() {
      fetch('https://risens.team/risensteam/ny_game/leader.php?get_leaders')
            .then((response) => {
                if(response.ok) {
                    return response.json();
                }
            
                throw new Error('Network response was not ok');
            })
            .then((json) => {
              this.leaders = [];
              json.forEach(element => {
                this.leaders.push({
                  id: element.id,
                  name: element.name,
                  score: element.score,
                })
              });
            })
            .catch((error) => {
                console.log(error);
            });
    },
    startGame: function() {
      this.score = 0;
      this.lifes = 30;
      this.wisps = [];
      this.projectiles = [];
      this.wispId = 0;
      this.projectileId = 0;
      this.playerX = 250;
      this.playerY = 650;
      this.maxAddSpeed = 1;
      this.cooldown = 0;
      this.playtime = 0;
      this.isAlive = true;
      this.justStarted = true;
    },
    loseGame: function() {
      this.isAlive = false;
      fetch('https://risens.team/risensteam/ny_game/leader.php?new_leader_score=' + this.score + '&new_leader_name=' + this.nickname)
            .then((response) => {
                if(response.ok) {
                    return response.json();
                }
            
                throw new Error('Network response was not ok');
            })
            .then((json) => {
              this.loadBoards();
            })
            .catch((error) => {
                console.log(error);
            });
    },
    gameLoop: function() {
      if (this.isAlive) {
        if (this.lifes <= 0) {
          this.loseGame();
          return;
        }
        this.projectileCycler();
        this.wispCycler();
        if (this.frame == 60) {
          this.frame = 0;
          this.playtime++;
        }
        if (this.cooldown !== 0) {
          this.cooldown--;
        }
        if (this.playtime % 10 === 0 && this.frame === 0 && !this.justStarted) {
          this.maxAddSpeed += Math.floor(Math.random() * 2);
        }

        this.frame++;
        this.justStarted = false;
      }
    },
    wispCycler: function() {
      if (this.frame == 60) {
        this.wispSpawner();
      }
      this.wisps.forEach(element => {
        element.y += element.speed;
        
        if (element.y >= this.barricadeRange) {
          this.wispKiller(element.id);
        }

        if (this.frame % 15 == 0) {
        element.animationFrame++;
          if (element.animationFrame === 4) {
            element.animationFrame = 0;
          }
        }
      });
    },
    wispSpawner: function() {
      let y = 25;
      let x = Math.floor(Math.random() * 500) + 10;
      this.wisps.push({
        id: this.wispId,
        x: x,
        y: y,
        speed: Math.floor(Math.random() * 2) + this.maxAddSpeed,
        animationFrame: Math.floor(Math.random() * 4),
      });
      this.wispId++;
    },
    wispKiller: function(id, byPlayer = false) {
      if (!byPlayer) {
        this.lifes--;
        let audio = new Audio('assets/explosion_2.mp3');
        audio.play();
      }
      else {
        this.score += 5;
        let audio = new Audio('assets/explosion.mp3');
        audio.play();
      }
      for(let i = 0; i < this.wisps.length; i++) {
        if (this.wisps[i].id === id) {
          this.wisps.splice(i, 1);
          break;
        }
      }
    },
    move: function(dir) {
      if (dir === 'left' && this.playerX - 5 >= 0 && this.isAlive) {
        this.playerX -= this.playerSpeed;
      }
      else if (dir === 'right' && this.playerX + 5 <= 510 && this.isAlive) {
        this.playerX += this.playerSpeed;
      }
    },
    shoot: function() {
      if (this.cooldown === 0 && this.isAlive) {
        this.cooldown = 30;
        this.projectileSpawner();
      }
    },
    projectileCycler: function() {
      this.projectiles.forEach(element => {
        element.y += element.speed;

        if (this.frame % 15 == 0) {
        element.animationFrame++;
          if (element.animationFrame === 4) {
            element.animationFrame = 0;
          }
        }

        if (element.y < 0) {
          this.projectileKiller(element.id);
        }
        else {
          this.projectileCollisionWithWisps(element);
        }
      });
    },
    projectileSpawner: function() {
      let audio = new Audio('assets/fireball.mp3');
      audio.play();
      let y = this.playerY - 10;
      let x = this.playerX;
      this.projectiles.push({
        id: this.projectileId,
        x: x,
        y: y,
        speed: -10,
        animationFrame: 0,
      });
      this.projectileId++;
    },
    projectileKiller: function(id) {
      for(let i = 0; i < this.projectiles.length; i++) {
        if (this.projectiles[i].id === id) {
          this.projectiles.splice(i, 1);
          break;
        }
      }
    },
    projectileCollisionWithWisps: function(projectile) {
      for(let i = 0; i < this.wisps.length; i++) {
        let wisp = this.wisps[i];

        if (wisp.x - 32 < projectile.x && wisp.x + 32 > projectile.x) {
          if (wisp.y - 32 < projectile.y && wisp.y + 32 > projectile.y) {
            this.projectileKiller(projectile.id);
            this.wispKiller(wisp.id, true);
            return;
          }
        }
      }
    }
  },

  computed: {

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