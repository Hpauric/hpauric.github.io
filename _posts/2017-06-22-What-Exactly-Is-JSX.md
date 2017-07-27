---
published: true
---

JSX  will never be part of the official Javascript canon. [Quote the website](https://facebook.github.io/jsx/):

> **It's NOT a proposal to incorporate JSX into the ECMAScript spec itself**. It's intended to be used by various preprocessors (transpilers) to transform these tokens into standard ECMAScript.

> Why not Template Literals? 
ECMAScript 6th Edition (ECMA-262) introduces template literals which are intended to be used for embedding DSL in ECMAScript. Why not just use that instead of inventing a syntax that's not part of ECMAScript?

> Template literals work well for long embedded DSLs. Unfortunately the syntax noise is substantial when you exit in and out of embedded arbitrary ECMAScript expressions with identifiers in scope.