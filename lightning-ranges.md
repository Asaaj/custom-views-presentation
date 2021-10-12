<section>

<div class="hl-block pretty-big-text">
Ranges and Views
</div>

</section>
<section>

```c++ []
std::vector<int> v { /* ... */ };

auto max = std::max_element(std::begin(v), std::end(v));
```

</section>
<section>

```c++ []
std::vector<int> v { /* ... */ };

auto max = std::ranges::max_element(v);
```

</section>
<section>

```c++ []
std::vector<int> v { /* ... */ };

auto sum = std::ranges::accumulate(v, 0); // Uh oh! Doesn't exist
```

</section>
<section>

<div class="hl-block pretty-big-text">

C++20 constrains algorithms in `<algorithm>`, but not `<numeric>`. 

</div>

</section>
<section>

<div class="hl-block left-align">

### Views

- Offer lazily-evaluated, composable algorithm syntax.
- Rarely make any copies; just compose transformations.
- Also support "infinite" "containers" (e.g. `std::views::iota`).

</div>

</section>
<section>

```c++ []
auto TrimFront(std::string const& str) {
    return std::string{ std::find_if(str.begin(), str.end(), 
                        std::not_fn(::isspace)), str.end() };
}

auto TrimBack(std::string const& str) {
    std::string output{ std::find_if(str.rbegin(), str.rend(), 
                        std::not_fn(::isspace)), str.rend() };
    std::reverse(output.begin(), output.end());
    return output;
}

std::string TrimString(std::string const& str) {
    return TrimFront(TrimBack(str));
}
```

</section>
<section>

```c++ [|1|1-5|1-7|]
inline constexpr auto TrimFront = std::views::drop_while(::isspace);

inline constexpr auto TrimBack = std::views::reverse
                               | TrimFront
                               | std::views::reverse;

inline constexpr auto Trim = TrimFront | TrimBack;

std::string TrimString(std::string const& str) {
    return str | Trim | rangesnext::to<std::string>;
}
```

</section>
