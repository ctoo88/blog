## HTML5 Canvas: 贝塞尔曲线

2017/06/23

### 背景资料

>[Bézier curve](https://en.wikipedia.org/wiki/B%C3%A9zier_curve) [De Casteljau's algorithm](https://en.wikipedia.org/wiki/De_Casteljau%27s_algorithm) [Bernstein polynomial](https://en.wikipedia.org/wiki/Bernstein_polynomial)

### HTML中的用法

#### 二次贝塞尔曲线

方法：quadraticCurveTo(cx, cy, x, y)

![quadraticCurve](./res/quadraticCurve.png)

    moveTo(20, 20); // 开始点
    quadraticCurveTo(20, 100, 200, 20); // 控制点&结束点

简单应用：波浪线

![wave](./res/wave.png)

    <canvas id="myCanvas" width="320" height="200"></canvas>
    <script>
      var c = document.getElementById("myCanvas")
        , ctx = c.getContext("2d");

      ctx.beginPath();
      ctx.moveTo(20, 80); // 曲线1起点
      ctx.quadraticCurveTo(70, 140, 120, 80);
      ctx.strokeStyle = "red";
      ctx.stroke();

      ctx.beginPath();
      ctx.moveTo(120, 80); // 曲线2起点
      ctx.quadraticCurveTo(170, 20, 220, 80);
      ctx.strokeStyle = "blue";
      ctx.stroke();
    </script>

#### 三次贝塞尔曲线

方法：bezierCurveTo(cx1, cy1, cx2, cy2, x, y)

![bezierCurve](./res/bezierCurve.png)

    moveTo(20, 20); // 开始点
    bezierCurveTo(110, 143, 165, 47, 105, 20); // 控制点1&控制点2&结束点

简单应用：心形

![heart](./res/heart.png)

    <canvas id="myCanvas" width="320" height="200"></canvas>
    <script>
      var c = document.getElementById("myCanvas");
        , ctx = c.getContext("2d");

      ctx.beginPath();
      ctx.moveTo(166, 164); // 曲线1起点
      ctx.bezierCurveTo(27, 87, 131, 16, 166, 65);
      ctx.strokeStyle = "blue";
      ctx.stroke();

      ctx.beginPath();
      ctx.moveTo(166, 164); // 曲线2起点
      ctx.bezierCurveTo(305, 87, 201, 16, 166, 65);
      ctx.strokeStyle = "red";
      ctx.stroke();
    </script>

#### 综合应用

采用CANVAS动画做一个通用loading页面。

[演示DEMO](./loading.html)
