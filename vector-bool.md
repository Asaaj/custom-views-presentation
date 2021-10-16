<section>

<div class="hl-block pretty-big-text">
    <code class="cpp">
        vector&lt;<span class="hljs-keyword">bool</span>&gt;
    </code>
</div>

</section>
<section>

<div class="hl-block left-align">

### cppreference:

<div style="font-size: 30px;">

`std::vector<bool>` behaves similarly to `std::vector`, but in order to be 
<span class="fragment hl-text" data-fragment-index="1">space efficient</span>, it:

- <span class="fragment hl-text" data-fragment-index="1">Does not necessarily store its elements as a contiguous array.</span>
- <span class="fragment hl-text" data-fragment-index="1">Exposes class `std::vector<bool>::reference` as a method of accessing individual bits.</span> In particular, objects of this class are returned by `operator[]` by value.
- Does not use `std::allocator_traits::construct` to construct bit values.
- Does not guarantee that different elements in the same container can be modified concurrently by different threads.

</div>

</div>

</section>
<section>

```c++
std::vector<bool> v{ true, true, false, true };
```

<br/>

<table class="byte-layout">
<tr>
<td>0b0000'0001</td>
<td>0b0000'0001</td>
<td>0b0000'0000</td>
<td>0b0000'0001</td>
</tr>
</table>

<img src="images/crossed-bytes.png" 
     class="fragment" data-fragment-index="1"
     style="position: absolute; top: 85px; left: 150px; width: 980px; height: 150px;" />

<br/>

<table class="fragment byte-layout" data-fragment-index="1">
<tr>
<td>0b0000'1101</td>
</tr>
</table>

<span style="position: absolute; top: 280px; left: 810px; font-size: 55px;"
      class="fragment" data-fragment-index="1">✔️</span>

</section>
<section>

<div class="hl-block">

[comment]: <> (TODO: Very hard to read)

[comment]: <> (TODO: Make this in markdown, that'll be much easier to maintain I think)

<table class="cpp-ref-types">
<tr>
    <th>Member type</th>
    <th>Definition</th>
</tr>
<tr>
    <td><code>value_type</code></td>
    <td><code>T</code></td>
</tr>
<tr>
    <td><code>allocator_type</code></td>
    <td><code>Allocator</code></td>
</tr>
<tr>
    <td><code>size_type</code></td>
    <td>Unsigned integer type (usually <code>std::size_t</code>)</td>
</tr>
<tr>
    <td><code>difference_type</code></td>
    <td>Signed integer type (usually <code>std::ptrdiff_t</code>)</td>
</tr>
<tr>
    <td><code>reference</code></td>
    <td><code>Allocator::reference</code> <aside class="subtle">(until C++11)</aside><br/>
    <code>value_type&</code> <aside class="subtle">(since C++11)</aside></td>
</tr>
<tr>
    <td><code>const_reference</code></td>
    <td><code>Allocator::const_reference</code> <aside class="subtle">(until C++11)</aside><br/>
    <code>const value_type&</code> <aside class="subtle">(since C++11)</aside></td>
</tr>
<tr>
    <td><code>pointer</code></td>
    <td><code>Allocator::pointer</code> <aside class="subtle">(until C++11)</aside><br/>
    <code>std::allocator_traits&lt;Allocator>::pointer</code> <aside class="subtle">(since C++11)</aside></td>
</tr>
<tr>
    <td><code>const_pointer</code></td>
    <td><code>Allocator::const_pointer</code> <aside class="subtle">(until C++11)</aside><br/>
    <code>std::allocator_traits&lt;Allocator>::const_pointer</code> <aside class="subtle">(since C++11)</aside></td>
</tr>
<tr>
    <td><code>iterator</code></td>
    <td><i>LegacyRandomAccessIterator</i> to <code>value_type</code></td>
</tr>
<tr>
    <td><code>const_iterator</code></td>
    <td><i>LegacyRandomAccessIterator</i> to <code>const value_type</code></td>
</tr>
<tr>
    <td><code>reverse_iterator</code></td>
    <td><code>std::reverse_iterator&lt;iterator></code></td>
</tr>
<tr>
    <td><code>const_reverse_iterator</code></td>
    <td><code>std::reverse_iterator&lt;const_iterator></code></td>
</tr>

</table>

</div>

</section>
<section>

<div class="hl-block">

[comment]: <> (TODO: Very hard to read)

[comment]: <> (TODO: Make this in markdown, that'll be much easier to maintain I think)

<table class="cpp-ref-types">
<tr>
    <th>Member type</th>
    <th>Definition</th>
</tr>
<tr>
    <td><code>value_type</code></td>
    <td><code class="hljs-keyword">bool</code></td>
</tr>
<tr>
    <td><code>allocator_type</code></td>
    <td><code>Allocator</code></td>
</tr>
<tr class="hl-text fragment" data-fragment-index="1">
    <td><code>size_type</code></td>
    <td>implementation-defined</td>
</tr>
<tr class="hl-text fragment" data-fragment-index="1">
    <td><code>difference_type</code></td>
    <td>implementation-defined</td>
</tr>
<tr class="hl-text fragment" data-fragment-index="3">
    <td><code>reference</code></td>
    <td>proxy class representing a reference to a single bool <aside class="subtle">(class)</aside></td>
</tr>
<tr class="hl-text fragment" data-fragment-index="2">
    <td><code>const_reference</code></td>
    <td><code class="hljs-keyword">bool</code></td>
</tr>
<tr class="hl-text fragment" data-fragment-index="1">
    <td><code>pointer</code></td>
    <td>implementation-defined</td>
</tr>
<tr class="hl-text fragment" data-fragment-index="1">
    <td><code>const_pointer</code></td>
    <td>implementation-defined</td>
</tr>
<tr class="hl-text fragment" data-fragment-index="1">
    <td><code>iterator</code></td>
    <td>implementation-defined</td>
</tr>
<tr class="hl-text fragment" data-fragment-index="1">
    <td><code>const_iterator</code></td>
    <td>implementation-defined</td>
</tr>
<tr>
    <td><code>reverse_iterator</code></td>
    <td><code>std::reverse_iterator&lt;iterator></code></td>
</tr>
<tr>
    <td><code>const_reverse_iterator</code></td>
    <td><code>std::reverse_iterator&lt;const_iterator></code></td>
</tr>

</table>

</div>

</section>
<section>

<div class="hl-block left-align">

## "When Is a Container Not a Container?"

<br />

### - Herb Sutter, _C++ Report_, May 1999

</div>

</section>
<section>

```c++ []
template <class T>
void f(T& t)
{
	typename T::value_type* p1 = &t[0];
	typename T::value_type* p2 = &*t.begin();
	// ... do something with *p1 and *p2 ...
}
```

</section>
<section>

<div class="hl-block pretty-big-text">

What can we do about this?

</div>

</section>
<section>

<div class="hl-block pretty-big-text">Don't write code like this:</div>

```c++ []
template <class T>
void f(T& t)
{
	typename T::value_type* p1 = &t[0];
	typename T::value_type* p2 = &*t.begin();
	// ... do something with *p1 and *p2 ...
}
```

</section>
<section>

<div class="r-stack hl-block">
    <div class="pretty-big-text">
        <p>
            Write code expecting "proxies".
        </p>
    </div>
    <div class="fragment" data-fragment-index="1"
         style="position: absolute; top: 80px; left: 515px; width: 610px; height: 10px; background-color: #DA3939; box-shadow: 0 0 7px #000000A0;"></div>
    <div class="fragment" data-fragment-index="1">
        <h2 class="correction" style="top: -60px; left: 550px;">that conforms</h2>
    </div>
    <div class="fragment" data-fragment-index="2">
        <p class="correction handwriting"
           style="top: -200px; left: 780px; text-align: right;">with C++20<br />^</p>
    </div>
</div>

</section>
