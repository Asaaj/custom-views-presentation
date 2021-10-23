<section>

<table class="hl-block compare-columns conclusion">
	<colgroup>
		<col style="width: 50%" />
		<col style="width: 50%" />
	</colgroup>
	<tr>
        <th class="cell-right-align">Topics</th>
        <th class="cell-left-align">Lessons</th>
	</tr>
	<tr class="fragment">
		<td class="cell-right-align"><code>all_pairs_view</code></td>
		<td>
			<ul>
				<li>Views are tricky to write but easy to use.</li>
				<li>C++20 isn’t <i>quite</i> ready for custom views.</li>
				<li>range-v3 may help until closures are fixed.</li>
			</ul>
		</td>
    </tr>
    <tr class="fragment">
		<td class="cell-right-align"><code>vector&lt;bool></code>, proxy iterators</td>
		<td>
			<ul>
				<li>Proxies are unintuitive, but valid in C++20.</li>
				<li>Create a new world of novel iterator types.</li>
				<li>Keep them in mind in your generic code.</li>
			</ul>
		</td>
    </tr>
    <tr class="fragment">
		<td class="cell-right-align"><code>iter_swap</code>, <code>iter_move</code></td>
		<td>
			<ul>
				<li>Customization points are powerful.</li>
				<li>Easy to use and (usually) easy to customize.</li>
				<li>Prefer <code>std::ranges</code> algorithms.</li>
			</ul>
		</td>
    </tr>
</table>

</section>
<section>

<div class="hl-block pretty-big-text">
Thank you.
</div>

</section>
