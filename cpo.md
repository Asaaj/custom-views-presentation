<section>

<div class="hl-block pretty-big-text">Customization Point Objects</div>

</section>
<section>

```c++ [|5-6]
template <class T>
void AlgorithmThatSwaps(T& t1, T& t2)
{
    // Algorithm stuff...
	using std::swap;
	swap(t1, t2);
	// More algorithm
}
```

<div class="hl-block code-note" style="top: -150px;">

This has always been an awkward pattern:

</div>

</section>
<section>

<div class="hl-block left-align">

### Pre-C++20

- `swap`, `begin`, and `end` are "customization points".
- Special behavior for user types.
- Custom versions can't enforce concepts.
- Much easier to get wrong than right.

</div>

</section>
<section>

<div class="hl-block left-align">

### C++20 solution (N4381)

- Moving forward: _semiregular objects_ that do ADL internally.
- One place for type constraints == no user circumventing.
- Always easy to use.
- Currently, only in the `std::ranges` namespace.

</div>

</section>
<section>

```c++ [5]
template <class T>
void AlgorithmThatSwaps(T& t1, T& t2)
{
    // Algorithm stuff...
	std::ranges::swap(t1, t2);
	// More algorithm
}
```

<div class="hl-block code-note fragment" style="top: -200px;">

Calls the custom version, if it exists<br/>(same for `begin` and `end`):

</div>

</section>
