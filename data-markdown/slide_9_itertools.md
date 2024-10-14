<div style="text-align: left; color: #3776ab">
itertools
</div>

<span style="font-size: 28px; color: #ffd343;"><em>"[...] implements a number of iterator building blocks <br/>inspired by constructs from APL, Haskell, and SML. [...]<br/></span>

<span style="color: #3776ab"></span>

<span style="font-size: 28px; color: #ffd343">Together, they form an “<em>iterator algebra</em>” <br/>making it possible to construct specialized tools succinctly and efficiently [...]."</em></span>

+++

<span style="color: #ffd343;">itertools</span>

<span style="font-size: 28px">Higher order functions</span>

<pre class="fragment python">
<code>functools.reduce(operator.add, [1, 2, 3, 4, 5])      # 15
itertools.accumulate([1, 2, 3, 4, 5], operator.add)  # 1, 3, 6, 10, 15
</code>
</pre>

<pre class="fragment python">
<code>itertools.takewhile(bool, [1, 2, 0, 3])  # 1, 2
itertools.dropwhile(bool, [1, 2, 0, 3])  # 0, 3
</code>
</pre>

<pre class="fragment python">
<code>itertools.filterfalse(bool, [1, 2, 0, 3])                   # 0,
filter((lambda x: lambda y: not x(y))(bool), [1, 2, 0, 3])  # 0,
filter(toolz.complement(bool), [1, 2, 0, 3])                # 0,
</code>
</pre>

<pre class="fragment python"><code>itertools.groupby(["a", "b", "def"], len)  # (1, ("a", "b")), (3, ("def",))</code></pre>

<div class="fragment">
<pre class="python"><code>itertools.starmap(operator.pow, [(2, 1), (2, 2), (2, 3)])  # 2, 4, 8</code></pre>

<pre class="python">
<code>def my_starmap(f, iterable):
	for i in iterable:
		yield f(*i)
</code>
</pre>

</div>

+++

<span style="color: #ffd343;">Generator functions</span>

<span style="font-size: 28px">A functional abstraction for iteration</span>

+++

<!-- <span style="color: #ffd343;">Generator functions</span> -->

<span style="font-size: 28px; color: #3776ab">Turtle RDF Constructor</span>


<pre class="python" data-line-numbers>
<code data-line-numbers>class ttl:
    def __init__(
        self,
        uri: _TripleSubject,
        *predicate_object_pairs: tuple[
            URIRef,
            _TripleObject
            | list
            | Iterator
            | Self
            | str
            | tuple[_TripleObject | str, ...],
        ],
        graph: Graph | None = None,
    ) -> None:
        self.uri = uri
        self.predicate_object_pairs = predicate_object_pairs
        self.graph = Graph() if graph is None else deepcopy(graph)
        self._iter = iter(self)
		# ...
</code>
</pre>

+++

<span style="font-size: 28px; color: #3776ab">Turtle RDF Constructor</span>
<pre>
<code data-line-numbers data-ln-start-from="21">    def __iter__(self) -> Iterator[_Triple]:
        """Generate an iterator of tuple-based triple representations."""
        for pred, obj in self.predicate_object_pairs:
            match obj:
                case ttl():
                    yield (self.uri, pred, obj.uri)
                    yield from obj
                case list() | Iterator():
                    _b = BNode()
                    yield (self.uri, pred, _b)
                    yield from ttl(_b, *obj)
                case tuple():
                    _object_list = zip(repeat(pred), obj)
                    yield from ttl(self.uri, *_object_list)
                case obj if isinstance(obj, _TripleObject):
                    yield (self.uri, pred, obj)
                case str():
                    yield (self.uri, pred, Literal(obj))
                case _:
                    raise Exception("This should never happen.")
</code>
</pre>

+++

<span style="font-size: 28px; color: #3776ab">Turtle RDF Constructor</span>

<pre class="python">
<code data-line-numbers="1-22|2|13|3-9|14-21|1-22">triples: Iterator[_Triple] = ttl(
    ex.subject,
    (RDF.type, ex.some_type),
    (RDFS.label, (Literal("label 1"), "label 2")),
    (RDFS.seeAlso, [(RDFS.label, "label 3")]),
    (
        RDFS.isDefinedBy,
        ttl(ex.subject_2, (RDF.type, ex.another_type)),
    ),
)

more_triples: Iterator[_Triple] = ttl(
    ex.some_type,
    (RDF.type, crm.E55_Type),
    (
        crm.P1_is_identified_by,
        [
            (RDF.type, crm.E41_Appellation),
            (crm.P190_has_symbolic_value, "Pretty nifty type"),
        ],
    ),
)
</code>
</pre>

+++

<span style="font-size: 28px; color: #3776ab">Turtle RDF Constructor</span>

<pre class="python">
<code>def to_graph(
	*triples: Iterator[_Triple], 
	graph: Graph | None = None
) -> Graph:
    _graph: Graph = Graph() if graph is None else graph
	
    return functools.reduce(
        Graph.add,
        itertools.chain.from_iterable(triples),
        _graph
    )
	
graph: Graph = to_graph(triples, more_triples, graph=CRMGraph())
</code>
</pre>

+++

<span style="font-size: 28px; color: #3776ab">Turtle RDF Constructor</span>

<pre class="text">
<code data-line-numbers="1-16|5|10|7|16|1-16">
<script type="text/template">@prefix crm: <http://www.cidoc-crm.org/cidoc-crm/> .
@prefix ex: <https://example.test/> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .

ex:subject a ex:some_type ;
	rdfs:label "label 1","label 2" ;
	rdfs:isDefinedBy ex:subject_2 ;
	rdfs:seeAlso [ rdfs:label "label 3" ] .

ex:some_type a crm:E55_Type ;
	crm:P1_is_identified_by [ 
		a crm:E41_Appellation ;
		crm:P190_has_symbolic_value "Pretty nifty type" 
	] .

ex:subject_2 a ex:another_type .
</script>
</code>
</pre>
