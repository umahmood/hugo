+++
date = "2016-08-16T19:54:44+01:00"
draft = false
title = "SVG Sprites"
hideToc = true

+++

Within an single SVG file we can define many sprites. This consists of merging all your SVG sprites into a single .svg image file. Every sprite is wrapped in a 'symbol' tag, like this:

```
<svg class="character" width="100pt" height="100pt" version="1.1" xmlns="http://www.w3.org/2000/svg">
    <symbol id="circle-red" viewBox="0 0 100 100">
      <circle cx="50" cy="50" r="40" stroke="black" stroke-width="3" fill="red" />
    </symbol>
    <symbol id="circle-black" viewBox="0 0 100 100">
      <circle cx="50" cy="50" r="40" stroke="black" stroke-width="3" />
    </symbol>
</svg>
```

We can then use HTML or CSS to pick out each part of the image:

```
<hmtl>
  <body>
     <svg class="c-red" >
            <use xlink:href="test.svg#circle-red"></use>
        </svg>
        <svg class="c-black" >
            <use xlink:href="test.svg#circle-black"></use>
        </svg>
  </body>
</hmtl>
```

We can animate the sprite with CSS:

```
<style type="text/css">
    .c-black:hover {
        fill: #fe2fd0;
    }
<style>
```

If creating an SVG sprite file seems tedious or error prone. You can use a tool like [gulp-svgstore](https://github.com/w0rm/gulp-svgstore) to automate the process. And generate a single SVG file from your individual sprite files.

One of the advantages of using SVG sprites are the improved page load times. One of the disadvantages of using SVG sprites, is that when linking the 'use' tag to the 'symbol' tag, the image gets injected into the [Shadow DOM](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Shadow_DOM). Meaning we lose some CSS capabilities, and cannot apply some styling to the SVG image.

Fin.
