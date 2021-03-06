---
title: Accessibility
---

页面的可访问性是每个人的责任，本文档的目的是为开发人员和设计人员提供通用的可访问性指南。

Accessibility is everyone’s responsibility, and the purpose of this document is to provide general accessibility guidelines to developers and designers.

## Overview

Our products should be accessible to all. At minimum, features should comply to the requirements listed in [508 Reference Guide - 1194.22](https://www.access-board.gov/guidelines-and-standards/communications-and-it/about-the-section-508-standards/guide-to-the-section-508-standards/web-based-intranet-and-internet-information-and-applications-1194-22) from the US Access Board, and conform to [Web Content Accessibility Guidelines 2.0](https://www.w3.org/TR/WCAG20/#conformance) at Level AA.

- **Visual**: blindness, low vision, color blindness, using a screen reader or related assistive tech for lifestyle reasons (e.g. long car commute), machine readability and screen scraping technologies

- **Hearing**: deafness, hearing impairment, speech impairment, using closed captioning or other assistive features for lifestyle reasons (e.g. coworking in a loud coffee shop)

- **Cognitive**: including short-term memory issues, dyslexia, learning disabilities, trying to work or consume content while distracted or multitasking, etc.

- **Mobility**: mobility impairments, repetitive stress injuries, power users who love keyboard shortcuts, busy parents holding a sleeping child while trying to operate a computer with one hand, etc.

## General guidelines

### Semantic markup

Always use semantic HTML elements, like `button`, `ul`, `nav`. Most modern browsers implement the accessibility features outlined in the specs for these elements; without them, elements will need additional [ARIA attributes and roles](https://www.w3.org/WAI/PF/aria) to be recognized by assistive technologies.

Elements like `h1`-`h6`, `nav`, `footer`, `header` have [meaningful roles](https://www.w3.org/WAI/PF/aria/roles) assigned, so use them carefully. This can help assistive technologies read the page better and help users find information quicker.

Only use a `div` or a `span` to markup up content when there isn't another HTML element that would semantically be more appropriate, or when an element is needed exclusively for applying CSS styles or JS behaviors.

```html
<!-- Do: Button can be focused and tabbed to. -->
<button type="button" class="btn">Send</button>
```

```html
<!-- Don't: Button can't be focused and tabbed to. -->
<span class="btn">Send</span>
```

> More on semantic markup:
>
> - [Semantic Structure – WebAIM](http://webaim.org/techniques/semanticstructure/)

### Keyboard accessibility

Make sure elements can be reached via tabbing, and actions can be triggered with a keyboard. Using semantic markup is especially important in this case as it ensures that actionable elements are tabbable and triggerable without a mouse. Note that elements positioned off-screen are still tabbable, but those hidden with `display: none` or `visibility: hidden` are effectively removed from content since they are neither read by screen readers nor reachable by keyboard.

Tab order is determined by the order of elements in the DOM, and not by their visual positioning on the page after CSS is applied. CSS properties `float` and `order` for flex items are two common sources for disconnection between visual and DOM order.

The `tabindex` attribute should only be used when absolutely necessary.

- `tabindex=0/-1` makes an element focusable, while `tabindex=0` also includes the element in the normal tab order. In both cases, keyboard triggers of the element still require scripting, so where possible, use [interactive content](http://w3c.github.io/html/dom.html#kinds-of-content-interactive-content) instead.

- `tabindex=1+` takes an element to the very front of the default tab order. When it's applied, the element's visual positioning is no longer indicative of its tab order, and the only way to find out where an element is would be by tabbing through the page. Therefore, unless a page is carefully designed around elements with positive tabindex, it is very error-proned, and thus currently prohibited in yuanshen.site.

Finally, bear in mind that some assistive technologies have keyboard combinations of their own that will override the combination on the web page, so don't rely on keyboard shortcuts as the only alternative to mouse actions.

```html
<!-- Do: Tab order can be guessed. -->
<button type="button" class="btn">1</button>
<button type="button" class="btn">2</button>
<button type="button" class="btn">3</button>
```

```html
<!-- Don't: The second button's tab order is unexpected. -->
<button type="button" class="btn">1</button>
<button type="button" class="btn" tabindex="1">2</button>
<button type="button" class="btn">3</button>
```

> More on keyboard accessibility:
>
> - [Focus Order – Understanding WCAG 2.0](https://www.w3.org/TR/UNDERSTANDING-WCAG20/navigation-mechanisms-focus-order.html)
> - [Time to revisit accesskey? by Léonie Watson](http://tink.uk/time-to-revisit-accesskey/)
> - [Flexbox & the keyboard navigation disconnect by Léonie Watson](http://tink.uk/flexbox-the-keyboard-navigation-disconnect/)

### Visual accessibility

Be mindful when using small font size, thin font weight, low contrast colors in designs as it can severely affect usability.

Instead of relying solely on color to communicate information, always combine color with another factor, like shape or position change. This is important because some colors can be hard to tell apart due to color blindness or weak eyesight.

> More on visual accessibility:
>
> - [Use of Color – Understanding WCAG 2.0](https://www.w3.org/TR/UNDERSTANDING-WCAG20/visual-audio-contrast-without-color.html)
> - [Contrast – Understanding WCAG 2.0](http://www.w3.org/TR/UNDERSTANDING-WCAG20/visual-audio-contrast-contrast.html)

### Text and labels

Make sure text-based alternative is always available when using icons, images, and graphs, and that the text by itself presents meaningful information.

```html
<!-- Do: Link text communicates destination. -->
<p>Find out more about <a href="#url">Genshin Impact Map</a>.<br /></p>
```

```html
<!-- Don't: "Click here" is not meaningful. -->
<p>To find out more about Genshin Impact Map, <a href="#url">click here</a>.</p>
```

Use `aria-label` when there is no text.

```html
<a href="https://help.yuanshen.site/"
  ><%= octicon("question", :"aria-label" => "Help") %></a
>
```

### Link responsibly

Navigating from a list of all the links on a given web page is very common for assistive technology users. We should make sure that the link text itself is meaningful and unique, and there should be as few duplicated references as possible.

> More on link responsibly:
>
> - [Link Purpose – Understanding WCAG 2.0](https://www.w3.org/TR/UNDERSTANDING-WCAG20/navigation-mechanisms-refs.html)

### Dynamic content

When using JavaScript to change the content on the page, always make sure that screen reader users are informed about the change. This can be done with `aria-live`, or focus management.

> More on dynamic content:
>
> - [ARIA Live Regions – MDN](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Live_Regions)

### Focus management

Focus management is useful for informing users about updated content, and for making sure users land on the next useful section after performing an action. This means using scripts to change user’s currently focused element, and when focus changes, screen readers will read out change.

### Accessible forms

It is common for assistive technology users to jump straight to a form when using a website, so make sure most relevant information is in the form and is labelled properly. Labels and inputs should be associated with the `label[for]` and `input[id]`, and help texts should either be part of the label or be associated with `aria-describedby`.

```html
<!-- Do: Click "Email" label to focus on the input, and help text is read out by screen readers when focusing on the input. -->
<label for="test_input">Email</label><br />
<input
  id="test_input"
  aria-describedby="test_input_help_good"
  class="width-full my-1"
  type="email"
/>
<div id="test_input_help_good" class="f6">
  Please enter an email ending with @yuanshen.site.
</div>
```

```html
<!-- Don't: Input and label are not associated, and help text is not read out by screen reader when focusing on the input. -->
<label>Email</label><br />
<input type="email" class="width-full my-1" />
<div id="test_input_help_bad" class="f6">
  Please enter an email ending with @yuanshen.site.
</div>
```

## Development tools

### External tools

- Google Chrome provides an [Accessibility Developer Tools extension](https://chrome.google.com/webstore/detail/accessibility-developer-t/fpkknkljclfencbdbgkenhalefipecmb). Once installed, go to Audits tab in developer tools, tick Accessibility. It scans the page for violations and suggests solutions.

- If you're working on a design that uses color to communicate something, grab the [Chromatic Vision Simulator app](https://itunes.apple.com/tw/app/chromatic-vision-simulator/id389310222?mt=8), this will let you use your iPhone camera to see what a colorblind person would see.

- Check out [the Web Accessibility showcase on GitHub](https://yuanshen.site/showcases/web-accessibility). GitHubbers are free to add more projects to the showcase.

## Manual testing

### Screen reader

To use VoiceOver, the built-in screen reader for Mac, just hit <kbd>⌘</kbd> + <kbd>F5</kbd>. This will start voice narration and display most of the spoken information in a text box.

Use <kbd>alt</kbd> + <kbd>control</kbd> + <kbd>left/right</kbd> to navigate a web page. <kbd>alt</kbd> + <kbd>control</kbd> + <kbd>space</kbd> to click on an element. For more help with VoiceOver, check out the built-in tutorial in `System Preferences > Accessibility > VoiceOver > Open VoiceOver Training`.

Keep in mind that behaviors in different screen readers can differ when navigating the same web page; just like designing for different browsers, when it comes to accessibility, it is not just the implementation of the browsers that can be different, but also the ones of assistive softwares.

## User support

Accessibility is a priority for us, if you ever encounter accessibility related issues when using yuanshen.site, please don’t hesitate to reach out to us via email us at [yuanshenmap@gmail.com](mailto:yuanshenmap@gmail.com), we will try our best to assist.
