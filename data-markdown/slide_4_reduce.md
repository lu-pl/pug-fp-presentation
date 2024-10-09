<span style="color: #ffd343;">functools.reduce</span>

<span class="fragment" style="font-size: 28px;"><span style="white-space: nowrap">Reduction <em>reduces</em> the elements of an array into a single result</span> <br/>according to some binary operation.</span>

<pre class="fragment"><code class="python">reduce(add, [1, 2, 3, 4, 5])       # 15</code></pre>
<pre class="fragment"><code class="python">add(add(add(add(1, 2), 3), 4), 5)  # 15</code></pre>

<pre class="fragment">
<code class="python">reduce(lambda x, y: ("add", x, y), [1, 2, 3, 4, 5])</code>
<code class="python"># ('add', ('add', ('add', ('add', 1, 2), 3), 4), 5)</code>
</pre>

+++

<span style="color: #ffd343;">functools.reduce</span>

<div class="fragment">
<span style="font-size: 24px">Naive recursive definition of reduce</span>

<pre >
<code class="python" data-line-numbers>def simple_recursive_reduce[T](
    f: Callable[[T, T], T], iterable: Iterable[T], initial: T | None = None
) -> T:
    first, *rest = iterable

    if rest:
        return f(first, simple_recursive_reduce(f, rest, initial))
    return first if initial is None else f(first, initial)
</code>
</pre>

</div>

+++

<span style="color: #ffd343;">functools.reduce</span>

<span style="color: #3776ab;">compose</span>
<span class="fragment" style="font-size: 28px;"><span style="white-space: nowrap">
Function composition refers to the <em>recursive application of a sequence of functions</em>, <br/>where the output of each applied function becomes the input for the next.
</span>

<div class="fragment">
<pre><code class="python">λ f: λ g: λ x: f(g(x))</code></pre>
<pre><code class="python">(lambda f: lambda g: lambda x: f(g(x)))(lambda x: x == 1)(bool)(1)</code></pre>
<pre><code class="python">(bool(1) == 1)</code></pre>
</div>

+++

<span style="color: #ffd343;">functools.reduce</span>

<span style="color: #3776ab;">compose</span>

<pre >
<code class="python" data-line-numbers>def reductive_compose(*fns):
    fn, *rest_fns = reversed(fns)
    return lambda value: reduce(lambda x, y: y(x), rest_fns, fn(value))
</code>
</pre>

<pre >
<code class="python" data-line-numbers>def recursive_compose(*fns):
    fn, *rest_fns = fns

    if rest_fns:
        return lambda x: fn(recursive_compose(*rest_fns)(x))
    return fn
</code>
</pre>

+++

<span style="color: #ffd343;">functools.reduce</span>

<span style="color: #3776ab;">Applications</span>


<span>
conversion pipelines
</span>
<pre>
<code class="python" data-line-numbers>snake_case_pipeline: Annotated[
    tuple[Callable[[str], str], ...],
    "Functional components for snake case conversion.",
] = (
    partial(re.sub, r"(\s|_)+", "_"),
    partial(re.sub, r"[^a-z0-9\s_]", ""),
    str.lower,
    str.lstrip,
    str.rstrip,
)

to_snake_case: Annotated[Callable[[str], str], "Convert to snake case."] = (
    reductive_compose(*snake_case_pipeline)
)
</code>
</pre>

+++

<span style="color: #ffd343;">functools.reduce</span>

<span style="color: #3776ab;">Applications</span>

<span>
decorator composition
</span>


<pre>
<code class="python">@decor3
@decor2
@decor1
def f():
	pass
</code>
</pre>

<pre>
<code class="python">composed_decorator = reductive_compose(
	decor3, 
	decor2, 
	decor1
)

@composed_decorator
def g():
	pass
</code>
</pre>
