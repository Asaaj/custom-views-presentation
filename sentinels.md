<section>

<div class="hl-block left-align">

### What is a sentinel?

<table class="compare-columns">
    <colgroup>
        <col style="width: 50%" />
        <col style="width: 50%" />
    </colgroup>
    <tr>
        <th>Value</th>
        <th>Type</th>
    </tr>
    <tr>
        <td>
            <ul>
                <li>Special value, same type.</li>
                <li>Indicates end of a range.</li>
                <li>Most famous: <code class="cpp">‘\0’</code></li>
            </ul>
        </td>
        <td>
            <ul>
                <li>"New" to <code>std::ranges</code>.</li>
                <li>Class returned by <code>end()</code> calls.</li>
                <li>Checks <code>==</code> with <code>iterator</code> type.</li>
            </ul>
        </td>
    </tr>
</table>

</div>

</section>
<section>

<div class="hl-block left-align" style="font-size: 35px;">

### Sentinel semantic requirements:

Let `s` and `i` be values of type `S` and `I`, respectively, such that `[i, s)` denotes a range.
`sentinel_for<S, I>` is modeled only if:

- `i == s` is well-defined.
- <span class="fragment hl-text">If `bool(i != s)` then `i` is dereferenceable and `[++i, s)` denotes a range.</span>
- `std::assignable_from<I&, S>` is either modeled or not satisfied.

</div>

</section>
<section>

<pre><code class="cpp" data-trim data-noescape data-line-numbers="|1-2|1-3|1-4|1-5">
template &lt;class S, class I&gt;
concept sentinel_for =
	std::semiregular&lt;S&gt; &&
	std::input_or_output_iterator&lt;I&gt; &&
	<i>__WeaklyEqualityComparableWith</i>&lt;S, I&gt;;
</code></pre>

</section>
<section>

```c++ []
class inner_sentinel
{
public:
	[[nodiscard]] bool operator==(inner_sentinel const&) const
	{
		return true;
	}
 
	[[nodiscard]] bool operator==(inner_iterator const& rhs) const
	{
		return rhs._current_inner == std::ranges::end(*rhs._base);
	}

    /* As of C++20, the compiler uses this^ to generate these:
     *    sentinel != iterator
     *    iterator == sentinel
     *    iterator != sentinel
     */
};
```

</section>
<section>

<div class="hl-block pretty-big-text">

Why use a sentinel instead<br/>of an `end` iterator?

</div>

</section>
<section>

```c++ [|6,9]
class inner_iterator
{
    /* ... */
    inner_iterator(TBase const& base, base_iterator current_outer)
		: _current_outer{ std::move(current_outer) }
		, _current_inner{ std::ranges::begin(base) }
		, _base{ std::addressof(base) }
	{
		correct_inner_if_needed();
	}
    /* ... */
};
```

</section>
<section>

```c++ [|5,7,10]
class inner_iterator
{
    /* ... */
    inner_iterator(TBase const& base, base_iterator current_outer, 
                   base_iterator current_inner)
		: _current_outer{ std::move(current_outer) }
		, _current_inner{ std::move(current_inner) }
		, _base{ std::addressof(base) }
	{
		correct_inner_if_needed(); // Should I even do this..?
	}
    /* ... */
};
```

</section>
<section>

<div class="hl-block pretty-big-text">

This constructor is only for `end()` to use,<br/>but nothing actually tells us that.

</div>

</section>
<section>

<div class="hl-block pretty-big-text">

Sentinels separate concerns.

The definition of "end" is in its own class.

</div>

</section>
