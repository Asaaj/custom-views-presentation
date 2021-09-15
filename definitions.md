<section>

<div class="hl-block pretty-big-text">
What is a <code>view</code>?
</div>

</section>
<section>

<div class="hl-block medium-text">
https://github.com/ericniebler/range-v3
</div>

</section>
<section>

<table class="hl-block compare-columns">
    <colgroup>
        <col style="width: 50%" />
        <col style="width: 50%" />
    </colgroup>
    <tr>
        <th>Range</th>
        <th>View</th>
    </tr>
    <tr>
        <td>
            <ul>
                <li>Semantically: a collection of "things".</li>
                <li>Has <code>begin</code> and <code>end</code> functions.</li>
            </ul>
            <br /><br />
            <center><b style="font-size: 35px;">That's it.</b></center>
        </td>
        <td>
            <ul>
                <li>A specific type of range.</li>
                <li><b>Constant-time copy/move/assign.</b></li>
                <li>Semiregular <aside class="subtle">(copyable + default-constructible)</aside></li>
                <li><code>ranges::enable_view</code> is true for it.</li>
            </ul>
        </td>
    </tr>
    <tr>
        <td>
            <pre>
                <code class="cpp">template &lt;class T&gt;
concept range = requires(T& t)
{
	ranges::begin(t);
	ranges::end(t);
};</code>
            </pre>
        </td>
        <td>
            <pre>
                <code class="cpp">
template &lt;class T&gt;
concept view = ranges::range&lt;T&gt; &&
               semiregular&lt;T&gt; &&
               ranges::enable_view&lt;T&gt;;

</code>
            </pre>
        </td>
    </tr>
</table>

</section>
<section>

<table class="hl-block compare-columns">
    <colgroup>
        <col style="width: 50%" />
        <col style="width: 50%" />
    </colgroup>
    <tr>
        <th>Range Adaptor</th>
        <th>Range Factory</th>
    </tr>
    <tr>
        <td>
            <ul>
                <li>Takes a range and returns a view.</li>
                <li>Most views are made by adaptors.</li>
                <li>Adaptors can compose to make new adaptors.</li>
            </ul>
        </td>
        <td>
            <ul>
                <li>A view that creates elements from "nothing".</li>
                <li>Usually the source of a pipeline.</li>
                <li>Only 4 in the standard.</li>
            </ul>
        </td>
    </tr>
    <tr>
        <td>
            <pre>
                <code class="cpp">using namespace std::ranges;
auto a = views::reverse;
auto b = views::drop(2) | views::keys |
         views::filter(evens);
</code>
            </pre>
        </td>
        <td>
            <pre>
                <code class="cpp">using namespace std::ranges;
auto a = views::iota(1, 10);
auto b = istream_view&lt;std::string&gt;(words);

</code>
            </pre>
        </td>
    </tr>
</table>

</section>
<section>

<div>In the standard:</div>

```c++
namespace std {
	namespace views = ranges::views;
}
```

</section>
<section>

<div class="hl-block left-align">

### "Pipelines"

- Views and adaptors can be composed together.
- <code>operator|</code> is used for composition.
- A series of things connected with a pipe is often called a "pipeline".
- To be complete, pipelines need source data.

</div>

</section>

<section>

```c++ [2]
using namespace std::views;
for (int i : iota(2) | filter(evens) | transform(square))
	std::cout << i << ' ';
```

</section>
