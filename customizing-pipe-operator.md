<section>

<div class="hl-block pretty-big-text">
Why can't we customize this?
</div>

</section>
<section>

<div class="hl-block left-align">

### Range Adaptor Object Problems

- Standard doesn't provide a common type for composition.
- Implementations use a common type to compose closure objects.
- You can't use the type because it's unspecified.

</div>

</section>
<section>

<pre><code class="cpp" data-noescape data-trim data-line-numbers="|9">
namespace detail
{
template &lt;class T>
concept can_construct_all_pairs_view = requires(T&& t)
{
	all_pairs_view{ static_cast&lt;T&&>(t) };
};
 
struct all_pairs_fn : public <span class="fragment code-warning" data-fragment-index="1">std::ranges::_Pipe::_Base&lt;all_pairs_fn></span>
{
	template &lt;std::ranges::viewable_range Rng>
	[[nodiscard]] constexpr auto operator()(Rng&& rng) const
		requires can_construct_all_pairs_view&lt;Rng>
	{
		return all_pairs_view{ std::forward&lt;Rng>(rng) };
	}
};
} // namespace detail
 
inline constexpr detail::all_pairs_fn all_pairs;

</code></pre>

</section>
<section>

<div class="hl-block left-align">

### Solution (P2387)

- New type: `std::ranges::range_adaptor_closure<T>`.
- Ensure all closure objects inherit from this.
- Custom closure objects can then use it, too.

</div>

<aside class="notes">

An alternative (or at least broader) solution is proposed in P2011, which suggests a language-level piping operator. It 
would have slightly different syntax, but it would allow piping the first argument of _any_ function rather than passing 
it. 

</aside>

</section>
<section>

```c++ [|1-7|9-18|20]
namespace detail
{
template <class T>
concept can_construct_all_pairs_view = requires(T&& t)
{
	all_pairs_view{ static_cast<T&&>(t) };
};
 
struct all_pairs_fn : public std::ranges::range_adaptor_closure<all_pairs_fn>
{
	template <std::ranges::viewable_range Rng>
	[[nodiscard]] constexpr auto operator()(Rng&& rng) const
		requires can_construct_all_pairs_view<Rng>
	{
		return all_pairs_view{ std::forward<Rng>(rng) };
	}
};
} // namespace detail
 
inline constexpr detail::all_pairs_fn all_pairs;
```

<aside class="notes">
The first thing we see is a concept. I don't know if this pattern has a name, but I like to call it a "deferring 
concept". Essentially this says "as long as T satisfies whatever constraints all_pairs_view uses in its constructor, T 
works in this case too." It lets us avoid repeating concepts, and just define the input requirements in one place.


</aside>

</section>
