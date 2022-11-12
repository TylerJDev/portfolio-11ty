---
title: When role="button" is not enough
date: 2020-09-07T18:36:17.247Z
description: The attribute role="button" is often used in place of a regular
  button element, yet this is usually not enough to ensure that the element is accessible.
tags:
  - ARIA
  - HTML
  - a11y
  - roles
layout: layouts/post.njk
---

Whenever I find a website that I particularly like, I open up my browser’s devtools to get an idea on how it was built. Needless to say, I do this _quite a lot_. In some cases, I solely look at the code to get an idea of how accessible a website truly is. One thing that always stands out like a sore thumb is an element with a improper role attribute.

For this post I will talk about **role="button"**, how it may be misused and how to make it accessible.

Developers tend to use `role="button"` to turn an element into a "button". This is to indicate to users, that the element with the attribute `role="button"` is indeed a button via the accessibility tree. In many cases this is not enough to make that element accessible.

Before I begin, I would like to say that in **all cases**, utilizing a regular `<button>` element is preferred over `role="button"`. You should only utilize the role in events where you cannot use a native button element.

## What is not enough?

When utilizing `role="button"`, you need to ensure that the element is focusable, though that is only if the element isn’t already natively focusable. For example, an anchor element `<a>` is natively focusable if there is a valid `href` attribute attached.

Imagine the following scenario, where a user needs to utilize the search button, but it’s a div:

```html
<!-- Toggles search input on/off -->

<div onclick="openSesame">Search</div>

<!-- This is not at all accessible! -->
```

**In the example above, the element is not accessible.**

Perhaps there’s some sort of constraint where you can’t turn an element into a native button. That’s where `role="button"` comes in:

```html
<!-- role attribute takes value as a string -->

<div role="button" onclick="openSesame">Search</div>

<!-- is now a "button" in the accessibility tree -->
```

Though we are close, this is still not accessible. A user cannot focus on the element, as there is no tabindex. We can fix that by adding a tabindex with the value of "0":

```html
<!-- tabindex="0" means element is in sequential focus navigation of the page -->
<div role="button" tabindex="0" onclick="openSesame">Search</div>
```

It might not be a surprise to hear me say that this still isn’t enough. In some cases, a user may now be able to focus and trigger this button, whereas some will still not be able to.

Some screen readers simulate a "click" when enter is pressed instead of a keydown event. That means that in the above example, the "onclick" event would actually trigger for _some_ assistive technology. Though for most, it still isn't accessible. This isn't a topic I will dwell on in this post, but this is always good to keep note of.

In a good majority of cases where I see elements that have `role="button"`, **two out of three** criteria are met. One being `role="button"` and second having a `tabindex`, although they often fall short of having an actual keydown/keyup event attached.

There are also cases where the element that has `role="button"` is a natively focusable element, like an anchor element `<a>` (if a valid href is attached). In this case, using the "enter" key does work, yet you can’t activate an anchor element with the space key, whereas with a `<button>` element, both "space" and "enter" would trigger it.

## How to make it accessible

Making an accessible button when a native button cannot be used is pretty simple. This is how:

### Step 1: Add the role

```html
<!-- Add role="button" to the elem -->

<div role="button">Hello, I'm now a button!</div>
```

Adding role="button" will ensure that 'button' is announced to as a button for assistive tech. Without that role, a user utilizing a screen reader may be confused about what type of element it is. With the role added, it'll be listed as a "button" in the accessibility tree.

### Step 2: Add a tabindex

```html
<div role="button" tabindex="0">Hello, I’m a button!</div>
```

A tabindex ensures that the element is focusable so that a user may focus on it. It's generally not advisable to set a tabindex value greater than 0. [Here's a good post on why you shouldn't set a positive tabindex.](https://webaim.org/techniques/keyboard/tabindex) Do note that a negative tabindex such as -1 will remove that element from tab order, while allowing you to programmatically set focus to that element.

### Step 3: Add a keydown/keyup event

```javascript
const fakeBtn = document.querySelector('div[role="button"]');

fakeBtn.addEventListener("keydown", function (e) {
  const keyD = e.key !== undefined ? e.key : e.keyCode;
  // e.key && e.keycode have mixed support - keycode is deprecated but support is greater than e.key
  // I tested within IE11, Firefox, Chrome, Edge (latest) & all had good support for e.key

  if (keyD === "Enter" || keyD === 13) {
    // In IE11 and lower, e.key will equal "Spacebar" instead of ' '
    // Default behavior is prevented to prevent the page to scroll when "space" is pressed
    e.preventDefault();
    this.click();
  }
});

fakeBtn.addEventListener("keyup", function (e) {
  const keyD = e.key !== undefined ? e.key : e.keyCode;
  if (["Spacebar", " "].indexOf(keyD) >= 0 || keyD === 32) {
    e.preventDefault();
    this.click();
  }
});
```

[Example on CodePen](https://codepen.io/TylerJDev/pen/LYryJeX)

Adding a proper keydown and keyup event should ensure that the element triggers the click event (if one is attached). The reason why you'd want to utilize both a keydown & keyup event, is to simulate a native button. For a native button, the 'space' key will only trigger an action on keyup, whereas 'enter' will trigger with a keydown.

### Step 4: Test it

You should always manually test, especially for accessibility. Perhaps something is blocking the keydown event, or maybe focus isn’t styled properly on the element. You won’t know until you test it out! For this, you can utilize your keyboard to try to "trigger" the element.

I'd also suggest trying out a screen reader. I'd highly recommend NVDA, which is a [free open source screen reader](https://www.nvaccess.org/). You may already have a screen reader pre-installed too!

If you're a macOS user, you should have access to VoiceOver, which is a very easy to use screen reader. [VoiceOver comes pre-installed with most Mac systems.](https://www.apple.com/voiceover/info/guide/_1121.html)

If you're a windows user you should have Narrator installed, [which is a screen reader made by Microsoft](https://support.microsoft.com/en-us/help/22798/windows-10-complete-guide-to-narrator).

## Common gotchas

1. **Styles and role="button"**

   When utilizing `role="button"`, sometimes it’s good to style that element as an actual button would appear. This is to ensure that users are able to recognize it as a button. Also, don't forget to add hover, focus and active styles to the button to mimic a native button.

2. **Not having the same click and key event**

   A key event (keydown/keyup) should trigger the same event as the click event. You should also ensure that a click event is attached and that the element utilizing `role="button"` should function the same way for click, "enter" or "space".

3. **Windows High Contrast Mode**

   Something that's very easy to miss when utilizing `role="button"` is that Windows High Contrast Mode (WHCM) will not display `role="button"` as it would actual native button elements. This can have some impact on a user as they might not be able to discern a button utilizing `role="button"`. Here's a great article surrounding [WHCM and custom elements](https://www.scottohara.me/blog/2019/02/12/high-contrast-aria-and-images.html) (such as `role="button"`). The best solution here is to use a native button!

## ARIA attributes

**There are a few ARIA attributes you can utilize to make role="button" even more accessible.**

1. `aria-pressed`

   Attribute `aria-pressed` can have the values "**true**", "**false**" or "**mixed**". Utilizing this attribute means that you can make a "toggle" button, which indicates the state of the button. When the value is "true", it means that the button is currently pressed and when the value is "false", it means that the button is not currently pressed. The "mixed" value means that the values of more than one item controlled by the button do not all share the same value. _I personally haven't seen aria-pressed="mixed" being used before, and I have not used it myself._ [Here's a bit more information about aria-pressed from the specification](https://www.w3.org/TR/wai-aria-1.1/#aria-pressed).

2. `aria-expanded`

   You may utilize aria-expanded to notify the user that the element, or element it controls is either expanded or collapsed. [This is generally used when making accordions](https://www.w3.org/TR/wai-aria-practices-1.1/examples/accordion/accordion.html), where the content is initially hidden and only expanded when activating the trigger. The values supported by `aria-expanded` are "**true**" or "**false**". [You can learn more from the specification](https://www.w3.org/TR/wai-aria-1.1/#aria-expanded).

**Update (11/12/22):** Adjusted example [on using key events](#step-3-add-a-keydown-keyup-event), to now utilize `keyup`, instead of solely utilizing `keydown` for both keys (space/enter), and codepen example. Adjusted "Common gotchas" section to talk about Windows High Contrast Mode, styles with role="button", and removed "gotcha" about links.

## Further Reading

With that, I hope this article helps you when creating accessible buttons! There's a lot of different ways to utilize WAI-ARIA, some of which I hope to cover soon. Please feel free to ask questions!

Here's a few posts that I believe will supplement what was discussed in this post.

1. [ARIA: button role - Accessibility | MDN](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles/button_role)
2. [Toggle Buttons - Inclusive Components](https://inclusive-components.design/toggle-button/)
3. [Button Examples | WAI-ARIA Authoring Practices 1.1](https://www.w3.org/TR/wai-aria-practices-1.1/examples/button/button.html)
4. [Keyboard Accessibility with the Space Bar](http://www.last-child.com/keyboard-accessibility-with-the-space-bar/)
5. [What does "focusable" mean? - ally.js](https://allyjs.io/what-is-focusable.html)
6. [Using ARIA](https://www.w3.org/TR/using-aria/)
