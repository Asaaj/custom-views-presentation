<section>

```c++ []
using namespace std::views;
for (int i : iota(2) | filter(evens) | transform(square))
	std::cout << i << ' ';
``` 

<div class="huge-text">🎉🎆🎊</div>

</section>
<section data-background-image="images/solar-system.png" data-background-size="contain"></section>
<section data-background-image="images/simplified-gravity.png" data-background-size="contain"></section>
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
concept GravitationalRange =
    std::ranges::range<T> && GravitationalBody<std::ranges::range_value_t<T>>;
```

</section>
<section>

<div class="hl-block huge-text">
$$ F = G\frac{m_1 m_2}{r^2} $$
</div>

</section>

[comment]: <> (<section>)

[comment]: <> (<div class="hl-block huge-text">)

[comment]: <> ($$ \vec{F}_{12} = -G\frac{m_1 m_2}{|\vec{r}_{21}|^2} \hat{r}_{21} $$)

[comment]: <> (</div>)

[comment]: <> (</section>)
<section>

<div class="hl-block huge-text">
$$ F = ma $$
</div>

</section>
<section>

<div class="hl-block huge-text">
$$ \frac{F}{m} = a $$
</div>

</section>
<section>

```c++ [|6,7]
template <GravitationalBody TThisBody, GravitationalRange TAllBodies>
VelocityAndLocation UpdateGravity(TThisBody const& thisBody,
                                  TAllBodies const& allBodies,
                                  float const deltaTime)
{
	auto const force = GetForceOnBody(thisBody, allBodies);
	auto const acceleration = force / thisBody.GetMass();
	auto const newVelocity = thisBody.GetVelocity() + acceleration * deltaTime;
	auto const newLocation = thisBody.GetLocation() + newVelocity * deltaTime;
 
	return { newVelocity, newLocation };
}

```

</section>
<section>

```c++ [|18,19|6-16|12||8]
template <GravitationalBody TThisBody, GravitationalRange TAllBodies>
FVector GetForceOnBody(TThisBody const& thisBody,
                       TAllBodies const& allBodies)
{
    FVector const thisLocation = thisBody.GetLocation();
    auto const singleForceOnThisBody = [&](auto const& otherBody) -> FVector
    {
        if (otherBody == thisBody) return FVector{ 0 };

        auto const otherLocation = otherBody.GetLocation();
        auto const squareDistance = FVector::DistSquared(thisLocation, otherLocation);
        auto const forceSize = G * thisBody.GetMass() * otherBody.GetMass() / squareDistance;

        auto const forceDirection = (otherLocation - thisLocation).GetSafeNormal();
        return forceSize * forceDirection;
    };

    return jtl::transform_reduce(allBodies, FVector{ 0 },
                                 std::plus<>{}, singleForceOnThisBody);
}
```

</section>