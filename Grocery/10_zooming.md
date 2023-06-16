# Zooming

https://www.petercollingridge.co.uk/tutorials/svg/interactive/pan-and-zoom/

```js
function zoom(scale) {
  for (var i = 0; i < 6; i++) {
    transformMatrix[i] *= scale;
  }
  transformMatrix[4] += (1 - scale) * centerX;
  transformMatrix[5] += (1 - scale) * centerY;

  var newMatrix = "matrix(" +  transformMatrix.join(' ') + ")";
  matrixGroup.setAttributeNS(null, "transform", newMatrix);
}
```

```js
function pan(dx, dy) {
  transformMatrix[4] += dx;
  transformMatrix[5] += dy;

  var newMatrix = "matrix(" +  transformMatrix.join(' ') + ")";
  matrixGroup.setAttributeNS(null, "transform", newMatrix);
}
```

Had a lot of trouble with this as the blog post origianlly stated that the scale only applied to the first 4 elements of the matrix.

After literal hours (probably close to 5 hard thinking hours over 2 days) I debugged this and drew up a diagram explaining why the formula `(1-scale) * centerX` is so effective.

Essentially we have two vectors that are important. The vector of the item being scaled and a vector representing the top-left corner of the camera. The issue we run into is that scaling everything in the matrix causes the center of the camera to no longer be centered.

[a c e]
[b d f]
[0 0 1]

As we're not handing any rotations b and c can be ignored/left as 0. (See https://www.youtube.com/watch?v=kYB8IZa5AuE for a mindblowing visual understanding of vectors that I didn't have a mental image for despite getting a 99% in the class back in college).

a = scaling factor of x coordinate
d = scaling factor for y coordinate
e = offset x
f = offset y

By scaling the entire matrix by a constanst value we keep all items correctly sized. But we then need to shift the camera by an offset that will maintain the original center.

After many failed diagraming attempts I finally found 1 that let me intuit why (1 - scale) * center.x worked.

<image here>

The above formula describes how far to shift the | of each point so it will line up with the X. Then scaled by the amount the original center was offset (center.x). With this value you can then do a scaling and correct the camera so it looks like it doesn't move. 

(1 - 0.125) * 500 = 437.5
(1 - .5) * 500 = 250
(1 - 2.0) * 500 = -500


```text
0.125 :: --|--            X
0.25  :: ----|----        X
0.5   :: --------|--------X
1.0   :: ---------------- |----------------
2.0   :: ---------------- X----------------|--------------------------------
4.0   :: ---------------- X------------------------------------------------|----------------------------------------------------------------
```

