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

### Range Adaptor Closure Objects <span style="font-size: 40px;">[24.7.2.1]</span>

A _range adaptor closure object_ is a <span class="fragment hl-text" data-fragment-index="1">unary function object that accepts a
`viewable_range` argument and returns a `view`</span>. For a range adaptor closure object `C` and an expression `R` such
that `decltype((R))` models `viewable_range`, the following expressions are equivalent and yield a `view`:

- <span class="fragment hl-text" data-fragment-index="2">`C(R)`</span>
- <span class="fragment hl-text" data-fragment-index="2">`R | C`</span>

</div>

<aside class="notes">
A viewable_range just defines a range which it is safe to convert to a view. 
</aside>

</section>
<section>
<div class="hl-block left-align">

### Range Adaptor Closure Objects <span style="font-size: 40px;">[24.7.2.1]</span>

Given an additional range adaptor closure object `D`, the expression <span class="fragment hl-text" data-fragment-index="1">
<code>C&nbsp;|&nbsp;D</code> produces another range adaptor closure object `E`</span>.

<span class="fragment hl-text" data-fragment-index="2">**Simplification**</span>: The following expressions are 
equivalent:
- <span class="fragment hl-text" data-fragment-index="2">`E(R)`</span>
- <span class="fragment hl-text" data-fragment-index="2">`D(C(R))`</span>
- <span class="fragment hl-text" data-fragment-index="2">`R | C | D`</span>
- <span class="fragment hl-text" data-fragment-index="2">`R | (C | D)`</span>

</div>

</section>
<section>

<div class="hl-block left-align">

### Range Adaptor Objects <span style="font-size: 40px;">[24.7.2.2-4]</span>

A _range adaptor object_ is a customization point object that <span class="fragment hl-text">accepts a `viewable_range`
as its first argument and returns a `view`.</span>

<span class="fragment hl-text">If a range adaptor object accepts only one argument, then it is a
range adaptor closure object.</span>

</div>

</section>
<section>

<div class="hl-block left-align">

### Range Adaptor Objects <span style="font-size: 40px;">[24.7.2.2-4]</span>

If a range adaptor object `adaptor` accepts <span class="fragment hl-text" data-fragment-index="1">more than one 
argument</span>, then:

<span class="fragment hl-text" data-fragment-index="2">[**Simplification**]</span> let `args...` 
be arguments such that the expression <code>adaptor(R,&nbsp;args...)</code> produces a `view`. In this case, 
<span class="fragment hl-text" data-fragment-index="2">`adaptor(args...)` is a range adaptor closure object.</span>

</div>

</section>
<section>

<pre><code class="cpp" data-noescape data-trim data-line-numbers="1|2">
auto const reversed_evens = std::views::reverse | std::views::filter(even);
auto const x = <i>some_range</i> | reversed_evens;
</code></pre>

</section>
