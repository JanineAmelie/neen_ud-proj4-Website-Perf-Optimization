**Udacity FrontEnd Nanodegree Proj 4**
Website Optimization
---

### Table of Contents
1. [Project Overview](#project-overview)
2. [Project Information](#project-information)
3. [Optimizations for Cam's Portfolio](#optimizations-for-cams-portfolio)
4. Optimizations for Cam's Pizzeria
->  5. [Main.js](#js-optimizations-for-cams-pizzeria)
->  6.  [Styles.css](#css-optimizations-for-cams-pizzeria)
7. [How to Run](#how-to-run)
  8. [Setting up a LocalHost server](#creating-a-localhost)
  9. [Creating a secure tunnel (url) to your LocalHost](#using-ngrok-for-a-secure-web-tunnel)
  10. [View Live](https://janinelourens.github.io/neen_ud-proj4-Website-Perf-Optimization/)

---

### Project-Overview

My task was to optimize a  website provided by [Udacity](http://udacity.com/)  with a number of optimization and performance-related issues so that it achieves a target PageSpeed score and runs at 60 frames per second to produce the most enjoyable user experience.

I utilized my knowledge of the Critical Rendering Path and used Chrome Developer Tools to review the current state of the various pages of the application to identify Jank and Bottleneck Problem areas in the code.  I iteratively went over each block of code to make changes and test those changes if they were a performance gain or loss.

---

### Project-Information

- **Tools:**
  -  Google Developer Tools: Timeline.
  - [GTmetrix](https://gtmetrix.com/) and [Google Pagespeed Insights](https://developers.google.com/speed/pagespeed/insights/)  were utilized to benchmark the site.

- **Resources:**
  -  Google: [Critical Rendering Path](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/)
  - Google: [Pagespeed Insights docs](https://developers.google.com/speed/docs/insights/rules)

- **objectives:**
  - Time to resize pizzas is less than 5 ms using the pizza size slider on the views/pizza.html page. Resize time is shown in the browser developer tools.
  - Optimizations made to views/js/main.js make views/pizza.html render with a consistent frame-rate at 60fps when scrolling.


---

### **Optimizations-for-Cams-Portfolio**
### index.html

**Before optimization:**
*Google Page Speed Insights Scores:* Mobile 76, Desktop 89

**After optimization:**
*Google Page Speed Insights Scores:* Mobile & Desktop 96

![PSI Results](https://www.dropbox.com/s/6w1vtgmgvmi33zv/4_psi.gif?raw=1)

#### **Steps taken:**

- Eliminated render-blocking JavaScript and CSS in above-the-fold content by adding async / defer and moving js scripts to the bottom. And Open Sans web fonts to the bottom.
- Inlined the main style.css into the top of index.html and [minified it.](http://htmlcompressor.com/compressor/)
- Image Optimizations:
  -  Tools used: Adobe Photoshop and [Optimizilla.com](http://optimizilla.com/)
  -  Created a thumbnail for images/pizzeria.jpg and resized from 2048x1536 (2.25mb) to 173 x 30 (7.83kb) reducing the size from 2.25mb to 135kb
- [Minified](http://htmlcompressor.com/compressor/) index.html
- Inlined js/perfmatters and [minified it.](http://htmlcompressor.com/compressor/)

**TODO: Optimizations that could net a 100/100 but would require a server/hosting/apache:**

 - Leverage browser caching
 - Reduce server response time
 - Enable gzip compression

---

### **JS-Optimizations-for-Cams-Pizzeria**
## (views/main.js)

**!IMPORTANT!!:  All benchmarks were done with a clean install of google chrome canary in incognito mode. add-ons / extensions might affect performance benchmarks of any kind.**

**Before optimization:**

- *Time to Generate Pizzas on Load:*  8.44 ms
- *Time to Resize Pizzas:*
  - med to sm:   63.085ms
  - sm to med: 62.934 ms
  - med to large: 73.149 ms
- *Average Scripting time to generate last 10 frames:* 16.73 ms


**After optimization:**

- *Time to Generate Pizzas on Load:* 7.16 ms
- *Time to Resize Pizzas:*
  - med to sm:   0.445ms
  - sm to med: 0.435 ms
  - med to large: 0.294ms
- *Average Scripting time to generate last 10 frames:* 0.12 ms

**Results:**

#### **✓ Time to resize pizzas is less than 5 ms using the pizza size slider:**

![enter image description here](https://www.dropbox.com/s/tczag5dhuevmkv7/4_time-to-resize-pizzaslider.gif?raw=1)

#### **✓ Render with a consistent frame-rate at 60fps when scrolling.**

![Consistent Fps](https://www.dropbox.com/s/fvwbpscilfmd4os/4_15175527_10157868136920323_777205252_n.png?raw=1)
![Recording](https://www.dropbox.com/s/jrph8j5c1vhuybh/4_scroll-fps.gif?raw=1)

**Steps Taken:**
*note: All code not relevant to optimization in view/main.js has been minified for better readability of code that has been optimized.*

1. All Instances of **.querySelector()** have been replaced with **.getElementByID() & .getElementsByClassName().**

2. **changeSliderLabel()** 's case switch has a very repetitive use of querySelectors for each switch case thus, it has been called once and stored in a variable.

```javascript
\\OLD:
function changeSliderLabel(size) {
  switch (size) {
      case "1":
          document.querySelector("#pizzaSize").innerHTML = "Small";
          return;
      case "2":
          document.querySelector("#pizzaSize").innerHTML = "Medium";
          return;
      case "3":
          document.querySelector("#pizzaSize").innerHTML = "Large";
          return;
      default:
          console.log("bug in changeSliderLabel");
  }
}

\\NEW:
function changeSliderLabel(size) {
    var pizzaSizeId = document.getElementById('pizzaSize');

    switch (size) {
        case "1":
            pizzaSizeId.innerHTML = "Small";
            return;
        case "2":
            pizzaSizeId.innerHTML = "Medium";
            return;
        case "3":
            pizzaSizeId.innerHTML = "Large";
            return;
        default:
            console.log("bug in changeSliderLabel");
    }
}


```

3. **DetermineDx() :** Has been removed.

4. **changePizzaSizes(size) :**  newWidth is now a percentage value that will be concatenated, with a '%' symbol instead of calculating it to a pixel value with determineDx(). It now features a simple for loop that sets the width of the element.

```javascript
\\OLD:
function changePizzaSizes(size) {
  for (var i = 0; i < document.querySelectorAll(".randomPizzaContainer").length; i++) {
      var dx = determineDx(document.querySelectorAll(".randomPizzaContainer")[i], size);
      var newwidth = (document.querySelectorAll(".randomPizzaContainer")[i].offsetWidth + dx) + 'px';
      document.querySelectorAll(".randomPizzaContainer")[i].style.width = newwidth;
  }
}

\\NEW:
for (var i = 0; i < randomPizzas.length; i++) {
    randomPizzas[i].style.width = newWidth + '%';
}
```

5. **updatePositions():**  Removed the repetitive computation outside the for-loop. It only needs to run 5 calculations so there is no need calculate scroll position times items.length. Created a lookup table using map data-structure to reduce Math.sin() calculations and improve performance.

```javascript
\\OLD:
function updatePositions() {
    frame++;
    window.performance.mark("mark_start_frame");
    var items = document.querySelectorAll('.mover');

    for (var i = 0; i < items.length; i++) {
        var phase = Math.sin((document.body.scrollTop / 1250) + (i % 5));
        items[i].style.left = items[i].basicLeft + 100 * phase + 'px';
    }
}

\\NEW:
function updatePositions() {
    frame++;
    window.performance.mark("mark_start_frame");
    var items = document.getElementsByClassName('mover');
    var scrollPosition = document.body.scrollTop / 1250;
    var phaseMap = new Map();

    for (var k = 0; k < 5; k++) {
        phaseMap.set(k, Math.sin(scrollPosition + (k % 5)));
    }

    for (var i = 0; i < items.length; i++) {
        var phase = phaseMap.get(i % 5);
        items[i].style.left = items[i].basicLeft + 100 * phase + 'px';
    }
}

```

6. **DOMContentLoaded Event Listener: ** Removed static values out of the loop and assigned them to variables to passed to the elem object's properties. Reduced the number of pizzas from 200 to 15.

```javascript
\\OLD:
document.addEventListener('DOMContentLoaded', function() {
    var cols = 8;
    var s = 256;
    for (var i = 0; i < 200; i++) {
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

\\NEW:
document.addEventListener('DOMContentLoaded', function() {
    var cols = 8;
    var s = 256;
    var pizHeight = "100px";
    var pizWidth = "73.333px";
    var pizSrc = "images/pizza.png";
    var pizCls = 'mover';
    var mvingPizDiv = document.getElementById("movingPizzas1")

    for (var i = 0; i < 15; i++) {
        var elem = document.createElement('img');
        elem.className = pizCls;
        elem.src = pizSrc;
        elem.style.height = pizHeight;
        elem.style.width = pizWidth;

        elem.basicLeft = (i % cols) * s;
        elem.style.top = (Math.floor(i / cols) * s) + 'px';
        mvingPizDiv.appendChild(elem);

    }
    updatePositions();
});

```

### **CSS-Optimizations-for-Cams-Pizzeria**
### views/css/style.css

1. Minified bootstrap grid
2. Added properites to the mover class in the css file to reduce repaint times, and place each element on its own layer.
```css
.mover {
    position: fixed;
    width: 256px;
    -webkit-backface-visibility: hidden;
    backface-visibility: hidden;
    -webkit-transform: translate3d(0, 0, 0);
    -ms-transform: translate3d(0, 0, 0);
    transform: translate3d(0, 0, 0);
    z-index: -1;
}
```

---
### How-to-Run:

#### **Getting the project on your system (viewing offline):**

1. Clone, Fork or Download the ZIP file of this project into a folder on your local machine.
2. Make sure you are connected to the net.
3. Unzip the file using Winrar / 7zip etc.
4. Double click the index.html file and open with your favorite browser.

#### **For Testing Pagespeed Insights with a localhost:**
1. Follow Steps 1-3 in the previous section.
3. Setup a Localhost Server (see below)
4. Run NGROK to create a secure tunnel to publish the local web server to the internet (see how below).
5. Make sure you have your correct link to paste into PageSpeed Insights it should be:

> http:// your-ngrok-link.ngrok.io/folder-name-in-localhost

5. Go to Google's [PageSpeedInsights](https://developers.google.com/speed/pagespeed/insights/), paste the link generated with NGROK and Analyze the results.

#### **Testing Live:**
1. View the site live here: https://janinelourens.github.io/neen_ud-proj4-Website-Perf-Optimization/
(achieves a PSI score of 96 & 97!!)

#### **How to measure FPS at views/pizza.html:**
See tutorial here at [developers.google.com](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance/timeline-tool)

----

#### **Creating-a-LocalHost**
Create a web server using your favorite method or if you don't know how, an easy way is to use XAMPP or Python's simpleserver

##### **Using XAMPP **
Follow this [tutorial](https://blog.udemy.com/xampp-tutorial/)

or

##### **Creating a Python 'SimpleHTTPServer**
1. Install [Python](https://www.python.org/downloads/)
2. Start a Localhost
```bash
  $> cd /path/to/your-project-folder
  $> python -m SimpleHTTPServer 8080  (if python version = 2)
  OR
  $> python -m http.server 8080  (if python version = 3)
```

#### **Using-Ngrok-for-a-secure-web-tunnel**
1. go to [ngrok](https://ngrok.com/download) and download.
2. Navigate to your project folder:
```bash
$> cd /path/to/your-project-folder
```
3.  Run the following command:
```bash
$> ngrok http 8080
```
