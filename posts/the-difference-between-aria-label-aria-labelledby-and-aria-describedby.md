---
title: The difference between aria-label, aria-labelledby and aria-describedby
date: 2020-08-20T00:42:03.566Z
description:
  Often, aria-* attributes can be confusing, and are frequently used incorrectly. WebAIM noted that the majority of home pages where ARIA was present, actually resulted in more accessibility issues.
  No ARIA is better than incorrectly used ARIA.
tags:
  - ARIA
  - HTML
  - a11y
layout: layouts/post.njk
---

Often, aria-\* attributes can be confusing, and easy to use incorrectly. WebAIM noted that [the majority of home pages where ARIA was present, actually resulted in more accessibility issues.](https://webaim.org/projects/million/#aria)

No ARIA is better than incorrectly used ARIA. If you are not sure about an aria-\* attribute, then the [best place to learn about it is from the documentation](https://www.w3.org/TR/wai-aria-1.1/#state_prop_def).

## What is ARIA?

**Accessible Rich Internet Applications** or **ARIA** for short, allows you to give the information to the document which will then describe that information in ways that assistive technologies (or **AT** for short) can readily read and interpret. ARIA is a supplement to HTML, and should only be used when needed.

This may sound vague at first, but after putting ARIA to use, it becomes very clear what ARIA can and can’t do.

## What is aria-label, and how do you use it?

An `aria-label` attribute allows you to add an [accessible name](https://developer.paciellogroup.com/blog/2017/04/what-is-an-accessible-name/) to an element. A good majority of HTML elements carry an accessible name. For example, the inner content of a button would be the accessible name for that element, if no other text content or aria-\* attributes are provided.

To use `aria-label`, you will need to give it a value. The value provided (which is always a string), will be picked up by assistive technologies.

Here’s an example of what it would look like in markup:

`<button aria-label="Hello, world!">Hi</button>`

![Chrome DevTools, showcasing accessible name](/img/accessibility_tree_chrome_1.png)

**Example 1: An image of the accessibility tree within Chrome DevTools.**

In the accessibility tree, the button will add the value provided by the aria-label as the accessible name.

It’s important to note that this will replace the inner text with the aria-label “Hello, world!”, when it’s read by assistive technologies. This is shown in the example above, the “contents” of the button is crossed out, as the aria-label takes precedence.

This could be useful in various situations. For example, let’s say you have a button with an icon within and no text content.

`<button><i class="icon some-icon"></i></button>`

Assistive technologies will not be able to read this button properly as it contains no text content. Meaning that the accessible name would be “button” at best.

If we were to add an aria-label, it would provide adequate text content, as it would add an accessible name to the element.

`<button aria-label="some icon"><i class="icon some-icon"></i></button>`

A screen reader might then read “some icon, button”.

`aria-label` can be used in other situations as well. For example, adding a unique label to a navigation region:

```html
<div id="menu">
  <nav aria-label="Menu">...</nav>
</div>

<div id="pages">
  <nav aria-label="Pagination">...</nav>
</div>
```

This will allow a user using AT to distinguish between the two navigation regions.

There are many use cases for aria-label, but there are also cases where you should not use an aria-label. For example, you should not use an aria-label if the inner text content is different from what it would be for a visual user.

Here’s an example:

`<button aria-label="Close">Save and Close</button>`

![Chrome DevTools, showing applied accessible name](/img/accessibility_tree_chrome_1.png)

**Example 2: An image of the accessibility tree, showing the current accessible name, which is "Close".**

In the example above, the aria-label misses out on the inner text content. The inner text content could be what properly describes the action of the button, in which the button triggers an event which both saves and closes.

A user utilizing assistive technology might miss out on that content, as it is replaced by the aria-label, and may think the button does something different than what is actually intended**.**

**An aria-label should always provide equivalent information, to that of which it would provide visually.**

## What is an aria-labelledby, and how do you use it?

`aria-labelledby` allows you to provide a label to an element using an ID reference. That ID reference would be from an element which you want to use as a label.

For example:

```html
<div>
  <span id="test1">Tyler</span>
  <button aria-labelledby="test1">
    Wow!
    <!-- 
    This text is overridden for AT in place of the aria-labelledby 
    (if value is provided to the aria-labelledby attribute)
   -->
  </button>
</div>
```

In the example, the button receives the ID of “`test1`” from the span for the aria-labelledby value. This means that the accessible name for the button would be _"Tyler"_.

This is because aria-labelledby takes precedence over the inner text content of whichever element it is on. The user would never reach the buttons inner text if they were using assistive technology.

Here's what it looks like in the accessibility tree:

![Chrome DevTools, using aria-labelledby to show accessible name](/img/accessibility_tree_chrome_2.png)

**Example 3: An image of the accessibility tree, showing the value received from "aria-labelledby". That value being "Tyler".**

In regards to precedence, if you were to use both an `aria-label` and an `aria-labelledby` attribute, the `aria-labelledby` will take precedence over the `aria-label`, unless the aria-labelledby has an empty value.

Another thing to add is that you can add multiple references in an aria-labelledby, each separated by a space.

Here’s a quick example:

```html
<div>
  <span id="test1">Tyler</span>
  <span id="test2">Is</span>
  <span id="test3">Cool</span>

  <!-- Be sure to space out each ID reference -->
  <button aria-labelledby="test1 test2 test3">Wow!</button>
</div>
```

The above would look like the following, within the accessibility tree:

![Chrome DevTools, using aria-labelledby to show multiple references applied](/img/accessibility_tree_chrome_3.png)

**Example 4: An image of the accessibility tree, showing the values provided by the aria-labelledby, using multiple references.**

## **What is aria-describedby, and how do you use it?**

`aria-describedby` is very similar to `aria-labelledby`. Both reference other elements using a ID reference in order to gather a text alternative, but aria-describedby is used to “describe” the current element (or elements), rather than just label them.

This means that this is generally used more when providing a description to an element which tends to be based on a pre-existing element. Unlike `aria-labelledby`, `aria-describedby` will not take precedence over the inner text content, it will supplement the text content if there is any.

Here’s an example:

```html
<div>
  <span id="describeThis">It's just bolognese!</span>
  <button aria-describedby="describeThis">It's all right, Andy!</button>
</div>
```

Assistive technology might read something along the lines of, "It's all right, Andy! **button**, It's just bolognese!"

As with `aria-labelledby`, you can add multiple references, each separated by a space.

### Ending Notes

With that, I hope I gave a clear description on each attribute. Though that being said, you should only use these attributes if the situation doesn’t allow for plaintext, or semantic HTML elements.

### **Further Reading**

Here are a few links to topics if you wish to learn more about a11y and ARIA.

1. [aria-label does not translate by Adrian Roselli](https://adrianroselli.com/2019/11/aria-label-does-not-translate.html)
2. [aria-labels and relationships by Google Web Fundamentals](https://developers.google.com/web/fundamentals/accessibility/semantics-aria/aria-labels-and-relationships)
3. [Types of assistive technology by Berkeley Web Access](https://webaccess.berkeley.edu/resources/assistive-technology)
4. [ARIA by MDN Web Docs (Mozilla)](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA)
