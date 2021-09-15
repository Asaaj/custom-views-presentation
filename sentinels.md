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
                <li>“New” to the C++20 STL.</li>
                <li>Tiny class returned by <code>end()</code> calls.</li>
                <li>Checks <code>==</code> with <code>iterator</code> type.</li>
            </ul>
        </td>
    </tr>
</table>

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

<div class="hl-block left-align" style="font-size: 35px;">

### Sentinel semantic requirements:

Let `s` and `i` be values of type `S` and `I`, respectively, such that `[i, s)` denotes a range.
`sentinel_for<S, I>` is modeled only if:

- `i == s` is well-defined.
- If `bool(i != s)` then `i` is dereferenceable and `[++i, s)` denotes a range.
- `std::assignable_from<I&, S>` is either modeled or not satisfied.

</div>

</section>
<section>

```c++ [1,4-7]
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
<section>

[Insert graphic]

</section>
<section>

<div class="hl-block">

### Sentinels separate concerns.

#### Any nontrivial "end" logic has its own class.

</div>

</section>
