<section>

<div class="hl-block pretty-big-text">What about the piping operator?</div>

<br />

```c++ [2]
using namespace std::views;
for (int i : iota(2) | filter(even) | transform(square))
	std::cout << i << ' ';
```

</section>
<section>

<div class="hl-block">

## The piping operator isn't done.

<br/>

### P2387 proposes a fix for C++23.

</div>

</section>
<section>

<pre><code class="cpp" data-noescape data-trim data-line-numbers="|1|2|1">
auto const reversed_evens = std::views::reverse | std::views::filter(even);
auto const x = <i>some_range</i> | reversed_evens;
</code></pre>

</section>
<section>

<div class="hl-block left-align">

### Creating Views

`std::views::reverse` is typically used to create a `reverse_view`.

`std::views::filter` is typically used to create a `filter_view`.

</div>

</section>
<section>

<div class="hl-block left-align">

### View Parameters

`reverse_view` takes a range and represents it differently (adapts it).

`filter_view` takes a range _and a function_ and adapts the range.

</div>

</section>
<section>

<div class="hl-block left-align">

### Piping Views

`std::views::reverse` can be used in a pipeline.

`std::views::filter` needs a function to be used in a pipeline.

</div>

</section>
<section>

<pre><code class="cpp" data-noescape data-trim data-line-numbers="1">
auto const reversed_evens = std::views::reverse | std::views::filter(even);
auto const x = <i>some_range</i> | reversed_evens;
</code></pre>

</section>
<section>

<div class="hl-block left-align">

### Range Adaptor Closure Objects [24.7.1.1]

A _range adaptor closure object_ is a <span class="fragment hl-text" data-fragment-index="1">unary function object that accepts a
`viewable_range` argument and returns a `view`</span>. For a range adaptor closure object `C` and an expression `R` such
that `decltype((R))` models `viewable_range`, the following expressions are equivalent and yield a `view`:

- <span class="fragment hl-text" data-fragment-index="2">`C(R)`</span>
- <span class="fragment hl-text" data-fragment-index="2">`R | C`</span>

</div>

</section>
<section>
<div class="hl-block left-align">

### Range Adaptor Closure Objects [24.7.1.1]

Given an additional range adaptor closure object `D`, the expression <span class="fragment hl-text" data-fragment-index="1">
<code>C&nbsp;|&nbsp;D</code></span> is well-formed and
<span class="fragment hl-text" data-fragment-index="1">produces another range adaptor closure object</span> such that
the following two expressions are equivalent:

- <span class="fragment hl-text" data-fragment-index="2">`R | C | D`</span>
- <span class="fragment hl-text" data-fragment-index="2">`R | (C | D)`</span>

</div>

</section>
<section>

<div class="hl-block left-align">

### Range Adaptor Objects [24.7.1.2-4]

A _range adaptor object_ is a customization point object that <span class="fragment hl-text">accepts a `viewable_range`
as its first argument and returns a `view`.</span>

</div>

</section>
<section>

<div class="hl-block left-align">

### Range Adaptor Objects [24.7.1.2-4]

<span class="fragment hl-text" data-fragment-index="1">If a range adaptor object accepts only one argument, then it is a
range adaptor closure object.</span>

If a range adaptor object accepts <span class="fragment hl-text" data-fragment-index="2">more than one argument</span>, 
then the following expressions are equivalent:

- `adaptor(range, args...)`
- `adaptor(args...)(range)`
- `range | adaptor(args...)`

<span class="fragment hl-text" data-fragment-index="3">In this case, `adaptor(args...)` is a range adaptor closure object.</span>

</div>

</section>
<section>

<pre><code class="cpp" data-noescape data-trim data-line-numbers="1|2">
auto const reversed_evens = std::views::reverse | std::views::filter(even);
auto const x = <i>some_range</i> | reversed_evens;
</code></pre>

</section>
