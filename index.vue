<template>
  <div class="wrapper">
    <h2>hello,ZZQ</h2>
    <img src="./images/ball1.png" class="ballImage" id="ball1">
    <img src="./images/ball2.png" class="ballImage" id="ball2">
    <img src="./images/ball3.png" class="ballImage" id="ball3">
    <img src="./images/ball4.png" class="ballImage" id="ball4">
    <img src="./images/ball5.png" class="ballImage" id="ball5">
    <canvas id="myCanvas" width="1000" height="600">

    </canvas>
  </div>
</template>
<script>
require("./utils.js");
require("./ball.js");

export default {
  data() {
    return {
      balls: []
    };
  },
  methods: {},
  mounted() {
    let targets = {
      ball1: { x: 112.5, y: 225, url: "" },
      ball2: { x: 337.5, y: 225, url: "" },
      ball3: { x: 562.5, y: 225, url: "" },
      ball4: { x: 787.5, y: 225, url: "" }
    };
    let balls = [];
    let animationId = "";

    // let ballImage = document.getElementById("ball1");

    let myCanvas = document.getElementById("myCanvas"),
      context = myCanvas.getContext("2d"),
      mouse = utils.captureMouse(myCanvas),
      // ball = new Ball(60, "#ff0000", ballImage),
      easing = 0.05;

    for (var ball, i = 1; i <= 4; i++) {
      let ballImage = document.getElementById("ball" + i);

      // ball = new Ball(60, "", ballImage);
      ball = new Ball(60);
      ball.id = "ball" + i;
      ball.backImg = ballImage;
      ball.x = Math.random() * myCanvas.width;
      ball.y = Math.random() * myCanvas.height;
      ball.vx = Math.random() * 2 - 1;
      ball.vy = Math.random() * 2 - 1;
      balls.push(ball);
    }

    function onMouseenter(e) {
      // remove
      cancelRequestAnimationFrame(animationId);
      for (var key in targets) {
        console.log(key);
      }
    }
    myCanvas.addEventListener("mouseenter", onMouseenter, false);
    myCanvas.addEventListener("mouseout", function(e) {
      // alert("out");
      // myCanvas.removeEventListener("mouseenter", onMouseenter, false);
      // 要重新设置vx，vy
      animationId = window.requestAnimationFrame(drawFrame, myCanvas);
    });
    // var vx = Math.random() * 10,
    //   vy = Math.random() * 10;
    // ball
    function Ball(radius = 40, color = "#ff0000", backImg = "") {
      this.x = 0;
      this.y = 0;
      this.radius = radius;
      this.vx = 0;
      this.vy = 0;
      this.rotation = 0;
      this.scaleX = 1;
      this.scaleY = 1;
      this.color = utils.parseColor(color);
      this.lineWidth = 1;
      // debugger;
      this.backImg = backImg;
    }

    Ball.prototype.draw = function(context) {
      context.save();
      context.translate(this.x, this.y);
      context.rotate(this.rotation);
      context.scale(this.scaleX, this.scaleY);
      if (this.backImg) {
        // url,x,y,width,height;image is a rectangle,so x is left-top corner
        context.drawImage(this.backImg, -50, -50, 100, 100); // add Image
      } else {
        context.lineWidth = this.lineWidth;
        context.fillStyle = this.color;
        context.beginPath();
        //x, y, radius, start_angle, end_angle, anti-clockwise
        context.arc(0, 0, this.radius, 0, Math.PI * 2, true);
        context.closePath();

        context.fill();
        if (this.lineWidth > 0) {
          context.stroke();
        }
      }

      context.restore();
    };

    Ball.prototype.getBounds = function() {
      return {
        x: this.x - this.radius,
        y: this.y - this.radius,
        width: this.radius * 2,
        height: this.radius * 2
      };
    };

    //
    function drawFrame() {
      animationId = window.requestAnimationFrame(drawFrame, myCanvas);
      context.clearRect(0, 0, myCanvas.width, myCanvas.height);

      //
      var left = 0,
        right = myCanvas.width,
        top = 0,
        bottom = myCanvas.height;
      // var vx = (mouse.x - ball.x) * easing,
      //   vy = (mouse.y - ball.y) * easing;

      // ball.x += vx;
      // ball.y += vy;
      // ball.draw(context);

      for (let i in balls) {
        let ball = balls[i];
        balls[i].x += balls[i].vx;
        balls[i].y += balls[i].vy;

        if (
          ball.x - ball.radius > myCanvas.width ||
          ball.x + ball.radius < 0 ||
          ball.y - ball.radius > myCanvas.height ||
          ball.y + ball.radius < 0
        ) {
          // balls.splice(pos, 1); //remove ball from array
          ball.vx *= -1;
          ball.vy *= -1;
        }
        balls[i].draw(context);
      }
    }
    drawFrame();
  }
};
</script>
<style lang="scss" scoped>
// @import url("./style.css");
@import "./style.css";
#myCanvas {
  background: url("./images/background.png");
}
.ballImage {
  display: none;
}
</style>
