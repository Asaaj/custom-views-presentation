<section data-background-image="images/planet-transformation.png" data-background-size="contain">
</section>
<section>

```c++ [|8]
template <std::ranges::view TBase> requires std::ranges::forward_range<TBase>
class all_pairs_view :
	public std::ranges::view_interface<all_pairs_view<TBase>> // CRTP!
{
private:
	TBase _vw;
public:
	class iterator { /* ... */ };
 
	all_pairs_view() = default;
	constexpr all_pairs_view(TBase vw);
 
	[[nodiscard]] constexpr iterator begin() const;
	[[nodiscard]] constexpr iterator end() const;
};
```

</section>
<section>

<div class="hl-block pretty-big-text">
A view is often just a supplier<br/>of a very fancy iterator.
</div>

</section>
<section>

```c++ [|7]
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

```c++ [|18]
class inner_iterator
{
private:
	friend class inner_sentinel;
 
	using base_iterator = std::ranges::iterator_t<TBase>;
	using base_sentinel = std::ranges::sentinel_t<TBase>;
	using base_value_type = std::ranges::range_value_t<TBase>;
	using base_reference = std::ranges::range_reference_t<TBase>;
	using base_pointer = std::add_pointer_t<base_value_type>;
    /* ... */

public:
	using value_type = std::pair<base_value_type, base_value_type>;
	using reference = std::pair<base_reference, base_reference>;
	using pointer = std::pair<base_pointer, base_pointer>;
	using difference_type = std::ptrdiff_t;
	using iterator_category = std::forward_iterator_tag;
	/* ... */
};
```

</section>
<section>

```c++ [|9]
class inner_iterator
{
private:
	/* ... */
	base_iterator _current_outer{};
	base_iterator _current_inner{};
	base_sentinel _base_end_cache{};

	void correct_inner_if_needed();

public:
	/* ... */
	inner_iterator() = default;
	inner_iterator(all_pairs_view const& parent, base_iterator const& current_outer);

	[[nodiscard]] reference operator*() const;
	inner_iterator& operator++();
	[[nodiscard]] inner_iterator operator++(int)
	[[nodiscard]] bool operator==(inner_iterator const& rhs) const;
};
```

</section>
<section>

```c++ [16-22|31-44|46-58|53|46-58|60-66]
class inner_iterator
{
private:
	friend class inner_sentinel;

	using base_iterator = std::ranges::iterator_t<TBase>;
	using base_sentinel = std::ranges::sentinel_t<TBase>;
	using base_value_type = std::ranges::range_value_t<TBase>;
	using base_reference = std::ranges::range_reference_t<TBase>;
	using base_pointer = std::add_pointer_t<std::add_const_t<base_value_type>>;

	base_iterator _current_outer{};
	base_iterator _current_inner{};
	base_sentinel _base_end_cache{};

	void correct_inner_if_needed()
	{
		if (_current_inner == _current_outer)
		{
			std::advance(_current_inner, 1);
		}
	}

public:
	using value_type = std::pair<base_value_type, base_value_type>;
	using reference = std::pair<base_reference, base_reference>;
	using pointer = std::pair<base_pointer, base_pointer>;
	using difference_type = std::ptrdiff_t;
	using iterator_category = std::forward_iterator_tag; // TODO

	inner_iterator() = default;

	inner_iterator(all_pairs_view const& parent, base_iterator const& current)
		: _current_outer{ current }
		, _current_inner{ std::ranges::begin(parent.base()) }
		, _base_end_cache{ std::ranges::end(parent.base()) }
	{
		correct_inner_if_needed();
	}

	[[nodiscard]] reference operator*() const
	{
		return { *_current_outer, *_current_inner };
	}

	inner_iterator& operator++()
	{
		++_current_inner;
		correct_inner_if_needed();
		return *this;
	}

	[[nodiscard]] inner_iterator operator++(int)
	{
		auto tmp = *this;
		++(*this);
		return tmp;
	}

	[[nodiscard]] bool operator==(inner_iterator const& rhs) const
	{
		return _current_outer == rhs._current_outer &&
		       _current_inner == rhs._current_inner;
	}
	
	/* As of C++20, operator!= is auto-generated as !(operator==) */
};
```

</section>
<section>

```c++ [7|8]
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

```c++ [|6]
class inner_sentinel
{
public:
	[[nodiscard]] bool operator==(inner_sentinel const&) const
	{
		return true;
	}
 
	[[nodiscard]] bool operator==(inner_iterator const& rhs) const
	{
		return rhs._current_inner == rhs._base_end_cache;
	}

    /* As of C++20, the compiler uses this^ to generate these:
     *    sentinel != iterator
     *    iterator == sentinel
     *    iterator != sentinel
     */
};
```

</section>
