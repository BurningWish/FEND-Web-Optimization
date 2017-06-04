# Website Optimization
This is project 5 of Front End Web Developer Nanodegree Programme. Two website pages are optimized here.

## Code Usage
1. Download the repository to your desktop.
2. Unzip the folder, and open `dist/index.html` and `dist/views/pizza.html` to see the two optimized pages.

## Optimizations done in this project

### Optimization to `index.html` to reach 90 Google PSI scores.
1. Move all the `<script>` tag to the very bottom within `<body>` element.
2. `defer` the `analytics.js` script, so that this javascript will run after initial render finishs.
3. Add `Media` attribute to the `<link href='print.css'>` so that this css file will be no longer render blocking.
4. Inline the `style.css` into the HTML file to reduce the number of critical resources.
5. Delay loading of google fonts, using the techniques described [here](https://davidwalsh.name/font-loading).
6. Minify and compress the `pizzeria.jpg` file, which will be referenced by the HTML.
7. Minify the `index.html` and `perfmatter.js` files.

### Optimization to `main.js` so that resize pizza time is less than 5 ms.
The problem with very long resizing time is due to forced synchronous layout issue, caused in the `views/js/main.js`. The solution is now bettwen line 450 - 472, where I refactor the `changePizzaSizes` function to the following one.

```js
// Iterates through pizza elements on the page and changes their widths
  function changePizzaSizes(size) {

    switch(size) {
      case "1":
        newWidth = 25;
        break;
      case "2":
        newWidth = 33;
        break;
      case "3":
        newWidth = 50;
        break;
      default:
        console.log("bug in sizeSwitcher");
    }

    var randomPizzas = document.getElementsByClassName("randomPizzaContainer");

    for (var i = 0; i < randomPizzas.length; i++) {
      randomPizzas[i].style.width = newWidth + '%';
    }
  }
```

### Optimization to `main.js` so that scrolling the page doesn't cause serious FPS drop.
This is another layout thrash problem caused by repetitively reading and writing styles in a loop, in the `views/js/main.js`. Solution code is between line 521-531, which is shown as follows. The basic idea is in a frame, do a batch of style reading first, and then do a batch of style writing.
```js
  // Do a batch read, which is getting phase from each moving pizza
  var phases = [];
  var position = document.body.scrollTop / 1250;
  for (var i = 0; i < 5; i++) {
    phases[i] = Math.sin(position + (i % 5));
  }

  // Do a batch write, which is assigning a new position to each moving pizza
  for (var i = 0; i < items.length; i++) {
    items[i].style.left = items[i].basicLeft + 100 * phases[i % 5] + 'px';
  }
```

### Optimization to `main.js` so that time of geneting pizza onload can be reduced.
This problem is caused more than enough number of pizzas are rendered in the `DOMContentLoaded` event listener. I refactor the code to the following one, so that necessary number of pizza to be rendered onload will be calculated based on the user screen height. Modified code is now between line 546 - 565 of `views/js/main.js`.
```js
// Generates the sliding pizzas when the page loads.
document.addEventListener('DOMContentLoaded', function() {
  var cols = 8;
  var s = 256;
  // Calculate necessary rows of pizza, depending on screen height
  var rows = window.screen.height / s;
  // Calculate necessary pizza to be rendered, depending on cols and rows
  var onloadPizzaNumber = cols * rows;
  for (var i = 0; i < onloadPizzaNumber; i++) {
    var elem = document.createElement('img');
    elem.className = 'mover';
    elem.src = "images/pizza.png";
    elem.style.height = "100px";
    elem.style.width = "73.333px";
    elem.basicLeft = (i % cols) * s;
    elem.style.top = (Math.floor(i / cols) * s) + 'px';
    document.querySelector("#movingPizzas1").appendChild(elem);
  }
  updatePositions();
});
```

## License
This project is licensed under the terms of the **MIT** license.