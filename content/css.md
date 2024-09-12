---
author: Clement
title: "CSS"
date: 2024-09-12T16:33:29-03:00
---

This page gathers the differents CSS specifications and their current implementations in _DragonFly_.

## CSS2

You can find the specifications [here](https://www.w3.org/TR/2011/REC-CSS2-20110607/#minitoc)

| CSS Property            | Is Implemented |
| ----------------------- | -------------- |
| `azimuth`               | ❌             |
| `background-attachment` | ❌             |
| `background-color`      | ❌             |
| `background-image`      | ❌             |
| `background-position`   | ❌             |
| `background-repeat`     | ❌             |
| `background`            | ❌             |
| `border-collapse`       | ❌             |
| `border-color`          | ✅             |
| `border-spacing`        | ❌             |
| `border-style`          | ❌             |
| `border-top`            | ✅             |
| `border-right`          | ✅             |
| `border-bottom`         | ✅             |
| `border-left`           | ✅             |
| `border-top-color`      | ✅             |
| `border-right-color`    | ✅             |
| `border-bottom-color`   | ✅             |
| `border-left-color`     | ✅             |
| `border-top-style`      | ❌             |
| `border-right-style`    | ❌             |
| `border-bottom-style`   | ❌             |
| `border-left-style`     | ❌             |
| `border-top-width`      | ✅             |
| `border-right-width`    | ✅             |
| `border-bottom-width`   | ✅             |
| `border-left-width`     | ✅             |
| `border-width`          | ❌             |
| `border`                | ✅             |
| `bottom`                | ❌             |
| `caption-side`          | ❌             |
| `clear`                 | ❌             |
| `clip`                  | ❌             |
| `color`                 | ✅             |
| `content`               | ❌             |
| `counter-increment`     | ❌             |
| `counter-reset`         | ❌             |
| `cue-after`             | ❌             |
| `cue-before`            | ❌             |
| `cue`                   | ❌             |
| `cursor`                | ❌             |
| `direction`             | ❌             |
| `display`               | ❌             |
| `elevation`             | ❌             |
| `empty-cells`           | ❌             |
| `float`                 | ❌             |
| `font-family`           | ❌             |
| `font-size`             | ✅             |
| `font-style`            | ❌             |
| `font-variant`          | ❌             |
| `font-weight`           | ✅             |
| `font`                  | ❌             |
| `height`                | ❌             |
| `left`                  | ❌             |
| `letter-spacing`        | ✅             |
| `line-height`           | ✅             |
| `list-style-image`      | ❌             |
| `list-style-position`   | ❌             |
| `list-style-type`       | ❌             |
| `list-style`            | ❌             |
| `margin-bottom`         | ✅             |
| `margin-left`           | ✅             |
| `margin-right`          | ✅             |
| `margin-top`            | ✅             |
| `margin`                | ✅             |
| `max-height`            | ✅             |
| `max-width`             | ✅             |
| `min-height`            | ✅             |
| `min-width`             | ✅             |
| `orphans`               | ❌             |
| `outline-color`         | ❌             |
| `outline-style`         | ❌             |
| `outline-width`         | ❌             |
| `outline`               | ❌             |
| `overflow`              | ❌             |
| `padding-bottom`        | ✅             |
| `padding-left`          | ✅             |
| `padding-right`         | ✅             |
| `padding-top`           | ✅             |
| `padding`               | ✅             |
| `page-break-after`      | ❌             |
| `page-break-before`     | ❌             |
| `page-break-inside`     | ❌             |
| `pause-after`           | ❌             |
| `pause-before`          | ❌             |
| `pause`                 | ❌             |
| `pitch`                 | ❌             |
| `pitch-range`           | ❌             |
| `play-during`           | ❌             |
| `position`              | ❌             |
| `quotes`                | ❌             |
| `richness`              | ❌             |
| `right`                 | ❌             |
| `speak-header`          | ❌             |
| `speak-numeral`         | ❌             |
| `speak-punctuation`     | ❌             |
| `speak`                 | ❌             |
| `speech-rate`           | ❌             |
| `stress`                | ❌             |
| `table-layout`          | ❌             |
| `text-align`            | ✅             |
| `text-decoration`       | ❌             |
| `text-indent`           | ❌             |
| `text-transform`        | ❌             |
| `top`                   | ❌             |
| `unicode-bidi`          | ❌             |
| `vertical-align`        | ❌             |
| `visibility`            | ❌             |
| `voice-family`          | ❌             |
| `volume`                | ❌             |
| `white-space`           | ❌             |
| `widows`                | ❌             |
| `width`                 | ❌             |
| `word-spacing`          | ❌             |
| `z-index`               | ❌             |

## CSS Selectors

You can find the specifications [here](https://www.w3.org/TR/selectors-4/)

Total CSS selectors implemented : **3/5**
❌

| CSS Selector                  | Is Implemented | Level  |
| ----------------------------- | -------------- | ------ |
| `*`                           | ✅             | 2      |
| `E`                           | ✅             | 1      |
| `E:not(s1, s2, …)`            | ❌             | 3/4    |
| `E:is(s1, s2, …)`             | ❌             | 4      |
| `E:where(s1, s2, …)`          | ❌             | 4      |
| `E:has(rs1, rs2, …)`          | ❌             | 4      |
| `E.warning`                   | ▶️             | 1      |
| `E#myid`                      | ❌             | 1      |
| `E[foo]`                      | ❌             | 2      |
| `E[foo="bar"]`                | ❌             | 2      |
| `E[foo="bar" i]`              | ❌             | 4      |
| `E[foo="bar" s]`              | ❌             | 4      |
| `E[foo~="bar"]`               | ❌             | 2      |
| `E[foo^="bar"]`               | ❌             | 3      |
| `E[foo$="bar"]`               | ❌             | 3      |
| `E[foo*="bar"]`               | ❌             | 3      |
| `E[foo\|="en"]`               | ❌             | 2      |
| `E:dir(ltr)`                  | ❌             | 4      |
| `E:lang(zh, "*-hant")`        | ❌             | 2/4    |
| `E:any-link`                  | ❌             | 4      |
| `E:link`                      | ❌             | 1      |
| `E:visited`                   | ❌             | 1      |
| `E:local-link`                | ❌             | 4      |
| `E:target`                    | ❌             | 3      |
| `E:target-within`             | ❌             | 4      |
| `E:scope`                     | ❌             | 4      |
| `E:current`                   | ❌             | 4      |
| `E:current(s)`                | ❌             | 4      |
| `E:past`                      | ❌             | 4      |
| `E:future`                    | ❌             | 4      |
| `E:active`                    | ❌             | 1      |
| `E:hover`                     | ❌             | 2      |
| `E:focus`                     | ❌             | 2      |
| `E:focus-within`              | ❌             | 4      |
| `E:focus-visible`             | ❌             | 4      |
| `E:enabled`                   | ❌             | 3      |
| `E:disabled`                  | ❌             | 3      |
| `E:read-write`                | ❌             | 3-UI/4 |
| `E:read-only`                 | ❌             | 3-UI/4 |
| `E:placeholder-shown`         | ❌             | 3-UI/4 |
| `E:default`                   | ❌             | 3-UI/4 |
| `E:checked`                   | ❌             | 3      |
| `E:indeterminate`             | ❌             | 4      |
| `E:valid`                     | ❌             | 3-UI/4 |
| `E:invalid`                   | ❌             | 3-UI/4 |
| `E:in-range`                  | ❌             | 3-UI/4 |
| `E:out-of-range`              | ❌             | 3-UI/4 |
| `E:required`                  | ❌             | 3-UI/4 |
| `E:optional`                  | ❌             | 3-UI/4 |
| `E:blank`                     | ❌             | 4      |
| `E:user-invalid`              | ❌             | 4      |
| `E:root`                      | ❌             | 3      |
| `E:empty`                     | ❌             | 3      |
| `E:nth-child(n [of S]?)`      | ❌             | 3/4    |
| `E:nth-last-child(n [of S]?)` | ❌             | 3/4    |
| `E:first-child`               | ❌             | 2      |
| `E:last-child`                | ❌             | 3      |
| `E:only-child`                | ❌             | 3      |
| `E:nth-of-type(n)`            | ❌             | 3      |
| `E:nth-last-of-type(n)`       | ❌             | 3      |
| `E:first-of-type`             | ❌             | 3      |
| `E:last-of-type`              | ❌             | 3      |
| `E:only-of-type`              | ❌             | 3      |
| `E F`                         | ❌             | 1      |
| `E > F`                       | ❌             | 2      |
| `E + F`                       | ❌             | 2      |
| `E ~ F`                       | ❌             | 3      |
| `F \|\| E`                    | ❌             | 4      |
| `E:nth-col(n)`                | ❌             | 4      |
| `E:nth-last-col(n)`           | ❌             | 4      |

## Text Decoration

| CSS Property              | Is Implemented |
| ------------------------- | -------------- |
| `text-decoration-line`    | ❌             |
| `text-decoration-style`   | ❌             |
| `text-decoration-color`   | ❌             |
| `text-decoration`         | ❌             |
| `text-underline-position` | ❌             |
| `text-emphasis-style`     | ❌             |
| `text-emphasis-color`     | ❌             |
| `text-emphasis`           | ❌             |
| `text-emphasis-position`  | ❌             |
| `text-shadow`             | ❌             |

## Text

| CSS Property          | Is Implemented |
| --------------------- | -------------- |
| `text-transform`      | ❌             |
| `white-space`         | ❌             |
| `tab-size`            | ❌             |
| `word-break`          | ❌             |
| `line-break`          | ❌             |
| `hyphens`             | ❌             |
| `overflow-wrap`       | ❌             |
| `text-align`          | ✅             |
| `text-align-all`      | ❌             |
| `text-align-last`     | ❌             |
| `text-justify`        | ❌             |
| `word-spacing`        | ❌             |
| `letter-spacing`      | ✅             |
| `text-indent`         | ❌             |
| `hanging-punctuation` | ❌             |
