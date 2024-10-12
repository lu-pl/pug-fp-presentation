<span style="color: #ffd343;">functools.singledispatch</span>

<div class="fragment">
<span style="font-size: 38px;"><span style="white-space: nowrap">Dynamic Dispatch</span>

<span style="font-size: 28px">Dynamic dispatch denotes a runtime mechanism <br/>for selecting an applicable implementation of a polymorphic function.</span>

+++

<span style="color: #ffd343;">functools.singledispatch</span>

<span style="font-size: 28px">Python has single dispatch/ad hoc polymorphism in the form of methods</span>

<pre>
<code>map(lambda x: x.f(), [Foo(), Bar()])  # "foo", "bar"
</code>
</pre>

+++

<span style="color: #ffd343;">functools.singledispatch</span>

<pre class="fragment">
<code data-line-numbers>class Foo: pass
class Bar: pass

@functools.singledispatch
def f(self):
    raise NotImplementedError

@f.register
def _(self: Foo):
    return "foo"

@f.register
def _(self: Bar):
    return "bar"

map(lambda x: f(x), [Foo(), Bar()])  # "foo", "bar"
</code>
</pre>

+++

<span style="color: #ffd343;">functools.singledispatch</span>

<span style="font-size: 38px;"><span style="white-space: nowrap">Use cases</span>

<ul style="font-size: 30px">
	<li class="fragment">Functional type switch</li>
	<li class="fragment">Functional Adapter pattern</li>
	<li class="fragment">Overloading of external code</li>
</ul>

+++

<span style="color: #ffd343;">functools.singledispatch</span>

<span style="font-size: 38px;"><span style="white-space: nowrap">Type switch</span>
<pre>
<code data-line-numbers>def f(obj):
    match obj:
        case Foo():
            return "foo"
        case Bar():
            return "bar"
        case _:
            raise NotImplementedError

map(lambda x: f(x), [Foo(), Bar()])  # ["foo", "bar"]
</code>
</pre>

+++

<span style="color: #ffd343;">functools.singledispatch</span>

<pre>
<code data-line-numbers>@functools.singledispatch
def get_message(obj) -> Never:
    raise NotImplementedError

@get_message.register(Foo)
def _(obj) -> str:
    return obj.msg

@get_message.register(Bar)
def _(obj) -> str:
    return obj.message

@get_message.register(Baz)
def _(obj) -> str:
    return obj.text

foo = Foo(msg="foo")
bar = Bar(message="bar")
baz = Baz(text="baz")

map(lambda x: get_message(x), (foo, bar, baz))  # 'foo', 'bar', 'baz'
</code>
</pre>

+++

<span style="color: #ffd343;">functools.singledispatch</span>

<span style="font-size: 38px;"><span style="white-space: nowrap">External overloading</span>

<pre>
<code># external.py
def f[T1, T2](x: T1, y: T2) -> tuple[T1, T2]:
    return x, y
</code>
</pre>

<pre>
<code>from external import f

f = functools.singledispatch(f)

@f.register
def _(x: str, y) -> str:
    return f"{x}{y}"

f(1, 2)    # (1, 2)
f("1", 2)  # "12"
</code>
</pre>
