# Building out the site

Not too many hang-ups for the rest of development.

Lots of fiddling with TailwindCSS, but overall the library pretty much works as you'd expect.

Classes I've found that I've been using a lot
```
flex : flexbox
flex-wrap : flexbox which can go to multiple lines
space-x-2 : flex items have small gaps between them
space-y-2 : flex items have small gaps between them when stacked
rounded : Rounds corers of box
px-2 : padding x 2. One thing to note here is that the height of a div does depend a bit on the width, so it can cause spacing changes
py-2 : padding y 2
text-xl : font size
text-3xl : font size for headers
```

Taken pretty much directly from tailwind's guide. The ability to add these to the styles.css file (global stylesheet set up in `angular.json` "projects.yaml-config-manager-ng.architect.build.styles") really makes it easy to create buttons everywhere, and the button sizes could be changed by updating the font-size.
```tailwindcss
.btn {
  @apply font-bold px-4 py-2 rounded
}

.btn-blue {
  @apply text-white bg-blue-500 border-blue-700
}

.btn-blue:hover {
  @apply bg-blue-700
}

.btn-blue:disabled {
  @apply text-gray-200 bg-gray-500 border-gray-700
}
```

