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

```c++ [2]
using namespace std::views;
for (int i : iota(2) | filter(evens) | transform(square))
	std::cout << i << ' ';
```

</section>
