---
title: 'Hello world'
categories: miscel
excerpt: 'This is an excerpt =) Just writing something here for testing purposes.'
---

## Some testing

Hello world!

Testing mathjax:
$$e^{2\pi i kx}$$

## How this is built

We fork the Minimal Mistakes repo and use it to generate static content into a Github Pages repo. We do **not** need Github Pages support for Jekyll.

Remember to add the following to `_includes/head/custom.html` for the LaTeX to work:

```javascript
<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.2/MathJax.js?config=TeX-MML-AM_CHTML">
</script>
```