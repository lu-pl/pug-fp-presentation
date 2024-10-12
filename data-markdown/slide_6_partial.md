<span style="color: #ffd343;">functools.partial</span>

<div class="fragment">
<span style="font-size: 38px;"><span style="white-space: nowrap">Partial Application</span>

<span style="font-size: 28px;"><span style="white-space: nowrap">Partial application is a functional technique for deriving a function of arity n - m <br/>from a function of arity n by supplying m arguments.</span>
</div>

+++

<span style="color: #ffd343;">functools.partial</span>

<span style="font-size: 28px;"><span style="white-space: nowrap">Basic examples</span>


<pre>
<code class="python" data-line-numbers>def exp(n, p):
    return n**p

square = functools.partial(exp, p=2)
cube = functools.partial(exp, p=3)
</code></pre>

<pre class="fragment">
<code class="python" data-line-numbers>open_for_winners: Annotated[
	Callable, 
	"Windows users like that."
] = functools.partial(
    open, encoding="utf-8"
)
</code></pre>

+++

<span style="color: #ffd343;">functools.partial</span>

<span style="font-size: 28px;"><span style="white-space: nowrap">Basic implemenation</span>

<pre>
<code class="python" data-line-numbers>class simple_partial[T]:
    def __init__(self, f: Callable[..., T], *args, **kwargs) -> None:
        self.f = f
        self.args = args
        self.kwargs = kwargs

    def __call__(self, *args, **kwargs) -> T:
        return self.f(*(*self.args, *args), **(self.kwargs | kwargs))


partially = simple_partial(lambda x, y, z=3: (x, y, z), 1)
partially(2)
</code></pre>

+++

<span style="color: #ffd343;">functools.partial</span>

<span style="font-size: 38px;"><span style="white-space: nowrap">Currying</span>

<span class="fragment" style="font-size: 28px;"><span style="white-space: nowrap">Currying is a functional technique for deriving n functions of arity 1 <br/>from a function of arity n.</span>


+++

<span style="color: #ffd343;">functools.partial</span>

<span style="font-size: 38px;"><span style="white-space: nowrap">Currying</span>

<pre class="fragment">
<code class="python" data-line-numbers>def f(x, y, z):
    return x, y, z

curried_1 = toolz.curry(f)(1)(2)(3)    # (1, 2, 3)
curried_2 = toolz.curry(f)(1)(2, 3)    # (1, 2, 3)
curried_3 = toolz.curry(f)(1, 2, 3)    # (1, 2, 3)
curried_4 = toolz.curry(f, 1, 2, 3)()  # (1, 2, 3)
</code></pre>

<pre class="fragment">
<code class="python" data-line-numbers>partial_curried_1 = partial(partial(partial(partial(f), 1), 2), 3)()
partial_curried_2 = partial(partial(partial(f), 1), 2, 3)()
partial_curried_3 = partial(partial(f), 1, 2, 3)()
partial_curried_4 = partial(f, 1, 2, 3)()
</code></pre>

+++

<span style="color: #ffd343;">functools.partial</span>

<span style="font-size: 38px;"><span style="white-space: nowrap">Curry Implementation</span>

<pre class="fragment">
<code class="python" data-line-numbers>def simple_curry(f, *args, **kwargs):
    def _wrapper(*more_args, **more_kwargs):
        if not more_args and not more_kwargs:
            return f(*args, **kwargs)
        return simple_curry(
			f, 
			*(*args, *more_args), 
			**(kwargs | more_kwargs)
		)
    return _wrapper
</code></pre>

+++

<span style="color: #ffd343;">functools.partial</span>

<span style="font-size: 38px;"><span style="white-space: nowrap">Idea: Currying Pydantic models</span>

<pre>
<code class="python" data-line-numbers>@CurryModel
class MyModel(BaseModel):
    x: str
    y: int
    z: tuple[str, int]

model_instance = MyModel(x="1")(y=2)(z=("3", 4))
</code></pre>
