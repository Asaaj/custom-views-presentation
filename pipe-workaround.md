<section>

<div class="hl-block pretty-big-text">

A temporary C++20 workaround

</div>

</section>
<section>

```c++ [|1,4]
class all_pairs_fn : public generic_pipeable<all_pairs_fn>
{
public:
	template <supported_viewable_range Rng>
	[[nodiscard]] constexpr auto operator()(Rng&& rng) const
		requires can_construct_all_pairs_view<Rng>
	{
		return all_pairs_view{ std::forward<Rng>(rng) };
	}
};
```

</section>
<section>

```c++ [|7,9|2,4]
template <class T>
concept view = ranges::range<T>
               && std::movable<T>
               && ranges::enable_view<T>;

template<class T>
concept viewable_range = ranges::range<T>
                         && (ranges::borrowed_range<T> || 
                             ranges::view<std::remove_cvref_t<T>>);
```

</section>
<section>

```c++ [|1-3,5-7]
template <class Rng>
concept supported_enable_view = std::ranges::enable_view<Rng>
                                || ranges::enable_view<Rng>;
 
template <class Rng>
concept supported_enable_borrowed_range = std::ranges::enable_borrowed_range<Rng>
                                          || ranges::enable_borrowed_range<Rng>;
 
template <class Rng>
concept supported_view = std::ranges::range<Rng> && std::movable<Rng>
                         && supported_enable_view<Rng>;
 
template <class Rng>
concept supported_borrowed_range = /* see std::ranges::borrowed_range */
 
template <class Rng>
concept supported_viewable_range = /* see std::ranges::viewable_range */
```

</section>
<section>

```c++ [1]
class all_pairs_fn : public generic_pipeable<all_pairs_fn>
{
public:
	template <supported_viewable_range Rng>
	[[nodiscard]] constexpr auto operator()(Rng&& rng) const
		requires can_construct_all_pairs_view<Rng>
	{
		return all_pairs_view{ std::forward<Rng>(rng) };
	}
};
```

</section>
<section>

```c++ []
std::vector<int> v{ /* ... */ };

auto my_std_view_of_v = v | std::views::all
                          | jtl::views::all_pairs
                          | std::views::all;

auto my_rv3_view_of_v = v | ranges::views::all
                          | jtl::views::all_pairs
                          | ranges::views::all;

auto my_dual_view_of_v = v | std::views::all
                           | jtl::views::all_pairs
                           | ranges::views::all;
```

</section>
<section>

```c++ []
template <class TLeft, class TRight>
concept pipeable_into = requires(TLeft&& l, TRight&& r)
{
	static_cast<TRight&&>(r)(static_cast<TLeft&&>(l));
};
 
// TODO: I only need to show this one if I show the incomplete pipeline hack.
template <class TLeft, class TRight>
concept composable_types = 
    std::constructible_from<std::remove_cvref_t<TLeft>, TLeft> &&
    std::constructible_from<std::remove_cvref_t<TRight>, TRight>;
```

</section>
<section>

```c++ []
template <class TDerived>
struct generic_pipeable
{
    /* Instantiated pipeline: complete view, including input source. */
    template <detail::pipeable_into<TDerived const&> TLeft>
    friend constexpr auto operator|(TLeft&& l, generic_pipeable<TDerived> const& r)
    {
            return static_cast<TDerived const&>(r)(std::forward<TLeft>(l));
    }
    /* ... */
};
```

</section>
<section>

```c++ []
template <class TDerived>
struct generic_pipeable
{
    /* Instantiated pipeline: complete view, including input source. */
    template <detail::pipeable_into<TDerived> TLeft>
    friend constexpr auto operator|(TLeft&& l, generic_pipeable<TDerived>&& r)
    {
            return static_cast<TDerived&&>(r)(std::forward<TLeft>(l));
    }
    /* ... */
};
```

</section>
<section>

```c++ []
auto my_std_adaptor = std::views::all
                    | jtl::views::all_pairs
                    | std::views::all;

auto my_rv3_adaptor = ranges::views::all
                    | jtl::views::all_pairs
                    | ranges::views::all;

```

</section>
