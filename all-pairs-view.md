<section>

```c++ [|8]
template <std::ranges::view TView> requires std::ranges::forward_range<TView>
class all_pairs_view :
	public std::ranges::view_interface<all_pairs_view<TView>> // CRTP!
{
private:
	TView _vw;
public:
	class iterator { /* ... */ };
 
	all_pairs_view() = default;
	constexpr all_pairs_view(TView vw);
 
	[[nodiscard]] constexpr iterator begin() const;
	[[nodiscard]] constexpr iterator end() const;
};
```

</section>
<section>

<div class="hl-block pretty-big-text">

A <code>view</code> is often just a supplier<br />of a very fancy iterator.

</div>

</section>
<section>

```c++ [|7]
template <std::ranges::view TView> requires std::ranges::forward_range<TView>
class all_pairs_view :
	public std::ranges::view_interface<all_pairs_view<TView>>
{
private:
	TView _vw;
	class inner_iterator { /* ... */ };
	class inner_sentinel { /* ... */ };
	class inner_view { /* ... */ };
 
	class outer_iterator { /* ... */ };
 
public:
	using iterator = outer_iterator;
 
	all_pairs_view() = default;
	constexpr all_pairs_view(TView vw);
 
	[[nodiscard]] constexpr iterator begin() const;
	[[nodiscard]] constexpr iterator end() const;
};
```

</section>