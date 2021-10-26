<section>

<div class="hl-block pretty-big-text">
    <code class="cpp">std::ranges::iter_swap</code>
    <code class="cpp">std::ranges::iter_move</code>
</div>

</section>
<section>

```c++ []
template <class I>
void SwapIters(I lhs, I rhs)
{
	using std::swap;
	swap(*lhs, *rhs);
}
```

<div class="hl-block code-note fragment" style="top: -200px;">

Proxy reference type could be `pair<T&,T&>`, so this just swapped two rvalues.

</div>

<aside class="notes">
I could use std::iter_swap for this, but it actually does the same thing. It can be used to avoid this pattern, but it
has the same problem with proxy iterators.
</aside>

</section>
<section>

<div class="hl-block left-align">

### `std::ranges::iter_swap`

- Improvement upon `std::iter_swap` from C++98.

<!-- .element: class="fragment" data-fragment-index="1" -->

- Intentionally designed _customization point_ for proxy iterators.

<!-- .element: class="fragment" data-fragment-index="1" -->

- Used by all `std::ranges` algorithms that swap stuff.

<!-- .element: class="fragment" data-fragment-index="1" -->

<div style="height: 70px;"></div>

###### If your generic code needs to swap elements in a container, use this.

<!-- .element: class="fragment" data-fragment-index="2" -->

###### You usually won’t have to customize this; calls `iter_move` by default.

<!-- .element: class="fragment" data-fragment-index="2" -->

</div>

</section>
<section>

```c++ []
template <class I>
std::iter_value_t<I> Extract(I iter)
{
	return std::move(*iter);
}
```

<div class="hl-block code-note fragment" style="top: -200px;">

Proxy reference type could be `pair<T&,T&>`, so this moved an rvalue.

</div>

</section>
<section>

<div class="hl-block left-align">

### `std::ranges::iter_move`

- Didn’t exist before, even after `std::move` came along.

<!-- .element: class="fragment" data-fragment-index="1" -->

- Another customization point for proxy iterators.

<!-- .element: class="fragment" data-fragment-index="1" -->

- Used by all `std::ranges` algorithms that move values.

<!-- .element: class="fragment" data-fragment-index="1" -->

<div style="height: 70px;"></div>

###### If `std::move` won’t work for your iterators, you have to customize this.

<!-- .element: class="fragment" data-fragment-index="2" -->

###### Customization gives you semantically correct `iter_swap` for free.

<!-- .element: class="fragment" data-fragment-index="2" -->

</div>

</section>

<section>

<div class="hl-block pretty-big-text">

Another benefit of sentinels:<br/>Won't work with the old algorithms.*

</div>

</section>
<section>

<div class="hl-block pretty-big-text">

But how does one "customize" `std::ranges::iter_move`?

</div>

</section>
<section>

<div class="hl-block left-align">

### cppreference: `iter_move`

<div style="font-size: 25px;">

Obtains an rvalue reference or a prvalue temporary from a given iterator.

A call to `ranges::iter_move` is expression-equivalent to:
- `iter_move(std::forward<T>(t))`, if `std::remove_cvref_t<T>` is a class or enumeration type and the expression is well-formed in unevaluated context, where the overload resolution is performed with the following candidates:
	- `void iter_move();`
	- any declarations of `iter_move` found by argument-dependent lookup.
- otherwise, `std::move(*std::forward<T>(t))` if `*std::forward<T>(t)` is well-formed and is an lvalue,
- otherwise, `*std::forward<T>(t)` if `*std::forward<T>(t)` is well-formed and is an rvalue.

In all other cases, a call to `ranges::iter_move` is ill-formed, which can result in substitution failure when `ranges::iter_move(e)` appears in the immediate context of a template instantiation.

If `ranges::iter_move(e)` is not equal to `*e`, the program is ill-formed, no diagnostic required.

</div>

</div>

<aside class="notes">
You can look up the overload resolution rules for iter_move...
</aside>

</section>
<section>

<div class="hl-block left-align">

### Simplified: `iter_move`

- Define a function `your_iter_namespace::iter_move` that accepts your iterator.
- If you don’t, it'll just use `std::move(*i)`.

</div>

</section>
<section>

```c++ []
class inner_iterator
{
private:
	using base_value_type = std::ranges::range_value_t<TBase>;
	using base_reference = std::ranges::range_reference_t<TBase>;
 
public:
	using value_type = std::pair<base_value_type, base_value_type>;
	using reference = std::pair<base_reference, base_reference>;
 
	friend constexpr auto iter_move(inner_iterator i)
    {
		using base_rref = std::ranges::range_rvalue_reference_t<TBase>;
		return std::pair<base_rref, base_rref> {
			std::ranges::iter_move(i._current_outer),
			std::ranges::iter_move(i._current_inner)
		};
    }
};
```

</section>
<section>

```c++ []
class inner_iterator
{
private:
	using base_value_type = std::ranges::range_value_t<TBase>;
	using base_reference = std::ranges::range_reference_t<TBase>;
 
public:
	using value_type = std::pair<base_value_type, base_value_type>;
	using reference = std::pair<base_reference, base_reference>;
 
	friend constexpr auto iter_move(inner_iterator i)
    {
		return std::pair {
			std::ranges::iter_move(i._current_outer),
			std::ranges::iter_move(i._current_inner)
		};
    }
};
```

</section>
<section data-background-image="images/problem-with-iter-move-1.png" data-background-size="contain">
</section>
<section data-background-image="images/problem-with-iter-move-2.png" data-background-size="contain">
</section>
<section data-background-image="images/problem-with-iter-move-3.png" data-background-size="contain">
</section>
<section>

<div class="hl-block pretty-big-text">

Views that expose elements multiple times can't use `iter_move`.

</div>

</section>
