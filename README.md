# dark-mode-toggle-flashing-fix

## Background

Looking for a small library for toggling dark mode on a website, I found
Google's
[dark-mode-toggle](https://github.com/GoogleChromeLabs/dark-mode-toggle) which
is fairly popular. However, I noticed that it results in a suboptimal user
experience due to flashing when the page loads. This issue was reported several
times:

* [Issue #77 - Reloading in the demo in dark mode still causes an initial flash
  of the light
  theme.](https://github.com/GoogleChromeLabs/dark-mode-toggle/issues/77)
* [Issue #35 - dark mode switch
  error](https://github.com/GoogleChromeLabs/dark-mode-toggle/issues/35)
* [Issue #25 - Flashes when system prefers dark, but light theme is
  toggled](https://github.com/GoogleChromeLabs/dark-mode-toggle/issues/25)
* [Issue #20 - Flash of wrong
  styles](https://github.com/GoogleChromeLabs/dark-mode-toggle/issues/20)

## The reason for the issue

`dark-mode-toggle` is a JavaScript module, and [modules are deferred by
default](https://v8.dev/features/modules#defer). While they can be defined as
`async` instead, resulting in an earlier execution, they can't block the HTML
rendering altogether like regular non-module scripts can.

## The proposed fix

If you prefer to keep using `dark-mode-toggle`, you can add an additional script
to apply the saved theme before the page is rendered.

tl;dr having this per the `dark-mode-toggle` instructions:

```html
<head>
  <link rel="stylesheet" href="common.css">
  <link rel="stylesheet" href="light.css" media="(prefers-color-scheme: light)">
  <link rel="stylesheet" href="dark.css" media="(prefers-color-scheme: dark)">
  <script type="module" src="https://googlechromelabs.github.io/dark-mode-toggle/src/dark-mode-toggle.mjs"></script>
</head>
<!-- ... -->
<aside>
  <dark-mode-toggle
    id="dark-mode-toggle-1"
    legend="Theme Switcher"
    appearance="switch"
    dark="Dark"
    light="Light"
    remember="Remember this"
  ></dark-mode-toggle>
</aside>
```

Wrap the stylesheet tags with `<noscript
id="dark-mode-toggle-stylesheets">...</noscript>` and add a small loader script
as following:

```html
<head>
  <link rel="stylesheet" href="common.css">
  <noscript id="dark-mode-toggle-stylesheets">
    <link rel="stylesheet" href="light.css" media="(prefers-color-scheme: light)">
    <link rel="stylesheet" href="dark.css" media="(prefers-color-scheme: dark)">
  </noscript>
  <script src="dark-mode-toggle-stylesheets-loader.min.js"></script>
  <script type="module" src="https://googlechromelabs.github.io/dark-mode-toggle/src/dark-mode-toggle.mjs"></script>
</head>
<!-- ... -->
<aside>
  <dark-mode-toggle
    id="dark-mode-toggle-1"
    legend="Theme Switcher"
    appearance="switch"
    dark="Dark"
    light="Light"
    remember="Remember this"
  ></dark-mode-toggle>
</aside>
```

## Demo

To see the issue and the fix in action, switch to the theme other than the one
your system prefers and reload the page.

* [Plain usage of `dark-mode-toggle`, without the
  fix](https://m417z.github.io/dark-mode-toggle-flashing-fix/with-flashing.html)
* [`dark-mode-toggle` with the
  fix](https://m417z.github.io/dark-mode-toggle-flashing-fix/without-flashing.html)
