<template>
  <div class="app">
    <h1>🇷🇼 Rwandan Museum Puzzle Game</h1>

    <h3>Level {{ index + 1 }} / {{ puzzles.length }}</h3>

    <p><b>Moves:</b> {{ moves }}</p>

    <!-- TRY AGAIN BUTTON -->
    <button v-if="!won" @click="resetGame" class="try-again">🔄 Try Again</button>

    <!-- PUZZLE GRID -->
    <div class="grid">
      <div
        v-for="(tile, i) in tiles"
        :key="i"
        class="tile"
        @click="move(i)"
      >
        <div
          v-if="tile !== 'empty'"
          class="img"
          :style="getTileStyle(tile)"
        ></div>
        <span v-if="tile !== 'empty'" class="tile-number">{{ tile }}</span>
      </div>
    </div>

    <!-- WIN PANEL -->
    <div v-if="won" class="reward">
      <h2>🎉 Puzzle Completed!</h2>

      <p><b>Moves:</b> {{ moves }}</p>
      <p class="fact">{{ currentPuzzle.fact }}</p>

      <div class="buttons">
        <button @click="nextPuzzle">➡️ Next Puzzle</button>
        <button @click="resetGame">🔁 Replay</button>
      </div>
    </div>

    <!-- SOUND -->
    <audio ref="winSound">
      <source src="/sound/win.mp3" type="audio/mp3" />
    </audio>

    <audio ref="moveSound">
      <source src="/sound/move.mp3" type="audio/mp3" />
    </audio>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tiles: [],
      moves: 0,
      won: false,
      index: 0,

      // 🔥 10 puzzles (you can add more images)
      puzzles: [
        {
          image: "/images/rwabugiri.webp",
          fact: "This leader contributed to Rwanda’s unity and peacebuilding."
        },
        {
          image: "/images/yuhi.webp",
          fact: "Known for improving education systems in Rwanda."
        },
        {
          image: "/images/",
          fact: "A cultural figure who preserved Rwandan heritage."
        },
        {
          image: "/images/hero4.jpg",
          fact: "Important figure in Rwanda’s historical development."
        }
      ]
    };
  },

  computed: {
    currentPuzzle() {
      return this.puzzles[this.index];
    }
  },

  mounted() {
    this.resetGame();
  },

  methods: {
    // 🎮 reset puzzle
    resetGame() {
      this.tiles = [1,2,3,4,5,6,7,8,"empty"];
      this.shuffle();
      this.moves = 0;
      this.won = false;
    },

    // 🔀 shuffle tiles
    shuffle() {
      for (let i = this.tiles.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [this.tiles[i], this.tiles[j]] = [this.tiles[j], this.tiles[i]];
      }
    },

    // 🎯 movement logic
    move(i) {
      const empty = this.tiles.indexOf("empty");

      const valid = [empty - 1, empty + 1, empty - 3, empty + 3];

      if (valid.includes(i)) {
        [this.tiles[i], this.tiles[empty]] =
        [this.tiles[empty], this.tiles[i]];

        this.moves++;

        this.$refs.moveSound?.play();

        this.checkWin();
      }
    },

    // 🏆 win check
    checkWin() {
      const win = [1,2,3,4,5,6,7,8,"empty"];

      if (JSON.stringify(this.tiles) === JSON.stringify(win)) {
        this.won = true;
        this.$refs.winSound?.play();
      }
    },

    // ➡️ next puzzle (NO storage, just in-memory)
    nextPuzzle() {
      this.index = (this.index + 1) % this.puzzles.length;
      this.resetGame();
    },

    // 🖼️ image slicing effect per tile
    getTileStyle(tile) {
      const size = 100; // tile size

      const positions = {
        1: "0 0",
        2: "-100px 0",
        3: "-200px 0",
        4: "0 -100px",
        5: "-100px -100px",
        6: "-200px -100px",
        7: "0 -200px",
        8: "-100px -200px"
      };

      return {
        width: size + "px",
        height: size + "px",
        backgroundImage: `url(${this.currentPuzzle.image})`,
        backgroundSize: "300px 300px",
        backgroundPosition: positions[tile] || "0 0"
      };
    }
  }
};
</script>

<style>
.app {
  text-align: center;
  font-family: Arial;
  background: #8a7575;
  min-height: 100vh;
  padding: 20px;
  color: #ffffff;
}

.try-again {
  margin: 15px 0;
  padding: 10px 20px;
  font-size: 16px;
  background: #6d5d5d;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.try-again:hover {
  background: #ff5252;
}

.grid {
  display: grid;
  grid-template-columns: repeat(3, 100px);
  grid-template-rows: repeat(3, 100px);
  gap: 3px;
  justify-content: center;
  margin-top: 20px;
}

.tile {
  width: 100px;
  height: 100px;
  background: #ddd;
  border: 2px solid #333;
  cursor: pointer;
  overflow: hidden;
  position: relative;
}

.img {
  width: 100%;
  height: 100%;
}

.tile-number {
  position: absolute;
  top: 5px;
  left: 5px;
  font-size: 18px;
  font-weight: bold;
  color: white;
  text-shadow: 1px 1px 2px black;
  pointer-events: none;
}

.reward {
  margin-top: 20px;
  padding: 20px;
  background: #d4f5d4;
  display: inline-block;
  border-radius: 10px;
}

.fact {
  margin-top: 10px;
  font-weight: bold;
}

button {
  margin: 5px;
  padding: 10px;
  cursor: pointer;
}

.buttons {
  margin-top: 10px;
}
</style>