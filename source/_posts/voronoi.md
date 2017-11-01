---
title: Sarah's Interactive Voronoi Diagram
date: 2017-11-01 10:35:00
categories:
    - Algorithm
tags:
    - Algorithm
    - Interactive
---

![Voronoi_Matt](/images/voronoi/Voronoi_Matt.png)

My friend and colleague, Sarah Yvonne Greer, is currently in her senior year.  She used her passion on llamas to design her cool cozy [personal website](http://sygreer.com).  It contains some very interesting mathematical visualization projects, including this interactive [Voronoi diagram](http://sygreer.com/projects/voronoi).

<!-- more -->

Her work was linked on the Agile Scientific's blog post [The norm: kings, crows and taxicabs](https://agilescientific.com/blog/2017/10/4/kings-crows-and-taxicabs) by Matt Hall; the figure in the very beginning is from that post. She also got a chance to join the Undersampled Radio show [Game, Set, Match](https://undersampledrad.io/home/2017/10/game-set-match) recently.

Unlike she having a dedicated server in Oregon, I host this website in [Git Pages](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&ved=0ahUKEwjOoYjAmJ7XAhUF0YMKHSadBP8QFggoMAA&url=https%3A%2F%2Fpages.github.com%2F&usg=AOvVaw0rFEJ-mlO9IotpcBjyXtQl) for free. Therefore I am limited to using **static pre-generated web pages**. The architecture that I am using now is [Hexo](https://hexo.io). Yet I wanted to explore the interaction feature out of its poor Javascript support. After tiny teeny adoptions, I managed to brutally reproduce it here.


{% raw %}
<script type="text/javascript" src="/lib/processingjs/processing.js"></script>
<script type="text/javascript">
    function setNorm(norm) {
        var pjs = Processing.getInstanceById('voronoi');
        pjs.updatePoints(norm); }

    function reset() {
        var pjs = Processing.getInstanceById('voronoi');
        pjs.clearBoard(); }
</script>

<center>
<canvas id='voronoi' data-processing-sources="/scripts/voronoi/voronoi.pde"></canvas>
<br>
<div class='text-center'>
    <a class="btn" onclick="setNorm(1)" rel="contents"> Manhattan </a>
    <a class="btn" onclick="setNorm(2)" rel="contents"> Euclidean </a>
    <a class="btn" onclick="setNorm(3)" rel="contents"> Chebyshev </a>
    <a class="btn" onclick="reset()" rel="contents"> Clear </a>
</div>
</center>
{% endraw %}

### About

You can read more about this interactive Voronoi diagram on [Wolfram MathWorld](http://mathworld.wolfram.com/VoronoiDiagram.html) and [Wikipedia](https://en.wikipedia.org/wiki/Voronoi_diagram). In this demonstration, there are 3 different metrics. The table below describes them. Essentially, these metrics represent $L_1$, $L\_2$, and $L\_\infty$ distance, respectively, between the point of interest $\mathbf{a}=(x_1,\,y_1)$ (denoted by black dots on the above display) and any other point $\mathbf{b}=(x_2,\,y_2)$ on the plane.

| Name               | Measurement                                                                                   |
|--------------------|-----------------------------------------------------------------------------------------------|
| Manhattan distance | $\Vert\mathbf{b}-\mathbf{a}\Vert_1 = \vert x_2-x_1\vert + \vert y_2-y_1\vert$                 |
| Euclidean distance | $\Vert\mathbf{b}-\mathbf{a}\Vert_2 = \sqrt{(x_2-x_1)^2 + (y_2-y_1)^2}$                        |
| Chebyshev distance | $\Vert\mathbf{b}-\mathbf{a}\Vert_\infty = \max{(\vert x_2-x_1\vert,\,\vert y_2-y_1\vert)}$ |

### Instructions

* Click anywhere on the canvas above to add a point.
* Use the buttons below the canvas to change the measured metric and clear the display.

Alternatively, you can press `1` to use the Manhattan distance, `2` to use the Euclidean distance, `3` to use the Chebyshev distance, `n` to toggle through all metrics, and `R` to clear the display.

### Here's a bug!

#### What?

Weirdly, I found a very subtle bug that only occurs here but not in Sarah's original page. You can follow the instructions below to reproduce this bug in the next demo (I fixed this bug in the first demo).

1. Click on the diagram to see the result;
2. Click the button to change metric norm (Do NOT use shortcut keys! Bug only happens when clicking buttons);
3. Click the diagram again, you will see incorrect small circles instead of correct segmentations.

{% raw %}
<script type="text/javascript" src="/lib/processingjs/processing.js"></script>
<script type="text/javascript">
    function setNorm_old(norm) {
        var pjs = Processing.getInstanceById('voronoi_old');
        pjs.updatePoints(norm); }

    function reset_old() {
        var pjs = Processing.getInstanceById('voronoi_old');
        pjs.clearBoard(); }
</script>

<center>
<canvas id='voronoi_old' data-processing-sources="/scripts/voronoi/voronoi_old.pde"></canvas>
<br>
<div class='text-center'>
    <a class="btn" onclick="setNorm_old(1)" rel="contents"> Manhattan </a>
    <a class="btn" onclick="setNorm_old(2)" rel="contents"> Euclidean </a>
    <a class="btn" onclick="setNorm_old(3)" rel="contents"> Chebyshev </a>
    <a class="btn" onclick="reset_old()" rel="contents"> Clear </a>
</div>
</center>
{% endraw %}

#### Solution
I found that it's related to the function `updatePoints` from the code [`voronoi.pde`](http://sygreer.com/projects/voronoi/voronoi.pde).
```java
...
int n;                   // metric flag
...
// update points on the plane
void updatePoints(int n){
    for (int i = points.size()-1; i >= 0; i--) {
        Point p = points.get(i);
        p.changeMetric(n);
    }
    redraw();
}
...
```
Notice that `int n` is declared as a global variable; however, in `updatePoints` it was only called as a local dummy variable. It leads to a very dangerous ambiguity: how should these two variables with the same name but different scope be related? Often this is an environment-dependent result. Eventually I fixed it by explicitly update the value of the global variable `int n`:
```java
void updatePoints(int n_new){
    for (int i = points.size()-1; i >= 0; i--) {
        Point p = points.get(i);
        p.changeMetric(n_new);
    }
    n = n_new
    redraw();
}
```

If you haven't gotten bored yet, go back to the first interactive demo and see whether the bug is solved.

### Source code
You can download the source code from Sarah's website [here](http://sygreer.com/projects/voronoi/sygreer_voronoi.zip) (ZIP file, 20kB). You will need [Processing.js](http://processingjs.org/) to run the program, not the [Processing software package](https://processing.org/) she mentioned. <img id="github-emoji" src="https://assets-cdn.github.com/images/icons/emoji/unicode/1f620.png?v7" height="20" width="20"> <img id="github-emoji" src="https://assets-cdn.github.com/images/icons/emoji/unicode/1f624.png?v7" height="20" width="20">
