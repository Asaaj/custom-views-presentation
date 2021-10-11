<section>

<div class="hl-block pretty-big-text">
Concepts
</div>

</section>
<section>

```c++ []
template <class T, class U>
void ReplaceFirst(T& t, U&& u)
{
    auto* first = &t[0];
    *first = std::forward<U>(u);
}
```

</section>
<section>

```c++ [|4]
std::vector<int> v{ 1, 2, 3 };
ReplaceFirst(v, 0);
ReplaceFirst(v, 10.0f);
ReplaceFirst(10, 5);
```

</section>
<section>

```
<source>(13): error C2109: subscript requires array or pointer type
<source>(25): see reference to function template instantiation 
              'void ReplaceFirst<int,int>(T &,U &&)' being compiled
        with
        [
            T=int,
            U=int
        ]
<source>(14): error C3536: 'first': cannot be used before it is initialized
<source>(14): error C2100: illegal indirection
Compiler returned: 2
```

</section>
<section>

```c++ [1-2]
template <class T, class U>
void ReplaceFirst(T& t, U&& u)
{
    auto* first = &t[0];
    *first = std::forward<U>(u);
}
```

</section>
<section>

```c++ [1-2]
// Explicit types:
void ReplaceFirst(std::vector<int>& t, int u)
{
    auto* first = &t[0];
    *first = std::forward<U>(u);
}
```

</section>
<section>

```c++ [1-2]
template <std::ranges::random_access_range T, class U>
void ReplaceFirst(T& t, U&& u)
{
    auto* first = &t[0];
    *first = std::forward<U>(u);
}
```

</section>
<section>

```
<source>(25): error C2672: 'ReplaceFirst': no matching overloaded function found
<source>(25): error C7602: 'ReplaceFirst': the associated constraints are not satisfied
<source>(10): see declaration of 'ReplaceFirst'
Compiler returned: 2
```

</section>
<section>

<pre><code style="font-size: 17px; line-height: 1.2;"><source>: In function 'int main()':
<source>:25:17: error: no matching function for call to 'ReplaceFirst(int&, int)'
   25 |     ReplaceFirst(i, 5);
      |     ~~~~~~~~~~~~^~~~~~
<source>:10:6: note: candidate: 'template&lt;class T, class U>  requires  random_access_range&lt;T> void ReplaceFirst(T&, U&&)'
   10 | void ReplaceFirst(T& t, U&& u)
      |      ^~~~~~~~~~~~
<source>:10:6: note:   template argument deduction/substitution failed:
<source>:10:6: note: constraints not satisfied
In file included from /.../include/c++/11.2.0/string_view:48,
                 from /.../include/c++/11.2.0/bits/basic_string.h:48,
                 from /.../include/c++/11.2.0/string:55,
                 from /.../include/c++/11.2.0/bits/locale_classes.h:40,
                 from /.../include/c++/11.2.0/bits/ios_base.h:41,
                 from /.../include/c++/11.2.0/streambuf:41,
                 from /.../include/c++/11.2.0/bits/streambuf_iterator.h:35,
                 from /.../include/c++/11.2.0/iterator:66,
                 from /.../include/c++/11.2.0/ranges:43,
                 from <source>:2:
/.../include/c++/11.2.0/bits/ranges_base.h: In substitution of 'template&lt;class T, class U> requires
                                            random_access_range&lt;T> void ReplaceFirst(T&, U&&) [with T = int; U = int]':
<source>:25:17:   required from here
/.../include/c++/11.2.0/bits/ranges_base.h:574:13:   required for the satisfaction of 'range&lt;_Tp>' [with _Tp = int]
/.../include/c++/11.2.0/bits/ranges_base.h:634:13:   required for the satisfaction of 'input_range&lt;_Tp>' [with _Tp = int]
/.../include/c++/11.2.0/bits/ranges_base.h:638:13:   required for the satisfaction of 'forward_range&lt;_Tp>' [with _Tp = int]
/.../include/c++/11.2.0/bits/ranges_base.h:643:13:   required for the satisfaction of 'bidirectional_range&lt;_Tp>' [with _Tp = int]
/.../include/c++/11.2.0/bits/ranges_base.h:648:13:   required for the satisfaction of 'random_access_range&lt;T>' [with T = int]
/.../include/c++/11.2.0/bits/ranges_base.h:574:21:   in requirements with '_Tp& __t' [with _Tp = int]
/.../include/c++/11.2.0/bits/ranges_base.h:576:22: note: the required expression 'std::ranges::__cust::begin(__t)' is invalid
  576 |         ranges::begin(__t);
      |         ~~~~~~~~~~~~~^~~~~
/.../include/c++/11.2.0/bits/ranges_base.h:577:20: note: the required expression 'std::ranges::__cust::end(__t)' is invalid
  577 |         ranges::end(__t);
      |         ~~~~~~~~~~~^~~~~
cc1plus: note: set '-fconcepts-diagnostics-depth=' to at least 2 for more detail
Compiler returned: 1
</code></pre>

</section>
<section>

```c++ [1-2]
template <std::ranges::random_access_range T, class U>
void ReplaceFirst(T& t, U&& u)
{
    auto* first = &t[0];
    *first = std::forward<U>(u);
}
```

</section>
<section>

```c++ [1-3]
template <std::ranges::random_access_range T, class U>
void ReplaceFirst(T& t, U&& u) requires 
    std::assignable_from<std::ranges::range_reference_t<T>, decltype(u)>
{
    auto* first = &t[0];
    *first = std::forward<U>(u);
}
```

</section>
<section>

```
<source>(45): error C2672: 'ReplaceFirst': no matching overloaded function found
<source>(45): error C2893: Failed to specialize function template
                           'void ReplaceFirst(T &,U &&)'
<source>(33): note: see declaration of 'ReplaceFirst'
<source>(45): note: With the following template arguments:
<source>(45): note: 'T=std::vector<int,std::allocator<int>>'
<source>(45): note: 'U=const char (&)[11]'
Compiler returned: 2
```

</section>
<section>

<div class="hl-block pretty-big-text">

Few to no templates should ever be written with fully generic types.

</div>

</section>
<section>

```c++ [|1-10|12-13]
template <class T>
concept GravitationalBody = requires(T const t)
{
	{ t.GetMass() } -> std::floating_point;
	{ t.GetLocation() } -> std::same_as<FVector>;
	{ t.GetVelocity() } -> std::same_as<FVector>;
	
	/* I can do this if I don't care about return type: */
    // t.GetMass()
};

template <class T>
concept GravitationalRange = GravitationalBody<std::ranges::range_value_t<T>>;
```

</section>
<section>

```c++ [1|3-4|6-7|9-10|12]
static_assert(GravitationalBody<SomeType>);

template <class T> requires GravitationalBody<T>
void F(T& t);

template <GravitationalBody T>
void F(T& t);

template <class T>
void F(T& t) requires GravitationalBody<decltype(t)>;

void F(GravitationalBody auto& t);
```

</section>
