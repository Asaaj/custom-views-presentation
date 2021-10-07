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

Note that the requirements for views just changed in May, in P2325.

</aside>

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
                <li>A view that "points at" a range.</li>
                <li>Requires a range for input.</li>
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
auto a = views::iota(1, 10);
auto b = istream_view&lt;std::string&gt;(words);
</code>
            </pre>
        </td>
    </tr>
</table>

<aside class="notes">
And this makes sense, right? If you think about the different ways you might represent a *range* that is constant-time
copyable, it must be either by not owning the data (so it just contains a limited number of pointers to the real data),
or by generating the data on demand. And that's exactly what these two do.
</aside>

</section>
