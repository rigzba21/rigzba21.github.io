---
layout: post
title:  "MathJax Syntax and Gaussian Elimination"
date:   2018-08-24 16:26:48 -0400
categories: jekyll update
---

Here is a quick example of [MathJax](https://www.mathjax.org/), which is a javascript library that uses Tex-like syntax for displaying mathematical equations. I don't use use MathJax or Tex syntax very much, except for notes in LaTex, so I usually always have to follow an example.

#### Including MathJax Support
Head over to the [Jekyll Docs](https://jekyllrb.com/docs/extras/) _extras_ page, and copy the CDN `<script></script>` tag and add it to the `default.html` file in the `_layouts` directory.

Here's mine for this site

{%highlight xml %}
<!DOCTYPE html>

<html>
<script src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.0/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
  {%- include head.html -%}

<!-- rest of content for default.html -->

</html>

{% endhighlight %}

Now you can use the [Kramdown/MathJax](https://kramdown.gettalong.org/syntax.html#math-blocks) syntax in your markdown files.

### Gaussian Elimination

_Gaussian Elimination_ is a handy way to calculate the _inverse matrix_ of a given linear system.

Given the linear system:  $$ 3x_1  +1x_2 = 1 \\ 1x_1 + 2x_2 = 0 $$, find the inverse matrix.


>MathJax syntax: ```$$ 3x_1  +1x_2 = 1 \\ 1x_1 + 2x_2 = 0 $$```

#### Represent Linear Equation as a Matrix:

$$ \left[\begin{matrix} 3 & 1 & 1 \\\ 1 & 2 & 0 \end{matrix}\right] $$

>MathJax syntax: ```$$ \left[\begin{matrix} 3 & 1 & 1 \\\ 1 & 2 & 0 \end{matrix}\right] $$```

#### Multiply Top Row by $$ 2 $$

$$ \left[\begin{matrix} 6 & 2 & 2 \\\ 1 & 2 & 0 \end{matrix}\right] $$

>MathJax syntax: ```$$ \left[\begin{matrix} 6 & 2 & 2 \\\ 1 & 2 & 0 \end{matrix}\right] $$
```

#### Subtract Bottom Row From Top Row

$$ \left[\begin{matrix} 5 & 0 & 2 \\\ 1 & 2 & 0 \end{matrix}\right] $$

>MathJax syntax: ```$$ \left[\begin{matrix} 5 & 0 & 2 \\\ 1 & 2 & 0 \end{matrix}\right] $$```

#### Multiply Bottom Row by $$ 5 $$

$$ \left[\begin{matrix} 5 & 0 & 2 \\\ 5 & 10 & 0 \end{matrix}\right] $$

>MathJax syntax: ```$$ \left[\begin{matrix} 5 & 0 & 2 \\\ 5 & 10 & 0 \end{matrix}\right] $$
```

#### Subtract Top Row from Bottom Row

$$ \left[\begin{matrix} 5 & 0 & 2 \\\ 0 & 10 & -2 \end{matrix}\right] $$

>MathJax syntax: ```$$ \left[\begin{matrix} 5 & 0 & 2 \\\ 0 & 10 & -2 \end{matrix}\right] $$
```

#### Divide Top Row by $$5$$ and Bottom Row by $$10$$

$$ \left[\begin{matrix} 1 & 0 & \frac{2}{5} \\\ 0 & 1 & \frac{-2}{10} \end{matrix}\right] $$

>MathJax syntax: ```$$ \left[\begin{matrix} 1 & 0 & \frac{2}{5} \\\ 0 & 1 & \frac{-2}{10} \end{matrix}\right] $$
```

#### Simplify

$$ \left[\begin{matrix} 1 & 0 & \frac{2}{5} \\\ 0 & 1 & \frac{-1}{5} \end{matrix}\right] $$

>MathJax syntax: ```$$ \left[\begin{matrix} 1 & 0 & \frac{2}{5} \\\ 0 & 1 & \frac{-1}{5} \end{matrix}\right] $$
```
