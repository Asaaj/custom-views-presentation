<section data-background-image="images/planet-transformation-bracket.png" data-background-size="contain">
</section>
<section>

```c++ [|1-3||8]
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

<aside class="notes">
So here is the view, or at least a high-level description. I would like to initially say that this is not a production-
ready view; it's only slide-ready. There are details and use cases that are not included in this slide version, but I 
hope this gets enough across to show there's no magic.

**CLICK** \[Describe inputs and template types\]

</aside>

</section>
<section>

<div class="hl-block pretty-big-text">
A view is usually just a supplier<br/>of a fancy iterator.
</div>

</section>
<section>

```c++ [|6,17]
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

```c++ [|12-13]
template <std::ranges::view TBase> requires std::ranges::forward_range<TBase>
class all_pairs_view :
	public std::ranges::view_interface<all_pairs_view<TBase>>
{
private:
	TBase _vw;
	
public:
	constexpr all_pairs_view(TBase vw) : _vw{ std::move(vw) } { }
};

template <class Rng>
all_pairs_view(Rng&&) -> all_pairs_view<std::views::all_t<Rng>>;
```

</section>
<section>

```c++ [17|7]
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

```c++ [|8,13|9,14|16|]
class inner_iterator
{
private:
	friend class inner_sentinel;
 
	using base_iterator = std::ranges::iterator_t<TBase>;
	using base_sentinel = std::ranges::sentinel_t<TBase>;
	using base_value_type = std::ranges::range_value_t<TBase>;
	using base_reference = std::ranges::range_reference_t<TBase>;
    /* ... */

public:
	using value_type = std::pair<base_value_type, base_value_type>;
	using reference = std::pair<base_reference, base_reference>;
	using difference_type = std::ptrdiff_t;
	using iterator_category = std::forward_iterator_tag;
	/* ... */
};
```

<aside class="notes">

You may notice I don't define "pointer" here. Before C++20, we had to define 5 types, these 4 plus pointer, in order to
be considered an iterator. Now, if pointer doesn't make sense for your iterator type, you don't actually have to define
it.

</aside>

</section>
<section>

```c++ [|9]
class inner_iterator
{
private:
	/* ... */
	base_iterator _current_outer{};
	base_iterator _current_inner{};
	TBase const* _base{};

	void correct_inner_if_needed();

public:
	/* ... */
	inner_iterator() = default;
	inner_iterator(TBase const& base, base_iterator current_outer);

	[[nodiscard]] reference operator*() const;
	inner_iterator& operator++();
	[[nodiscard]] inner_iterator operator++(int)
	[[nodiscard]] bool operator==(inner_iterator const& rhs) const;
};
```

</section>
<section>

```c++ [15-21|29-42|44-56|51|44-56|58-64]
class inner_iterator
{
private:
	friend class inner_sentinel;

	using base_iterator = std::ranges::iterator_t<TBase>;
	using base_sentinel = std::ranges::sentinel_t<TBase>;
	using base_value_type = std::ranges::range_value_t<TBase>;
	using base_reference = std::ranges::range_reference_t<TBase>;

	base_iterator _current_outer{};
	base_iterator _current_inner{};
	TBase const* _base{};

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
	using difference_type = std::ptrdiff_t;
	using iterator_category = std::forward_iterator_tag;

	inner_iterator() = default;

	inner_iterator(TBase const& base, base_iterator current_outer)
		: _current_outer{ std::move(current_outer) }
		, _current_inner{ std::ranges::begin(base) }
		, _base{ std::addressof(base) }
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
