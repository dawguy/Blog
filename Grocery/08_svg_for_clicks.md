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

## Additional Learnings

Angular doesn't seem to like top level group tags (<g>) in components. The fix is to make SVG hierarchies. This is fine IMO as SVG are meant to be nested.
```html
<svg *ngFor="let shelf of shelves">
</svg>
```

## Files

Oof. Looks like we need to wrap images in a resource server.

`Not allowed to load local resource: file:///Users/david/Documents/David%20Profile%20Photo%20Github.jpg`

## SVG Nesting - Part 2

https://stackoverflow.com/questions/58927837/can-we-render-angular-components-inside-of-our-svg-templates

From experimentation:

SVG is valid in angular and can use bindings. However adding components in the mix causes breakages.

Broken HTML:
```html
<svg *ngFor="let shelf of shelves">
  <app-shelf [shelf]="shelf"></app-shelf>
</svg>
```

Valid HTML:
```html
<svg *ngFor="let shelf of shelves">
  <svg width="100px" height="100px" *ngFor="let product of shelf.items">
    <g width="100px" height="100px" >
      <image id="img-{{product.id}}"
             width="100px"
             height="100px"
             href="http://localhost:8080/IMG_4266.png"
             xlink:href="http://localhost:8080/IMG_4266.png"
             xlink:role="http://localhost:8080/IMG_4266.png"
             transform=""></image>

      <text stroke="none" id="text1" style="outline-style:none;" stroke-width="1px" text-rendering="geometricPrecision"
            font-family="Helvetica" fill="#000000" font-size="16px" x="30px" y="20px" transform="" text-anchor="middle">{{product.name}}</text>
    </g>
  </svg>
</svg>
```

Also Valid HTML. Note that we attached the component by changing the component's selector from `selector: 'app-shelf'` to `selector: '[svg-shelf]'`. By doing this we swap the selector from being applied by name to being applied by attribute (similar to `href` or other attributes). Also possible is attaching a component's selector to a class.


```html
<svg *ngFor="let shelf of shelves">
  <g svg-shelf [shelf]="shelf"></g>
</svg>
```

```ts
@Component({
  selector: '[svg-shelf]',
  templateUrl: './shelf.component.html',
  styleUrls: ['./shelf.component.css']
})
export class ShelfComponent {
```

## Namespacing

https://stackoverflow.com/a/67023077

With namespacing, <g> is a valid attribute to use as a component header.

```html
<svg:g>
    
</svg:g>
```

```html
https://stackoverflow.com/questions/37005007/angular-2-ngfor-without-wrapping-in-a-container
```
