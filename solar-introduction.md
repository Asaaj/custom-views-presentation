<section>

<div class="hl-block left-align">

### About Me

- Software Engineer at Keysight Technologies, Inc.
- High Performance Oscilloscope Software Team
- Contact: jacob.rice.cpp@gmail.com

<center>
    <img src="images/keysight-logo.png" width="400" style="margin-top: 30px"
         alt="Keysight Technologies, Inc." />
</center>

</div>

<aside class="notes">

**Read slide**

I really like writing library code, but I'm not a maintainer of any big open-source projects or anything like that. 
That's what I mean when I say "custom views for the rest of us"; I want to show that views are actually quite 
approachable, even for those of us who don't write this kind of code all the time.

My goal, whenever I talk or teach, is to help build an intuition. Memorization is okay, but intuition is the only way 
to really master something. To that end, my goal here is for everyone watching this talk to end up with a similar level 
of understanding of views as I have, but in a much shorter time.

</aside>

</section>
<section>

```c++ []
using namespace std::views;
for (int i : iota(2) | filter(even) | transform(square))
	std::cout << i << ' ';
``` 

<div class="huge-text">🎉🎆🎊</div>

<aside class="notes">

If you don't know about views, this talk isn't really intended as an introduction, but I hope by the end of the talk you 
will nevertheless understand them thoroughly.

Here's an example of C++20's views in action. They offer this clean, composable, lazily-evaluated syntax for writing
common algorithms, but when you first see them, they kind of look like magic. They have a very different syntax from 
anything else in the STL, so it may seem daunting to first take a look under the hood. To make it more approachable, I 
tried implementing my own view, and I want to share that process with you. Let's start with the motivating example.

</aside>

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

#### This can be done with a <code>transform_reduce</code>:

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

This line is telling me that the input data to my algorithm is wrong. If my algorithm has to correct the data it's 
getting, that means it's getting the wrong data. I want the algorithm to just do the calculation.
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
