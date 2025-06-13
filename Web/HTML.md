# Guide
* [Reference](https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Structuring_content/Webpage_metadata)
* Meta
  - Add description for search engines: `<meta name="description" content="<content>"`
* Favicon: `<link rel="icon" href="favicon.ico" type="image/x-icon" />`
* CSS: `<link rel="stylesheet" href="my-css-file.css" />`
* Javascript: `<script src="my-js-file.js" defer></script>`
  - The `defer` keyword loads Javascript only after **all** the html has been loaded
* Unstructred list: `<ul><li>...</li><li>...</li></ul>`
* Ordered list: `<ol><li>...</li><li>...</li></ol>`
* Image:
```
<img
  src="images/dinosaur.jpg"
  alt="The head and torso of a dinosaur skeleton;
          it has a large head with long sharp teeth"
  width="400"
  height="341"
  title="A T-Rex on display in the Manchester University Museum" />
```
  - The `title` is displayed on mouse hover
* Tables
  - Contents inside: `<table></table>`
  - Table row: `<tr>...</tr>`
  - Table cell: `<td>...</td>`
  - Headers: `<th>...</th>`
  - Spanning attributes:
    * Column: `colspan="<num>"`
    * Row: `rowspan="<num>"`
* Buttons
  - Simple: `<button>...</button>`
## Forms
* [Reference](https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Forms/Your_first_form)
* A form contains:
  - A `<form>` element which wraps all of the form
  - One or more pairs consisting of a `<label>` element and a form control element, usually a `<input>` element
  - A `<button>` element used to submit the form, usually of `type="submit"`
* The `<form>` element attributes:
  - `action`: Contains a path to the page that we want to send the submitted form data to
  - `method`: The method to use, usually one of `get` or `post`
* The `<input>` element attributes:
  - `type`: Specifies the type of form control to create.
  - `name`: Specifies a name for the data item. When the form is submitted, the data is sent in name/value pairs
  - `id`: Specifies an ID that can be used to identify the element.
  - `required`: Specifies that a value has to be entered into the form element before the form can be submitted.
  - `size`
  - `maxlength`: The maximum number of characters that can be entered into the box
* Typical types for input elements:
  - Radio: These render as a set of push button controls where only one of the set can be selected at any one time. The `name` is always the same but the `value` is different
  - Checkbox: These render as a set of on/off state checkboxes. Type is `checkbox` and we can use the attribute `checked` to have it checked
  - Radio: Type is `radio` and we can use the attribute `checked` to have it checked
  - Drop-Down menu: `<select><option value="val1">...</option><option value="val2">...</option></select>`
  - Multi-line: `<textarea name="<name>" rows="<num rows>" cols="<num columns>"></textarea>`
  - `password`
  - `hidden`: Invisible to the user but still sent when the form is submitted
  - File picker: Type is `file`. The types of files that are accepted can be constrained using the `accept` attribute (ie `accept="image/*"`)
### HTML5 input fields
* Email: `type="email"`
* Phone number: `type="tel"`
* URL: `type="url"`
* Numeric field: `type="number"`. Minimum and maximum values are allowed using the `min` and `max` attributes
