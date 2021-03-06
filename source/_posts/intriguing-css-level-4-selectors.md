---
title: Интригующие селекторы 4-го уровня CSS
date: 2019-12-10 21:13:54
tags:
categories: css
---

Вольный перевод статьи [Intriguing CSS Level 4 Selectors](https://webdesign.tutsplus.com/tutorials/intriguing-css-level-4-selectors--cms-29499)

CSS селекторы массово прогрессировали на протяжении многих лет и давали все новые возможности разработчикам, для построения своих страниц. Примеры в этой статье являются частью спеки о селекторах 4го уровня CSS. Давайте рассмотрим лучше 7 интригующих селекторов, некоторые из которых я уверен вы использовали на практике.

## Селекторы 4-го уровня

[4-й уровень](https://www.w3.org/TR/selectors-4/) содержит знакомые псевдо-классы такие как `nth-child`, `last-child` и другие.

Спецификация описывает много нового, а также некоторые крайне удобные псевдо-классы и псевдо-элементы, по этой теме мы быстро разберемся в чем разница между одиночным и двойным двоеточиями.

## Одиночное vs Двойного двоеточия

На данный момент, главным правилом считается: двойное двоеточие `::` следует использовать вместо одиночного `:`, чтобы различать псевдо-классы и псевдо-элементы.

[Спецификация W3C по псевдо-элементам](https://www.w3.org/TR/selectors-3/#pseudo-elements)

![](/assets/images/semicolons.png)

## Псевдо-классы vs Псевдо-элементы

Псевдо-класс всегда начинается с одного двоеточия `:`, затем идет название псевдо-класса (так называемый keyword), далее по возможности идут круглые скобки в которые передают один или несколько аргументов (как CSS функции)

> Псевдо-классы позволяют применить стили к элементу не только по отношению к древу документа, но и по отношению внешних факторов таких как “история браузера” (`:visited`, к примеру ), статус содержимого (`:checked`, на определенных элементах форм), расположение мыши (`:hover`, который позволяет узнать, над элементом ли мышь или нет) - [MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-classes)

Псевдо-элементы предоставляют нам возможность, дополнять контент текстом, которого не существует в исходном документе, это такие псевдо-элементы как `::before` и `::after`

Теперь, когда мы понимаем, как все это работает давайте, уже перейдем к самому интересному.

## 1. `:matches()`

`:matches()` безусловно очень мощный селектор 4 уровня, но браузеры все еще дискутируют на тему поддержки этого псевдо-класса, за из исключением Safari (WebKit)

```css
/* Chrome */
:-webkit-any(:hover, :focus, :active) {
  color: #222;
}
 
/* Firefox */
:-moz-any(:hover, :focus, :active) {
  color: #222;
}
 
/* Safari */
:matches(:hover, :focus, :active) {
  color: #222;
}
```

Этот сниппет объединяет 3 состояния: `:hover`, `:focus`, `:active` в один селектор, но это все равно не раскрывает полный потенциал этого псевдо-класса.

Если вы знакомы с нестингом(nesting) в препроцессорах, то вас приятно удивит, что теперь подобные вещи есть в ванильном CSS.

```css
:matches(section, article) :matches(h1, h2, h3, h4, h5, h6) {
  color: goldenrod;
}
 
/* равен: */
section h1,
section h2,
section h3,
section h4,
section h5,
section h6, 
article h1,
article h2,
article h3,
article h4,
article h5,
article h6 {
  color: goldenrod;
}
```

Вы даже можете объединить их вместе, что бы получить новые возможности

```css
/* Firefox */
:-moz-any(a):-moz-any(:link, :visited) {
  color: blue;
}:-moz-any(a):-moz-any(:hover, :focus, :active) {
  color: red;
  text-decoration: none;
}
 
/* Chrome */
:-webkit-any(a):-webkit-any(:link, :visited) {
  color: blue;
}
:-webkit-any(a):-webkit-any(:hover, :focus, :active) {
  color: red;
  text-decoration: none;
}
 
/* Safari */
:matches(a):matches(:link, :visited) {
  color: blue;
}:matches(a):matches(:hover, :focus, :active) {
  color: red;
  text-decoration: none;
}
```

Этот сниппет, эквивалентен этому:

```css
a:link,
a:visited {
  ...
}
 
a:hover,
a:focus,
a:active {
  ...
}
```
Имейте введу, что `:matches()` не работает с нестингом (`:matches(:matches())`), так же не работает с псевдо-селектором `:not` (`:matches(:not())`, `:not(:matches())`).

К сожалению, поддержка этого псевдо-класса есть только в Safari, а в других браузерах поддержка до сих пор под префиксами, так же вы должны использовать разные имена одного и того же псевдо-класса плюс вендорные префиксы. Сейчас, инструменты такие как Autoprefixer не поддерживают `matches()`. Но не стоит беспокоиться, недавно я попросил решить эту проблему через репозиторий на GitHub.

[CanIUse](http://caniuse.com/#feat=css-matches-pseudo)