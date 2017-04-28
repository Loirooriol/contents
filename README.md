# CSS ::contents pseudo-element proposal

All non-replaced elements have a `::contents` pseudo-element, which wraps all the contents inside the element, not including [`::marker`](https://drafts.csswg.org/css-pseudo-4/#marker-pseudo) nor the contents generated by [`::before`](https://www.w3.org/TR/css-pseudo-4/#selectordef-before) and [`::after`](https://www.w3.org/TR/css-pseudo-4/#selectordef-after), if any.

`::contents` pseudo-elements must act as if they were assigned [`display: contents`](https://www.w3.org/TR/css-display-3/#valdef-display-contents) via a rule in the UA origin. Therefore, they do not generate any boxes and are treated as if they had been replaced with their children. This must be possible to override via [`display`](https://www.w3.org/TR/css-display-3/#the-display-properties), so they do generate boxes if desired.

> **Note**
> This is consistent with [slots in a shadow tree](https://drafts.csswg.org/css-scoping/#slots-in-shadow-tree).

A `::contents` pseudo-element can be styled exactly like any normal document-sourced element in the document tree.

When its [`display`](https://www.w3.org/TR/css-display-3/#the-display-properties) property does not compute to [`contents`](https://www.w3.org/TR/css-display-3/#valdef-display-contents) nor [`none`](https://www.w3.org/TR/css-display-3/#valdef-display-none), the pseudo-element generates boxes as if it was an immediate child of its originating element, and the actual contents of the originating element generate boxes as if they were immediate children of the pseudo-element.

When its [`display`](https://www.w3.org/TR/css-display-3/#the-display-properties) property computes to [`none`](https://www.w3.org/TR/css-display-3/#valdef-display-none), the pseudo-element and the actual contents of the originating element generate no boxes. [`::marker`](https://drafts.csswg.org/css-pseudo-4/#marker-pseudo), [`::before`](https://www.w3.org/TR/css-pseudo-4/#selectordef-before) and [`::after`](https://www.w3.org/TR/css-pseudo-4/#selectordef-after) pseudo-elements generate boxes as ausual, according to their [`display`](https://www.w3.org/TR/css-display-3/#the-display-properties) and [`content`](https://drafts.csswg.org/css-content-3/#content-property) properties.

When attached to a replaced element, the `::contents` pseudo-element has no effect.

> **Issue:** 
> What about "kinda-replaced" elements?

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
> div { display: contents; }
> div::contents { display: block; /* border, margin, ... */ }
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

> **Issue:**
> What should happen with non-inherited properties? Same as for [`::first-line`](https://www.w3.org/TR/css-pseudo-4/#first-line-pseudo) ([issue 1097](https://github.com/w3c/csswg-drafts/issues/1097))

## Interaction with the [CSS Generated Content Module](https://drafts.csswg.org/css-content/)

> **Issue:** This section intends to explain the magic behind `::contents` in terms of the `content` property. But Tab [said](https://github.com/w3c/csswg-drafts/issues/1195#issuecomment-293082128) that the `contents` was not supposed to affect inheritance, and that it will probably be dropped anyways.

As a [tree-abiding pseudo-element](https://www.w3.org/TR/css-pseudo-4/#treelike), the [`content`](https://drafts.csswg.org/css-content/#content-property) property applies to `::contents`.

 - [`normal`](https://drafts.csswg.org/css-content/#valdef-content-normal)
 
    For elements and `::contents` pseudo-elements, this computes to [`contents`](https://drafts.csswg.org/css-content/#valdef-content-contents).
    
    > **Issue:** Should [`normal`](https://drafts.csswg.org/css-content/#valdef-content-normal) compute to [`none`](https://drafts.csswg.org/css-content/#valdef-content-none) on `::contents` pseudo-elements in case the computed value of the [`content`](https://drafts.csswg.org/css-content/#content-property) property of the originating element does not include [`contents`](https://drafts.csswg.org/css-content/#valdef-content-contents)?
    
    For [`::before`](https://www.w3.org/TR/css-pseudo-4/#selectordef-before) and [`::after`](https://www.w3.org/TR/css-pseudo-4/#selectordef-after) pseudo-elements, this computes to [`none`](https://drafts.csswg.org/css-content/#valdef-content-none).
    
 - [`none`](https://drafts.csswg.org/css-content/#valdef-content-none)
  
    On elements, this inhibits the creation of the `::contents` pseudo-element, as if that pseudo-element had [`display: none`](https://www.w3.org/TR/css-display-3/#valdef-display-none).
    
    > **Note:** Inhibiting the `::contents` pseudo-element also prevents the actual contents of the element from being rendered, unless [`::before`](https://www.w3.org/TR/css-pseudo-4/#selectordef-before) or [`::after`](https://www.w3.org/TR/css-pseudo-4/#selectordef-after) include [`contents`](https://drafts.csswg.org/css-content/#valdef-content-contents) in the computed value of their [`content`](https://drafts.csswg.org/css-content/#content-property) property.
    
    On `::contents` pseudo-elements, this inhibits the actual children of the originating element from being rendered as children of this pseudo-element, as if the element was empty.
    
    > **Issue:** Should `content: none` on `::contents` completely inhibit it? This would be more consistent with other pseudo-elements, but less useful, because this would be equivalent to using `content: none` on the originating element.

    On other pseudo-elements, this inhibits the creation of the pseudo-element as if it had [`display: none`](https://www.w3.org/TR/css-display-3/#valdef-display-none).
    
 - [`<content-replacement>`](https://drafts.csswg.org/css-content/#typedef-content-content-replacement)
 
    Makes the element or pseudo-element a replaced element, filled with the specified `<image>`. Its normal contents are suppressed and do not generate boxes, as if they had [`display: none`](https://www.w3.org/TR/css-display-3/#valdef-display-none).

    > **Note:** Replaced elements do not have [`::before`](https://www.w3.org/TR/css-pseudo-4/#selectordef-before), [`::after`](https://www.w3.org/TR/css-pseudo-4/#selectordef-after) or `::contents` pseudo-elements; the content property replaces their entire contents.

 - [`<content-list>`](https://drafts.csswg.org/css-content/#typedef-content-content-list)
 
    On elements, this replaces the `::contents` pseudo-element with one or more anonymous inline boxes corresponding to the specified values, in the order specified. Unless [`contents`](https://drafts.csswg.org/css-content/#valdef-content-contents) appears in the `<content-list>`, the `::contents` pseudo-element is suppressed and does not generate boxes, as if it had [`display: none`](https://www.w3.org/TR/css-display-3/#valdef-display-none).
    
    On pseudo-elements, this replaces the pseudo-element’s contents with one or more anonymous inline boxes corresponding to the specified values, in the order specified. Its normal contents are suppressed and do not generate boxes, as if they had [`display: none`](https://www.w3.org/TR/css-display-3/#valdef-display-none).
    
 - [`contents`](https://drafts.csswg.org/css-content/#valdef-content-contents)
 
    On elements, this allows the creation of the `::contents` pseudo-element. It is useless to include [`contents`](https://drafts.csswg.org/css-content/#valdef-content-contents) twice in a single `content` property, because the second occurrence simply has no effect, as there can only be a single `::contents` pseudo-element.
    
    On pseudo-elements, this makes the contents of the pseudo-element be the actual contents of the originating element. It is useless to include `contents` more than once in a single `content` property, or use [`contents`](https://drafts.csswg.org/css-content/#valdef-content-contents) in multiple pseudo-elements, because only the first occurrence has an effect, as the actual contents of the originating element cannot be duplicated.
    
    When used in multiple pseudo-elements, the first occurrence is searched in the following order:
    
    1. In the `::contents` pseudo-element, but only if [`contents`](https://drafts.csswg.org/css-content/#valdef-content-contents) appears in the computed value of the [`content`](https://drafts.csswg.org/css-content/#content-property) property of the originating element.
    2. In the [`::before`](https://www.w3.org/TR/css-pseudo-4/#selectordef-before) pseudo-element
    3. In the [`::after`](https://www.w3.org/TR/css-pseudo-4/#selectordef-after) pseudo-element
