<section>

If you want more, read this:

<div class="hl-block left-align">

## P0022: Proxy Iterators for the Ranges Extensions

<br />

### - Eric Niebler, 2015

</div>

</section>
<section>

<div class="hl-block left-align">

## **proxy reference**

_noun_

> A type which acts like a reference for another type, even when it’s an r-value.

</div>

</section>
<section>

<div class="hl-block left-align">

## **proxy iterator**

_noun_

> An iterator whose reference type is a proxy reference.

</div>

</section>
<section>

<div class="hl-block left-align">

### Proxy iterator examples:

<code class="cpp" data-noescape data-trim>std::vector&lt;<span class="hljs-keyword">bool</span>&gt;::iterator</code>:
<!-- .element: class="fragment" data-fragment-index="1" -->

- Dereference returns r-value of type <code class="cpp" data-noescape data-trim>vector&lt;<span class="hljs-keyword">bool</span>&gt;::reference</code>.

<!-- .element: class="fragment" data-fragment-index="1" -->

- `reference` type provides read and write operations to bits.

<!-- .element: class="fragment" data-fragment-index="1" -->

`all_pairs_view::inner_iterator`:
<!-- .element: class="fragment" data-fragment-index="2" -->

- Dereference returns an r-value of type `std::pair<T&, T&>`.

<!-- .element: class="fragment" data-fragment-index="2" -->

- `pair` provides read and write operations to underlying values.

<!-- .element: class="fragment" data-fragment-index="2" -->

</div>

</section>
<section>

<div class="hl-block pretty-big-text">
"Are proxy iterators even allowed?"
</div>

<div class="fragment" data-fragment-index="1">
    <h2 class="correction" style="top: 200px; left: 550px; line-height: 0;">Yes!</h2>
</div>
<div class="fragment" data-fragment-index="2">
    <p class="correction handwriting"
       style="top: 180px; left: 740px; text-align: left; line-height: 0;">(with C++20)</p>
</div>

</section>
<section>

### Pre-C++20

```c++ [|5]
template <class I>
concept __LegacyForwardIterator = // then called a “named requirement”
	__LegacyInputIterator<I> && 
	std::constructible_from<I> &&
	std::is_lvalue_reference_v<std::iter_reference_t<I>> &&
	std::same_as<
		std::remove_cvref_t<std::iter_reference_t<I>>,
		typename std::indirectly_readable_traits<I>::value_type> &&
	requires (I i) {
		{  i++ } -> std::convertible_to<const I&>;
		{ *i++ } -> std::same_as<std::iter_reference_t<I>>;
};
```

</section>
<section>

### C++20

```c++ []
template<class I>
concept forward_iterator =
	std::input_iterator<I> &&
	std::derived_from<std::iterator_traits<I>, std::forward_iterator_tag> &&
	std::incrementable<I> &&
	std::sentinel_for<I, I>;
```

</section>
<section>

<div class="hl-block left-align">

### What this means:

Before C++20, everything was confusing:
<!-- .element: class="fragment" data-fragment-index="1" -->

- <code class="cpp">vector&lt;<span class="hljs-keyword">bool</span>&gt;</code> uses a proxy type for its “reference”.

<!-- .element: class="fragment" data-fragment-index="1" -->

- `ForwardIterator::reference` must be an l-value reference.

<!-- .element: class="fragment" data-fragment-index="1" -->

- <code class="cpp">vector&lt;<span class="hljs-keyword">bool</span>&gt;::iterator</code> wasn't even a forward iterator. 🤦‍♂️

<!-- .element: class="fragment" data-fragment-index="1" -->


C++20 makes iterators easier:
<!-- .element: class="fragment" data-fragment-index="2" -->

- Non-l-value proxy reference types are actually supported.

<!-- .element: class="fragment" data-fragment-index="2" -->

- <code class="cpp">vector&lt;<span class="hljs-keyword">bool</span>&gt;</code> is “okay” now!

<!-- .element: class="fragment" data-fragment-index="2" -->

</div>

</section>
<section>

<div class="hl-block pretty-big-text">
What else is required for proxy iterators to work?
</div>

</section>
