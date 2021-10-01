<section data-background-image="images/solar-system.png" data-background-size="contain">
</section>
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

[Insert graphic]

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

### Piping Operator Shortcomings (P2011):

- Not standardized; implementations incompatible.
- “Incomplete” adaptors like this are pretty tough to implement:
    ```auto a = std::views::transform(even);```
- Lots of boilerplate and upkeep for implementers.

</div>

</section>
<section>

<div class="hl-block left-align">

### Piping Operator Solutions (P2011)

- Standardize the adaptor/pipeline types.<aside class="subtle">*</aside>
- Language support for a new operator (e.g. P2011):
    ```x |> f(y); // exactly equivalent to f(x, y)```
- Others?

<div style="height: 70px;"></div>

<aside class="subtle">* If possible; I haven't tried it.</aside>

</div>

</section>
