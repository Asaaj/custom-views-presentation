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

```c++ [1-20|22-37]
class outer_iterator
{
private:
	using base_iterator = std::ranges::iterator_t<TBase>;

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
```

</section>
<section>

```c++ [|7]
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

### Pre-C++20

```c++ [|5,10]
template <class I>
struct iterator_traits<I, void_t<typename I::iterator_category, 
                                 typename I::difference_type,
                                 typename I::value_type, 
                                 typename I::pointer, 
                                 typename I::reference>> {
    using iterator_category = typename I::iterator_category;
    using difference_type   = typename I::difference_type;
    using value_type        = typename I::value_type;
    using pointer           = typename I::pointer;
    using reference         = typename I::reference;
};
```

</section>
<section>

### C++20

```c++ [|1-3|10]
template <class I>
concept has_iter_types = has_member_difference_type<I> && has_member_value_type<I> && 
                         has_member_reference<I> && has_member_iterator_category<I>;
 
template <has_iter_types I>
struct iterator_traits<I> {
    using iterator_category = typename I::iterator_category;
    using difference_type   = typename I::difference_type;
    using value_type        = typename I::value_type;
    using pointer           = /* I::pointer if defined, void otherwise */
    using reference         = typename I::reference;
};
```

</section>
<section>

```c++ []
class outer_iterator
{
	/* ... */
public:
	using value_type = inner_view;
	using reference = value_type;
	using difference_type = std::ptrdiff_t;
	using iterator_category = std::forward_iterator_tag;
	/* ... */
};
```

</section><section>

```c++ [|6]
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

```c++ []
class outer_iterator
{
    using reference = value_type;
    
	[[nodiscard]] value_type operator*() const
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
