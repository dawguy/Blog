# Professionalizing The Blog

Current status has empty blog pages and a non-working mobile layout. Also it doesn't look particularly pretty. What we're going to need to do next is prettify the blog and get it actually loading some posts.

I think this will be fairly straight-forward.

## Requests on every page

First big thing that has popped up is the right bar makes recent requests for both projects and blogs. I really want this to occur only once, and for the remaining pages to remember the results (At least when using [routerLink])

This was resolved by moving the router-outlet to app-component.html.

## Looking at other blogs

(Mostly taken from jvns.ca)

* https://www.joelonsoftware.com/
* https://jvns.ca/about/
* https://aphyr.com/
* https://larahogan.me/blog/
* https://blog.nelhage.com/post/efficiency-vs-resiliency/
* https://chelseatroy.com/2023/04/21/whats-the-point-of-tech-conferences/
* https://mathbabe.org/
* https://wingolog.org/
* https://danluu.com/

## Title Bar

Even if it takes up a bit more space, having an aestetically pleasing title bar seems like a difference maker in terms of how professional a site looks.

## Fonts

Using fonts is really important for how professional a site looks.

## Content

The blogs I've listed above are all primarily driven by content.

## SVG

A useful tool for creating fancy looking blogs.

## Implementing these.


## CSS Improvements

Using disabled and enabled selectors makes buttons easier to implement.

```css
[disabled]="page <= 0"

.btn-blue:disabled {
  @apply bg-gray-500 text-gray-100;
}

.btn-blue:enabled:hover {
  @apply bg-blue-700;
}
```

ngClass is pretty neat as well. It allows you to add classes conditionally. I believe you can also choose to add a string from the component.ts class instead of the hard-coded strings that I've listed here.
```css
[ngClass]="{'btn-blue': page > 0, 'disabled': page <= 0}"
```

## Fonts

It's actually kinda crazy how much adding a font makes the blog look more professional. Maybe its also the fact that caring enough to edit a font makes it more likely that the other parts of the design also are cared about, but overall big fan of how much this small change makes.

```css
.roboto {
  font-family: 'Roboto Light','sans-serif'
}
```

<image Blog Header With Roboto>
<image Blog Header Centralized With Sans>
<image Blog Header First Pass>

## 
