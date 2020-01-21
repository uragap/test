### Creating layer 2
​
So, we have our backdrop but now we need the second layer, which sits on top of the backdrop and contains our heading and byline.  To add our second layer, let's complete the same tasks we performed for layer 1, but instead of using the `fill` template, we'll use the **`vertical`** template. However, before we go any further, let's learn about templates and how we can arrange AMP and HTML elements in an `<amp-story-grid-layer>`.
​
#### Laying out elements with a template
​
The `<amp-story-grid-layer>` element lays out its children elements in a grid (based off the [CSS grid](https://www.w3.org/TR/css-grid-1/)).  To indicate how you want the children arranged, you need to specify one of the following layout templates:
​
<table class="noborder">
<tr>
    <td colspan="2"><h5 id="fill">Template: Fill</h5></td>
</tr>
<tr>
    <td width="65%">The <strong>fill</strong> template fills the screen with the first child element in the layer. Any other children in this layer aren't shown.
​
    The fill template works well for backgrounds, including images and videos.
   <code class="nopad"><pre>&lt;amp-story-grid-layer template="fill">
  &lt;amp-img src="dog.png"
      width="720" height="1280"
      layout="responsive">
  &lt;/amp-img>
&lt;/amp-story-grid-layer></pre></code>
    </td>
    <td>
    {{ image('/static/img/docs/tutorials/amp_story/layer-fill.png', 216, 341) }}
    </td>
</tr>
<tr>
    <td colspan="2"><h5 id="vertical">Template: Vertical</h5></td>
</tr>
<tr>
    <td width="65%">The <strong>vertical</strong> template lays the children elements along the y-axis. The elements are aligned to the top of the screen, and take up the entire screen along the x-axis.
​
    The vertical template works well when you want to vertically stack elements one right after the other.
​
   <code class="nopad"><pre>&lt;amp-story-grid-layer template="vertical">
  &lt;p>element 1&lt;/p>
  &lt;p>element 2&lt;/p>
  &lt;p>element 3&lt;/p>
&lt;/amp-story-grid-layer></pre></code>
    </td>
    <td>{{ image('/static/img/docs/tutorials/amp_story/layer-vertical.png', 216, 341) }}
    </td>
</tr>
<tr>
    <td colspan="2"><h5 id="horizontal">Template: Horizontal</h5></td>
</tr>
<tr>
    <td width="65%">The <strong>horizontal</strong> template lays the children elements along the x-axis.  The elements are aligned to the start of the screen, and take up the entire screen along the y-axis.
​
    The horizontal template works well when you want to horizontally stack elements one right after the other.
​
    <code class="nopad"><pre>&lt;amp-story-grid-layer template="horizontal">
  &lt;p>element 1&lt;/p>
  &lt;p>element 2&lt;/p>
  &lt;p>element 3&lt;/p>
&lt;/amp-story-grid-layer></pre></code>
    </td>
    <td>
    {{ image('/static/img/docs/tutorials/amp_story/layer-horizontal.png', 216, 341) }}
    </td>
</tr>
<tr>
    <td colspan="2"><h5 id="thirds">Template: Thirds</h5></td>
</tr>
<tr>
<td width="65%">
The <strong>thirds</strong> template divides the screen into three equally-sized rows, and allows you to slot content into each area.
​
You can also specify a named <code>grid-area</code> to indicate which third you want your content to be in&mdash;the <code>upper-third</code>, <code>middle-third</code>, or <code>lower-third</code>. Named grid areas are useful for changing the default behavior of where elements appear.  For example, if you have two elements in the layer, you can specify the first element to be in <code>grid-area="upper-third"</code> and the second element to be in the <code>grid-area="lower-third"</code>.
​
<code class="nopad"><pre>&lt;amp-story-grid-layer template="thirds">
  &lt;h1 grid-area="upper-third">element 1&lt;/h1>
  &lt;p grid-area="lower-third">element 2&lt;/p>
&lt;/amp-story-grid-layer>
</pre></code>
</td>
<td>{{ image('/static/img/docs/tutorials/amp_story/layer-thirds.png', 216, 341) }}</td>
</tr>
</table>
​
### Completing our cover page
​
Now that you understand layer templates, let's complete our second layer for the cover page.
​
For layer 2, we want the heading and byline to be at the top, and we want the elements to follow one after the other, so we'll specify the `vertical` template. Our second `amp-story-grid-layer` follows the first, like so:
​
```html hl_lines="4 5 6 7"
<amp-story-grid-layer>
 <!--our first layer -->
</amp-story-grid-layer>
<amp-story-grid-layer template="vertical">
  <h1>The Joy of Pets</h1>
  <p>By AMP Tutorials</p>
</amp-story-grid-layer>
```
​
Refresh your browser and review your work.  Our cover page is complete.
​
{{ image('/static/img/docs/tutorials/amp_story/pg0_cover.png', 720, 1280, align='center third', alt='Completed cover page' ) }}
