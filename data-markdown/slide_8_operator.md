<div style="text-align: left; color: #3776ab">
operator
</div>

<span style="font-size: 28px; color: #ffd343;"><em>"The operator module exports a set of efficient functions </br>corresponding to the intrinsic operators of Python."</em></span>

+++

<span style="color: #ffd343;">operator</span>

<span style="font-size: 28px">That is, operator provides functional handles for operations <br/>typically expressed as syntactic constructs (infix operators, dot access).</span>
<span style="font-size: 24px"><br/><em>"functional de-sugaring"</em></span>

<pre class="fragment"><code>1 + 2</code></pre>
<pre class="fragment"><code>int(1).__add__(2)</code></pre>
<pre class="fragment"><code>operator.add(1, 2)</code></pre>

<div class="fragment">
<pre><code class="python">reduce(lambda x, y: x + y, [1, 2, 3])</code></pre>
</div>

<pre class="fragment python"><code>reduce(operator.add, [1, 2, 3])</code></pre>

+++

<span style="color: #ffd343;">operator</span>

<span style="font-size: 28px">`Sequence.__getitem__` / `Mapping.__getitem__`</span>


<pre>
<code class="python" data-line-numbers>Point = namedtuple("Point", ["x", "y"])

p1 = Point(1, 2)
p2 = Point(2, 2)
p3 = Point(3, 2)

def horizontal(*points: Point) -> bool:
    y_values = map(operator.itemgetter(1), points)
    return len(set(y_values)) == 1

horizontal(p1, p2, p3)  # True
</code>
</pre>

+++

<span style="color: #ffd343;">operator</span>

<span style="font-size: 28px">`object.__getattribute__`</span>

<pre>
<code class="python" data-line-numbers>def max_axis(axis: PyLiteral["x", "y"], *points: Point) -> Point:
    *_, max_point = sorted(points, key=operator.attrgetter(axis))
    return max_point

max_axis("x", p1, p2, p3)  # Point(x=3, y=2)
</code>
</pre>
