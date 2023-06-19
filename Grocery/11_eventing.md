# Eventing

https://angular.io/guide/glossary#lifecycle-hook

To get the SVG element, ngAfterViewInit.


https://dev.to/netsi1964/screen-coordinates-to-svg-coordinates-3k0l

To get the screen -> SVG coordinates working.

# Learnings

Overall this was pretty straight forward. Dragging, min drag distance, clicking.

One thing I did learn was @Output. You can use the event name from the parent component which is pretty nice. I chose to have the selected product live on the planogram.


# Oddity

```js
this.availableQuantities = Array(10).map((_, i) => i + 1); // Empty array of 10 elements
this.availableWeights = Array.from(Array(10).keys()).map((_, i) => (i + 1) / 2); // Works as expected.
```

No clue why the first one doesn't work...

# CSS

Conditional classes:
https://stackoverflow.com/questions/35269179/angular-conditional-class-with-ngclass

## Where I'm Leaving It

Right now the buttons all work and do stuff. The navigation works.

Something that I do want to keep in mind will be listed in blog post 12 as they'll be my todo list of things that I believe are incredbily important, but not necessarily needed next as I want to implement category and planogram expansion/contraction navigation as a priority.
