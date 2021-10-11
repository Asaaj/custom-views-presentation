<section data-background-image="images/solar-system.png" data-background-size="contain">
</section>
<section>

```c++ [11|17-21]
template <std::ranges::view TBase> requires std::ranges::forward_range<TBase>
class all_pairs_view :
	public std::ranges::view_interface<all_pairs_view<TBase>>
{
private:
	TBase _vw;
	class inner_iterator { /* ... */ };
	class inner_sentinel { /* ... */ };
	class inner_view { /* ... */ };
 
	class outer_iterator { /* ... */ };
 
public:
	using iterator = outer_iterator;
 
	all_pairs_view() = default;
	constexpr all_pairs_view(TBase vw);
 
	[[nodiscard]] constexpr iterator begin() const;
	[[nodiscard]] constexpr iterator end() const;
};
```

</section>
<section>

```c++ [|19-20]
template <std::ranges::view TBase> requires std::ranges::forward_range<TBase>
class all_pairs_view :
	public std::ranges::view_interface<all_pairs_view<TBase>>
{
    /* ... */
	constexpr all_pairs_view(TBase vw) : _vw{ std::move(vw) } { }

	[[nodiscard]] constexpr iterator begin() const
	{
		return { *this, std::ranges::begin(_vw) };
	}

	[[nodiscard]] constexpr iterator end() const
	{
		return { *this, std::ranges::end(_vw) };
	}
};

template <class Rng>
all_pairs_view(Rng&&) -> all_pairs_view<std::views::all_t<Rng>>;
```

</section>
