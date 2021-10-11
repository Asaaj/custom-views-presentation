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

</section>
<section>

<pre><code class="cpp" data-noescape data-trim data-line-numbers="">
namespace detail
{
template &lt;class T>
concept can_construct_all_pairs_view = requires(T&& t)
{
	all_pairs_view{ static_cast&lt;T&&>(t) };
};
 
struct all_pairs_fn : public std::ranges::range_adaptor_closure&lt;all_pairs_fn>
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
