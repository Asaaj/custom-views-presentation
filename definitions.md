<section>

<div class="hl-block pretty-big-text">
What is a <code>view</code>?
</div>

</section>
<section>

#### Easy to access:

```c++ []
namespace std {
	namespace views = ranges::views;
}
```

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
                <li>Can be used in range adaptor pipelines.</li>
                <li>O(1) move (and maybe copy) construction.</li>
                <li><code>ranges::enable_view&lt;T></code> is true.</li>
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
               movable&lt;T&gt; &&
               ranges::enable_view&lt;T&gt;;

</code>
            </pre>
        </td>
    </tr>
</table>

<aside class="notes">

Note that the requirements for views changed in May, in P2325, and again in October, in P2415.

</aside>

</section>
<section>
<div class="hl-block left-align">

## **range adaptor pipeline**

_noun_

> Composed range transformations that evaluate lazily as the resulting view is iterated.
> 
> Pipelines are cheap to create; "real" work is done during iteration.

</div>

</section>
<section>

```c++ [2]
using namespace std::views;
for (int i : iota(2) | filter(even) | transform(square))
	std::cout << i << ' ';
```

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
                <li>A view that takes a range as input.</li>
                <li>Represents the range in a different way.</li>
                <li>Most views are adaptors.</li>
            </ul>
        </td>
        <td>
            <ul>
                <li>A view that generates elements on demand.</li>
                <li>Does not have any "backing" range.</li>
                <li>Only 4 in the standard.</li>
            </ul>
        </td>
    </tr>
    <tr>
        <td>
            <pre>
                <code class="cpp">using namespace std::ranges;
auto a = ref_view{ some_vect };
auto b = filter_view{ a, even };
</code>
            </pre>
        </td>
        <td>
            <pre>
                <code class="cpp">using namespace std::ranges;
auto a = iota_view{ 1, 10 };
auto b = istream_view&lt;std::string&gt;{ words };
</code>
            </pre>
        </td>
    </tr>
</table>

</section>
