# Basics
* [Reference](https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Styling_basics)
* CSS is a rule-based language — you define rules by specifying groups of styles that should be applied to particular elements or groups of elements on your web page.
* For example to style the main heading:
```
h1 {
  color: red;
  font-size: 5em;
}
```
## Selectors
* Main selectors
  - **Type**: Select an html element type using its **name**
  - **Class**: Starts with a dot (.) character. Select a class using `.<classname>`
  - **Class on specific element**: Use the dot along the element name: `h1.hightlight`
  - **ID**: Starts with a `#`. Select using `#<id>`
  - **State**: Select based on state: `a:hover`
* Attribute selectors
  - Attribute type: `a[title]`
  - Attribute value: `a[href="https://example.com"]`
  - Regular expression match: `p[class~="special"]`
## Box Model
* Everything in CSS has a box around it
* In CSS we have several types of boxes that generally fit into the categories of `block` boxes and `inline` boxes.
The type refers to how the box behaves in terms of page flow and in relation to other boxes on the page.
Boxes have an `inner display type` and an `outer display type`.
* In general, you can set various values for the display type using the `display` property, which can have various values.
* If a box has a display value of `block`, then:
  - The box will break onto a new line.
  - The `width` and `height` properties are respected.
  - Padding, margin and border will cause other elements to be pushed away from the box.
  - If `width` is not specified, the box will extend in the inline direction to fill the space available in its container.
* If a box has a display type of `inline`, then:
  - The box will not break onto a new line.
  - The `width` and `height` properties will not apply.
  - Top and bottom padding, margins, and borders will apply but will not cause other inline boxes to move away from the box.
  - Left and right padding, margins, and borders will apply and will cause other inline boxes to move away from the box.
* `block` and `inline` display values are said to be **outer display types — they affect how the box is laid out in relation to other boxes around it**. Boxes also have an **inner display type, which dictates how elements inside that box are laid out**.
* You can change the inner display type by setting an inner display value, for example `display: flex;`
* Making up a `block` box in CSS we have the:
  - **Content box**: The area where your content is displayed; size it using properties like `width` and `height`.
  - **Padding box**: The padding sits around the content as white space; size it using `padding` and related properties.
  - **Border box**: The border box wraps the content and any padding; size it using `border` and related properties.
  - **Margin box**: The margin is the outermost layer, wrapping the content, padding, and border as whitespace between this box and other elements; size it using `margin` and related properties.
![box-model](https://github.com/user-attachments/assets/b644e976-9778-484d-a7fd-09a77edd077e)
