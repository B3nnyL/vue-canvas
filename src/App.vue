<template>
  <div id="app">
    <h1>Canvas</h1>
   <canvas id="canvas" v-on:mousedown="handleMouseDown" v-on:mouseup="handleMouseUp" v-on:mousemove="handleMouseMove" width="900px" height="600px"></canvas>
   <div class="colorPlatte">
     <ul>
      <li v-for="color in colorPalette">
        <div class="colorBlock" :style="{background: color}" v-on:mousedown="pickColor(color)"></div>
        {{color}}
      </li>
     </ul>
   </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  data: function () {
    return {
      mouse: {
        current: {
          x: 0,
          y: 0
        },
        previous: {
          x: 0,
          y: 0
        },
        down: false
      },
      colorPalette: {
        white: "#fff",
        silver: "#c0c0c0",
        grey: "#808080",
        black: "#000",
        red: "#ff0000",
        maroon: "#800000",
        yellow: "#ffff00",
        olive: "#808000",
        lime: "#00ff00",
        green: "#008000",
        aqua: "#00ffff",
        teal: "#008080",
        blue: "#0000ff",
        navy: "#000080",
        fuchsia: "#ff00ff",
        purple: "#800080" 
      },
      currentColor: "FFF"
    }
  },
  computed: {
    currentMouse: function () {
      var c = document.getElementById("canvas");
      var rect = c.getBoundingClientRect();
      
      return {
        x: this.mouse.current.x - rect.left,
        y: this.mouse.current.y - rect.top
      }
    }
  },
  methods: {
    pickColor: function(color) {
      this.currentColor = color;
    },
    draw: function (event) {
     if (this.mouse.down ) {
       var c = document.getElementById("canvas");
       var ctx = c.getContext("2d");
       ctx.clearRect(0,0,900,600);
       ctx.lineTo(this.currentMouse.x, this.currentMouse.y);
       ctx.strokeStyle = this.currentColor;
       console.log(ctx.strokeStyle);
       ctx.lineWidth = 2;
       ctx.stroke()
     } 
    },
    handleMouseDown: function (event) {
      this.mouse.down = true;
      this.mouse.current = {
        x: event.pageX,
        y: event.pageY
      }
      var c = document.getElementById("canvas");
      var ctx = c.getContext("2d");
      ctx.moveTo(this.currentMouse.x, this.currentMouse.y)
    },
    handleMouseUp: function () {
      this.mouse.down = false;
    },
    handleMouseMove: function (event) {
      this.mouse.current = {
        x: event.pageX,
        y: event.pageY
      }
      this.draw(event)
    }
  },
  mounted: function () {
    var c = document.getElementById("canvas");
    var ctx = c.getContext("2d");
    ctx.translate(0.1, 0.1);
    ctx.imageSmoothingEnabled= false;
    ctx.strokeStyle = "FFF";
  }
}
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
canvas{
  background:black;
  box-shadow: 0 2px 3px rgba(0,0,0,0.2)
}
.colorBlock{
  width: 15px;
  height: 15px;
  border: 1px;
  border-color: black;
}
</style>
