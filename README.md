# canvas-smooth-line

## 👋 Overview
This is a free line on Canvas that I wrote using this repository.


![smooth_line](https://github.com/yokonoji/canvas-smooth-line/blob/master/images_for_readme/smooth_line.png?raw=true)


In general, The method to draw a free line on Canvas with Javascript is as follows.
```
ctx.beginPath();
ctx.moveTo(x, y);
ctx.lineTo(x, y);
ctx.closePath();
ctx.stroke();
```
However, if you draw a line with this code, you will get a bad line.

As follows:
![general_free_line](https://github.com/yokonoji/canvas-smooth-line/blob/master/images_for_readme/general_free_line.png?raw=true)

This is more noticeable the faster you draw. I wasn't happy with this quality, so I searched for a way to draw smooth lines.

## ✌ Explanation
You can try this repository as soon as you download index.html and style.css and open it in your web browser.

Although written in Vue.js, it can be applied to other projects.

Please check out the three functions.  
First, save the line trajectory as a point at each Move event.
```
onMouseMove(e) {
...
// Save line trajectories
this.points.push({
  x: e.clientX || e.touches[0].clientX,
  y: e.clientY || e.touches[0].clientY
})
...
}
```
Then connect the points with bezier-curves using the `quadraticCurveTo` method. Using the `quadraticCurveTo` method, you get a smooth line between points.
```
drawLine(points, width, color) {
  ...
  this.tmp_context.beginPath()
  this.tmp_context.moveTo(p1.x, p1.y)

  for (let i = 1; i < points.length; i++) {
    // Take the intermediate coordinates of points
    let midPoint = this.midPointBtw(p1, p2)
    // Draw bezier curves
    this.tmp_context.quadraticCurveTo(p1.x, p1.y, midPoint.x, midPoint.y)
    p1 = points[i]
    p2 = points[i + 1]
  }
  // Draw a straight line between the last point and the point immediately before it
  // Draw the continuation of the line when the next point is saved
  this.tmp_context.lineTo(p1.x, p1.y)
  this.tmp_context.stroke()
}
```
Finally, at the Mouseup event, tmp_canvas is posted to canvas. This is a trick to keep multiple lines on the canvas using the `quadraticCurveTo` method.
```
onMouseUp(e) {
  ...
  // If not an eraser
  if (this.current.tool !== 3) {
    // Transfer tmp_canvas to canvas
    this.context.drawImage(this.tmp_canvas, 0, 0)
    // Clear tmp_canvas
    this.tmp_context.clearRect(0, 0, this.tmp_canvas.width, this.tmp_canvas.height)
  }
}
```

## 👏 Reference pages
- [Exploring canvas drawing techniques](http://perfectionkills.com/exploring-canvas-drawing-techniques/#bezier-curves)
- [socketio / socket.io](https://github.com/socketio/socket.io/tree/master/examples/whiteboard)

## 🤝 License
canvas-smooth-line is under [MIT license](https://en.wikipedia.org/wiki/MIT_License).
