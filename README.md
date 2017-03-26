# CSS ::contents pseudo-element proposal

All non-replaced elements have a `::contents` pseudo-element, which wraps all the contents inside the element, not including contents generated by [`::before`](https://www.w3.org/TR/css-pseudo-4/#selectordef-before) and [`::after`](https://www.w3.org/TR/css-pseudo-4/#selectordef-after).

`::contents` pseudo-elements must act as if they were assigned [`display: contents`](https://www.w3.org/TR/css-display-3/#valdef-display-contents) via a rule in the UA origin. Therefore, they do not generate any boxes and are treated as if they had been replaced with their children. This must be possible to override via [`display`](https://www.w3.org/TR/css-display-3/#the-display-properties), so they do generate boxes if desired.

> **Note**
> This is consistent with [slots in a shadow tree](https://drafts.csswg.org/css-scoping/#slots-in-shadow-tree).

A `::contents` pseudo-element can be styled exactly like any normal document-sourced element in the document tree.
When its [`display`](https://www.w3.org/TR/css-display-3/#the-display-properties) property does not compute to [`contents`](https://www.w3.org/TR/css-display-3/#valdef-display-contents) nor [`none`](https://www.w3.org/TR/css-display-3/#valdef-display-none), the pseudo-element generates boxes as if it was an immediate children of its originating element. When its [`display`](https://www.w3.org/TR/css-display-3/#the-display-properties) property computes to [`none`](https://www.w3.org/TR/css-display-3/#valdef-display-none), the pseudo-element and the contents of the originating element generate no boxes.

> **Example**
> 
> The [fictional tag sequence](https://www.w3.org/TR/css-pseudo-4/#fictional-tag-sequence) for this HTML fragment with the following rules
> 
> ```css
> div::before { content: 'A'; }
> div::contents { display: inline; }
> div::after { content: 'C'; }
> ```
> ```html
> <div>B</div>
> ```
> 
> is:
> 
> ```html
> <div>
>   <div::before>A</div::before>
>   <div::contents>B</div::contents>
>   <div::after>C</div::after>
> </div>
> ```

> **Example**
> 
> Defining margins that only apply between flex-items ([issue 592](https://github.com/w3c/csswg-drafts/issues/592)) can be easily done as follows:
> 
> ```css
> .flex-container {
>   overflow: hidden;
> }
> .flex-container::contents {
>   display: flex;
>   margin: -25px;
> }
> .flex-container > .flex-item {
>   margin: 25px;
> }
> ```

> **Example**
> 
> The [`::before`](https://www.w3.org/TR/css-pseudo-4/#selectordef-before) and [`::after`](https://www.w3.org/TR/css-pseudo-4/#selectordef-after) pseudo-elements generate content inside an element. For example,
> 
> ```css
> div { /* border, margin, ... */ }
> div::before { content: 'A'; }
> div::after { content: 'C'; }
> ```
> ```html
> Previous
> <div>B</div>
> Following
> ```
>
> produces the following [fictional tag sequence](https://www.w3.org/TR/css-pseudo-4/#fictional-tag-sequence):
> 
> ```html
> Previous
> <div>
>   <div::before>A</div::before>
>   B
>   <div::after>C</div::after>
> </div>
> Following
> ```
> 
> However, sometimes it is desirable to generate content outside the element. This can be accomplished by adding
> 
> ```css
> .container { display: contents; }
> .container::contents { display: block; /* border, margin, ... */ }
> ```
>
> resulting in
>
> ```html
> Previous
> <div::before>A</div::before>
> <div::contents>B</div::contents>
> <div::after>A</div::after>
> Following
> ```

## Inheritance and the `::contents` Pseudo-element

A `::contents` pseudo-element [inherits](https://www.w3.org/TR/css-cascade-4/#inheriting) from its originating element.

A child element [inherits](https://www.w3.org/TR/css-cascade-4/#inheriting) [inherited properties](https://www.w3.org/TR/css-cascade-4/#inherited-property) from the `::contents` pseudo-element originated by the parent element.

> **Issue**
> 
> What should happen with non-inherited properties? Same as for [`::first-line`](https://www.w3.org/TR/css-pseudo-4/#first-line-pseudo) ([issue 1097](https://github.com/w3c/csswg-drafts/issues/1097))
