<section>

<div class="hl-block left-align">

### About Me

- Software Engineer at Keysight Technologies, Inc.
- High Performance Oscilloscope Software Team
- Contact: jacob@rice.systems

<center>
    <img src="images/keysight-logo.png" width="400" style="margin-top: 30px"
         alt="Keysight Technologies, Inc." />
</center>

</div>

</section>
<section>

```c++ []
using namespace std::views;
for (int i : iota(2) | filter(even) | transform(square))
	std::cout << i << ' ';
``` 

<div class="huge-text">🎉🎆🎊</div>

</section>
<section data-background-image="images/solar-system.png" data-background-size="contain">
</section>
<section data-background-image="images/simplified-gravity.png" data-background-size="contain">
</section>
<section>

```c++ []
template <class T>
concept GravitationalBody = requires(T const t)
{
	{ t.GetMass() } -> std::floating_point;
	{ t.GetLocation() } -> std::same_as<FVector>;
	{ t.GetVelocity() } -> std::same_as<FVector>;
};

template <class T>
concept GravitationalRange = GravitationalBody<std::ranges::range_value_t<T>>;
```

</section>
<section data-background-image="images/simplified-gravity.png" data-background-size="contain">
</section>
<section data-background-image="images/simplified-gravity-red-forces.png" data-background-size="contain">
</section>
<section data-background-image="images/simplified-gravity-total-red-forces.png" data-background-size="contain">
</section>
<section>

<div class="fragment">
This can be done with a <code>transform_reduce</code>:
</div>

```python
def GetForceOnBody(this_body, all_bodies):
    all_forces = list()
    for other_body in all_bodies:
        all_forces.append(SingleForceOnThisBody(this_body, other_body))
    return sum(all_forces)
```

</section>
<section>

```c++ [|14-15|5-12||7]
template <GravitationalBody TThisBody, GravitationalRange TAllBodies>
FVector GetForceOnBody(TThisBody const& thisBody,
                       TAllBodies const& allBodies)
{
    auto const singleForceOnThisBody = [&](auto const& otherBody) -> FVector
    {
        if (otherBody == thisBody) return FVector{ 0 };
        
        auto const forceSize = CalculateForce(thisBody, otherBody);
        auto const forceDirection = ForceDirection(thisBody, otherBody);
        return forceSize * forceDirection;
    };

    return jtl::transform_reduce(allBodies, FVector{ 0 },
                                 std::plus<>{}, singleForceOnThisBody);
}
```

<aside class="notes">
This is a code smell. It's not a big smell, it's not like a dumpster. It's more like when you catch a whiff of your 
kitchen trash and think "I should probably take that out". But this is going to be the inspiration for a different 
solution.
</aside>

</section>
<section data-background-image="images/planet-transformation-bad.png" data-background-size="contain">
</section>
<section data-background-image="images/planet-transformation-bad-x.png" data-background-size="contain">
</section>
<section data-background-image="images/planet-transformation.png" data-background-size="contain">
<aside class="notes">
What I really want is to take my collection of gravitational bodies and to see it as groups of pairs, so that I can 
easily see each body paired with everything that exerts a force on it. There are a lot of ways of doing this, of course. 
One option would be to use a regular old transform to create a new collection, but clearly that would grow too fast. 
For an input set of N elements, the output is N^2-N elements, so if we added an asteroid belt, we’d run out of RAM. 
Using manual loops is hard to parallelize, and even using algorithms like I showed before doesn’t give you much control. 
So instead, I would like to represent my collection in this new way, as a new collection, but I want to do it without 
running out of RAM. And we can achieve that with a C++20 view.
</aside>
</section>
