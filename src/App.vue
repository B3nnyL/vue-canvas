<template>
  <div id="app" class="uk-container">
    <h1>Canvas</h1>
   <canvas id="canvas" v-on:mousedown="handleMouseDown" v-on:mouseup="handleMouseUp" v-on:mousemove="handleMouseMove" width="900px" height="600px"></canvas>
   <div class="currentStatus">
     <ul>
       <li class="colorBlock" :style="{background:currentColor}"></li>
       <li>{{currentTool}}</li>
       <li>{{currentLineWidth}}</li>
     </ul>
   </div>
   <div class="colorPlatte">
     <ul>
      <li v-for="color in colorPalette">
        <div class="colorBlock" :style="{background: color}" v-on:mousedown="pickColor(color)"></div>
      </li>
     </ul>
   </div>
   <div class="toolBox">
     <ul>
       <li v-for="tool in toolBox"><button class="uk-button uk-button-default"v-on:mousedown="pickTool(tool)">{{tool.name}}</button></li>
     </ul>
   </div>
   <div class="util">
     <ul>
       <li><a v-on:mousedown="saveToPNG()" v-bind:href="downloadHref" download="myDoodle.png">Save</a></li>
       <li><a v-on:mousedown="clearCanvas()" href="">Clear</a></li>
       <li><a v-on:mousedown="sendToSlack()">Send to Slack</a></li>
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
      toolBox: {
        pen: {
          name:"pen",
          color: "#fff",
          width: 2
        },
        eraser: {
          name: "eraser",
          color: "#000",
          width: 10
        }
      },
      currentColor: "FFF",
      currentLineWidth: 2,
      currentTool: "pen",
      c: false,
      ctx: false,
      downloadHref: ''
    }
  },
  methods: {
    pickColor: function(color) {
      this.currentColor = color;
    },
    pickTool: function(tool){
      this.currentTool = tool.name
      this.currentColor = tool.color;
      this.currentLineWidth = tool.width;
    },
    draw: function (ctx, event) {
     if (this.mouse.down) {
       ctx.beginPath();
       ctx.moveTo(this.mouse.previous.x, this.mouse.previous.y);
       ctx.lineTo(this.mouse.current.x, this.mouse.current.y);
       ctx.strokeStyle = this.currentColor;
       ctx.lineWidth = this.currentLineWidth;
       ctx.stroke();
       ctx.closePath();
     } 
    },
    handleMouseDown: function (event) {
      this.mouse.down = true;
      this.mouse.previous = {
         x: this.mouse.current.x,
         y: this.mouse.current.y
      }
      this.mouse.current = {
        x: event.clientX - this.c.offsetLeft,
        y: event.clientY - this.c.offsetTop
      }
    },
    handleMouseUp: function () {
      this.mouse.down = false;
    },
    handleMouseMove: function (event) {
      this.mouse.previous = {
        x: this.mouse.current.x,
        y: this.mouse.current.y
      }
      this.mouse.current = {
       x: event.clientX - this.c.offsetLeft,
       y: event.clientY - this.c.offsetTop
      }
      this.draw(this.ctx, event)
    },
    clearCanvas: function(){
      this.ctx.clearRect(0, 0, this.c.width, this.c.height);
    },
    fillUpCanvas: function () {
      this.ctx.fillStyle = "#000";
      this.ctx.fillRect(0, 0, this.c.width, this.c.height);
    },
    saveToPNG: function(){
      var image = this.c.toDataURL("image/png").replace("image/png", "image/octet-stream"); //Convert image to 'octet-stream' (Just a download, really)
      this.downloadHref = image;
    },
    sendToSlack: function() {
      var data = this.ctx.getImageData(0, 0, this.c.width, this.c.height);
      console.log(data);
      console.log(JSON.stringify(data));
    }
  },
  mounted: function () {
    this.c = document.getElementById("canvas");
    this.ctx = this.c.getContext("2d");
    this.ctx.translate(0.1, 0.1);
    this.ctx.imageSmoothingEnabled= false;
    this.ctx.strokeStyle = "FFF";
    this.fillUpCanvas();
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
  margin-top: 50px;
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
canvas {
  background:black;
  box-shadow: 0 2px 3px rgba(0,0,0,0.2)
}
.colorBlock {
  width: 15px;
  height: 15px;
  border: 1px dotted black;
}

.currentStatus {
  margin-top: 20px;
}
</style>
