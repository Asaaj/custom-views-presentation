<section>

```c++ [8|9]
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

```c++ []
class inner_view : public std::ranges::view_interface<inner_view>
{
private:
	using base_iterator = std::ranges::iterator_t<TBase>;
 
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

```c++ [1-19|21-36]
class outer_iterator
{
private:
	using base_iterator = std::ranges::iterator_t<TBase>;

	all_pairs_view const* _parent{};
	base_iterator _current{};

public:
	using value_type = inner_view;
	using reference = value_type; // I’ll come back to this
	using difference_type = std::ptrdiff_t;
	using iterator_category = std::forward_iterator_tag;

	outer_iterator() = default;

	outer_iterator(all_pairs_view const& parent, base_iterator current)
		: _parent{ std::addressof(parent) }
		, _current{ std::move(current) } { }

	[[nodiscard]] reference operator*() const
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
```

</section>
<section>

```c++ [|6]
class outer_iterator
{
	/* ... */
public:
	using value_type = inner_view;
	using reference = value_type; // Why isn’t this "value_type&"?
	using difference_type = std::ptrdiff_t;
	using iterator_category = std::forward_iterator_tag;
	/* ... */
};
```

</section>
<section>

```c++ []
class outer_iterator
{
    using reference = value_type;
    
	[[nodiscard]] reference operator*() const
	{
		return inner_view{ *_parent, _current };
	}
};
```

</section>
<section>

```c++ [|4,10]
class outer_iterator
{
	using value_type = inner_view;
	using reference = value_type;
};

class inner_iterator
{
	using value_type = std::pair<base_value_type, base_value_type>;
	using reference = std::pair<base_reference, base_reference>;
};
```

</section>
