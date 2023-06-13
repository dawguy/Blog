# SVG For Click Listeners

Working as hoped, clicking anywhere on the filled shape causes testRequest to fire. Clicking outside the filled shape does nothing.
```html
<svg xmlns="http://www.w3.org/2000/svg" version="1.1" baseProfile="full" width="30px" height="30px" viewBox="0 0 30 30" preserveAspectRatio="xMidYMid meet" style="zoom: 1;">
  <g (click)="testRequest()" id="main_group">
  <polygon points="0,0 30,0 15,30 0,0" stroke="#000000" stroke-width="3px" transform=""></polygon>
</g>
</svg>
```

Clicking anywhere within the 30 x 30 viewBox causes testReqeuest to be fired.
```html
<svg (click)="testRequest()" xmlns="http://www.w3.org/2000/svg" version="1.1" baseProfile="full" width="30px" height="30px" viewBox="0 0 30 30" preserveAspectRatio="xMidYMid meet" style="zoom: 1;">
  <g id="main_group">
    <polygon points="0,0 30,0 15,30 0,0" stroke="#000000" id="polyline1" stroke-width="3px" fill="none" transform=""></polygon>
  </g>
</svg>
```

Clicking only on the line itself causes the testReqest to be fired. Anywhere else (including inside the shape doesn't). One not is that the key here is that `fill="none"` is the key for why this happens. Polygon vs polyline are primarily about whether or not the shape auto connects to the final point.
```html
<svg xmlns="http://www.w3.org/2000/svg" version="1.1" baseProfile="full" width="30px" height="30px" viewBox="0 0 30 30" preserveAspectRatio="xMidYMid meet" style="zoom: 1;">
  <g id="main_group" (click)="testRequest()">
    <polyline points="0,0 30,0 15,30 0,0" stroke="#000000" id="polyline1" stroke-width="3px" fill="none" transform=""></polyline>
  </g>
</svg>
```

## Using This In The Wild
Will need to do some experimentation, but I'm fully expecting to be able to manage the entirety of what I'm try to do with SVG's + SVG <g> (group) tags! If I hit performance issues I'll make another post.
