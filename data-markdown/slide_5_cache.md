<span style="color: #ffd343;">functools.lru_cache</span>

<span class="fragment" style="font-size: 28px;"><span style="white-space: nowrap">Functional caching denotes a memoization technique <br/>that stores results of pure function calls and returns cached results for the same inputs.
</span>

+++

<span style="color: #ffd343;">functools.lru_cache</span>

<span style="font-size: 28px;"><span style="white-space: nowrap">Basic implementation of a caching decorator</span>

<pre>
<code class="python" data-line-numbers>def simple_cache[**P, T](f: Callable[P, T]) -> Callable[P, T]:
    cache = {}

    @wraps(f)
    def _wrapper(*args: P.args, **kwargs: P.kwargs) -> T:
        _cache_dict: dict = (
			_args_to_kwargs(f, *args) | _get_defaults_kwargs(f) | kwargs
		)
        cache_key: frozendict = frozendict(**_cache_dict)

        try:
            result = cache[cache_key]
        except KeyError:
            result = f(*args, **kwargs)
            cache.update({cache_key: result})

        return result
    return _wrapper
</code></pre>


+++

<span style="color: #ffd343;">functools.lru_cache</span>

<span style="font-size: 28px;"><span style="white-space: nowrap">For how long will this sleep?</span>

<pre><code data-line-numbers>@simple_cache
def f(x, y, z=3):
    print("Sleeping...")
    sleep(1)
    return x, y, z

args_kwargs = [
    ((1, 2), {}),
    ((1, 2, 3), {}),
    ((1, 2), {"z": 3}),
    ((1,), {"z": 3, "y": 2}),
    ((1,), {"y": 2}),
    ((), {"x": 1, "y": 2}),
    ((), {"y": 2, "z": 3, "x": 1}),
]

for args, kwargs in args_kwargs:
    f(*args, **kwargs)
</code></pre>


+++

<span style="color: #ffd343;">functools.lru_cache</span>

<span style="font-size: 28px;white-space: nowrap">
functools.lru_cache provides a functional caching facility with a max_size limit;<br/>
i.e. if the max_size limit is exceeded, the least recently used entry is popped off the cache.
</span>

+++

<span style="color: #ffd343;">functools.lru_cache</span>

<span style="font-size: 24px;text-align: left">What would be the effect of another call to f(1) or f(2)?</span>

<pre>
<code>@functools.lru_cache(maxsize=2)
def f(x):
    sleep(1)
    return x

f(1)
f(2)
f(3)
</code>
</pre>

