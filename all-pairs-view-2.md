<section>

```c++ [8|9]
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
<section>

```c++ []
class inner_view : public std::ranges::view_interface<inner_view>
{
private:
	using base_iterator = std::ranges::iterator_t<TView>;
 
	all_pairs_view const* _parent{};
	base_iterator _current_outer{};
 
public:
	using iterator = inner_iterator;
	using sentinel = inner_sentinel;
 
	inner_view() = default;
 
	inner_view(all_pairs_view const& parent, base_iterator current)
		: _parent{ std::addressof(parent) }
		, _current_outer{ std::move(current) } { }
 
	[[nodiscard]] iterator begin() const { return { *_parent, _current_outer }; }
	[[nodiscard]] sentinel end() const { return { }; }
};
```

</section>
<section>

```c++ [9|11]
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
<section>

```c++ [1-20|22-39|39]
class outer_iterator
{
private:
	using base_iterator = std::ranges::iterator_t<TView>;

	all_pairs_view const* _parent{};
	base_iterator _current{};

public:
	using value_type = inner_view;
	using reference = value_type; // I’ll come back to this
	using pointer = value_type*;  // And this
	using difference_type = std::ptrdiff_t;
	using iterator_category = std::forward_iterator_tag;

	outer_iterator() = default;

	outer_iterator(all_pairs_view const& parent, base_iterator current)
		: _parent{ std::addressof(parent) }
		, _current{ std::move(current) } { }

	[[nodiscard]] value_type operator*() const
	{
		return inner_view{ *_parent, _current };
	}

	outer_iterator& operator++()  // Postfix necessary but omitted
	{
		++_current;
		return *this;
	}

	[[nodiscard]] bool operator==(outer_iterator const& rhs) const
	{
		return _parent == rhs._parent && _current == rhs._current;
	}
};

static_assert(std::input_iterator<outer_iterator>);
```

</section>
<section>

```c++ [|6|6,7]
class outer_iterator
{
	/* ... */
public:
	using value_type = inner_view;
	using reference = value_type; // Why isn’t this "value_type&"?
	using pointer = value_type*;
	using difference_type = std::ptrdiff_t;
	using iterator_category = std::forward_iterator_tag;
	/* ... */
};
```

</section>
<section>

[Insert graphic]

</section>
<section>

```c++ [|4-5,11]
class outer_iterator
{
	using value_type = inner_view;
	using reference = value_type;
	using pointer = value_type*;
};

class inner_iterator
{
	using value_type = std::pair<base_value_type, base_value_type>;
	using reference = std::pair<base_reference, base_reference>;
};
```

</section>
