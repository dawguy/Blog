# Attribute Vs Property Binding

https://angular.io/guide/attribute-binding

https://stackoverflow.com/questions/38306363/svg-circle-cant-bind-to-cx-since-it-isnt-a-known-native-property

Search for the difference between html properties and attributes. If you are binding to attributes you need the prefix

No idea why [attr.width] is needed instead of [width] though.

https://stackoverflow.com/questions/6003819/what-is-the-difference-between-properties-and-attributes-in-html

https://angular.io/guide/binding-syntax#html-attribute-vs-dom-property

"When you write a data binding, you're dealing exclusively with the DOM properties and events of the target object."

Seems like we set up width at creation

```txt
Property and attribute comparison
Though you could technically set the [attr.disabled] attribute binding, the values are different in that the property binding must be a boolean value, while its corresponding attribute binding relies on whether the value is null or not. Consider the following:

<input [disabled]="condition ? true : false">
<input [attr.disabled]="condition ? 'disabled' : null">
```

## It works as expecrted

We'll see how the performance is, but at a high level it works as expected.

Changing an attribute `[attr.transform]="getOffsetTransform(product)` does cause the SVG to be moved. In paarticular a Input() translate being bumped causes an immediate move of the SVG.

Not 100% sure if the DOM is recreated vs translate values are bumped (manipulated), but both should be fine for my use cases.

## Some more

There are console errors though :'(

https://stackoverflow.com/questions/44367701/expressionchangedafterithasbeencheckederror-when-passing-the-width-of-an-element

https://medium.com/angular-in-depth/everything-you-need-to-know-about-the-expressionchangedafterithasbeencheckederror-error-e3fd9ce7dbb4


