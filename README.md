<!-- solve-problem:edb1465e-dc39-4bbc-8e87-c6189f6ce346 -->
# Polylogarithmic-overhead exact maximum flow

A [Tangle Solve problem](https://solve.tangle.tools/problems/edb1465e-dc39-4bbc-8e87-c6189f6ce346).

## What is the problem?

Given a directed graph $G=(V,E)$ with $n=|V|$, $m=|E|$, integral capacities $c(e) \in \{1,\dots,U\}$ with $U \le \mathrm{poly}(n)$ (so capacities have $O(\log n)$-bit encodings), and distinguished vertices $s,t$, compute the **exact** maximum $s$-$t$ flow value and flow (an integral optimal flow) in time
$$m \cdot \mathrm{polylog}(nU),$$
i.e., linear time in the input size up to a fixed polylogarithmic factor, on a standard (sequential) RAM model with exact integer arithmetic. "Overhead" means the multiplicative factor beyond $\Theta(m)$ input-reading time. Note the target permits dependence on $\log U$ — this is not a strongly-polynomial $m\,\mathrm{polylog}(n)$ demand (though that variant is also interesting and open; state clearly which you attack). Precision requirements: the algorithm must output an exactly optimal integral flow, not a $(1-\varepsilon)$-approximation, and the polylog exponent/factor should be a fixed constant-independent function, not $m^{o(1)}$ hidden in a slowly growing term.

## Why does it matters?

Almost-linear $m^{1+o(1)}$ bounds hide factors like $\exp(O(\sqrt{\log n \log\log n}))$ or $2^{O(\log^{2/3} n)}$, which dominate real-world instance sizes; a genuine polylog overhead would make near-linear max flow an actually deployable primitive. Exact max flow reduces to (and underlies) min-cost flow, bipartite matching, scheduling, circulation, and OR/transportation optimization at scale. It would also settle the natural question of whether the $\Omega(m)$ input-size lower bound is tight up to polylog factors for this central problem.

## What's already known?

- The breakthrough of Chen–Kyng–Liu–Peng–Probst Gutenberg–Sachdeva (FOCS 2022) gives exact min-cost (hence max) flow in $m^{1+o(1)} \log U$ time — specifically roughly $m \cdot 2^{O(\sqrt{\log m \log\log m})}$-type factors via the interior-point method with dynamic low-stretch spanning trees / data structures. The $o(1)$ exponent is *not* a fixed polylog.
- Subsequent work sharpened the data-structure and IPM machinery (e.g., dynamic spanners, $\ell_2$/$\ell_p$ IPM steps by Kyng–Peng–Schewior and others — verify exact attributions); the current best overheads are still $m^{o(1)}$, not $\mathrm{polylog}$.
- For **approximate** max flow, nearly-linear $m\,\mathrm{polylog}(n)\,\varepsilon^{-1}$ algorithms are long known (Sherman; Kelner–Lee–Orecchia–Sidford; Peng), but exactness from approximation requires $\varepsilon \approx 1/(nU)$, costing an extra $U$ or $n$ factor naively.
- The main obstacle: IPM convergence gives $\sqrt{m}$-ish iterations; each iteration must be answered in $\mathrm{polylog}$ amortized time by a dynamic data structure, and known dynamic-tree/spanner structures incur $m^{o(1)}$ overheads that resist compression to fixed polylog.

## What would count as solving it?

A (sequential, randomized is acceptable) algorithm with worst-case expected time $m \cdot (\log nU)^{O(1)}$ with an explicit constant in the exponent, outputting an exact integral max flow, plus a proof of correctness and running time. Partial-but-accepted variants:
1. Time $m\,\mathrm{polylog}(n)\,\mathrm{polylog}(U)$ for $U \le \mathrm{poly}(n)$ (full solution).
2. $m \cdot 2^{O((\log\log m)^c)}$ for constant $c$ — major progress, not full resolution.
3. A conditional impossibility or lower bound showing fixed-polylog overhead is unlikely under some fine-grained/data-structure assumption would also be a significant resolution of the census question.

## How to start

1. Read Chen et al. 2022 (arXiv:2203.00671), Sections on the robust IPM and the dynamic data structure; identify exactly where the $2^{O(\sqrt{\log n\log\log n})}$ factor arises (low-stretch trees + dynamic shortcut maintenance).
2. Read the follow-up line on faster IPM iterations / simplified data structures (e.g., "A simple algorithm for maximum flow" style works by Chen et al., and dynamic spanning-tree-with-low-stretch literature — verify) to see whether the bottleneck has already shrunk.
3. Attack the data-structure bottleneck: can amortized $\mathrm{polylog}$-per-update maintenance of approximate electrical flows / low-stretch trees replace the current $m^{o(1)}$ structures, perhaps trading exact stretch guarantees for exactness recovered via IPM duality/certification?
4. Alternative: try bit-encoding tricks — since $U$ is polynomially bounded, attempt to convert an approximate flow at $\varepsilon = 1/\mathrm{poly}(n)$ into an exact one via rounding/cycle-cancelling in $m\,\mathrm{polylog}$ time.

Main obstacle: replacing every $m^{o(1)}$ amortized cost in the IPM machinery simultaneously with polylog; improving one component doesn't suffice.

## Key references

- Chen, L., Kyng, Y., Liu, Y. P., Peng, R., Probst Gutenberg, M., Sachdeva, S. (2022). *Maximum Flow and Minimum-Cost Flow in Almost-Linear Time.* arXiv:2203.00671. Establishes $m^{1+o(1)}\log U$ exact min-cost flow via a robust IPM plus dynamic low-stretch spanning tree data structures. https://arxiv.org/abs/2203.00671
- (verify) Follow-ups simplifying/improving the data structure overhead, e.g., Chen et al., *Maximum flow in almost-linear time on sparse graphs* / dynamic low-stretch hierarchy works — check arXiv listings citing 2203.00671 for the current best $o(1)$ exponent.
- Sherman, J. (2013). *Nearly Maximum Flows in Nearly Linear Time.* FOCS 2013. Nearly-linear approximate max flow via oblivious routing — the approximation baseline that exactness must improve on.
- Kyng, R., Peng, R., Schewior, S., et al. — $\ell_p$-progress IPM analyses relevant to reducing iteration counts (verify exact papers).

## Warnings

- The census phrasing "polylogarithmic overhead" is ambiguous about $\log U$ dependence; the mainstream reading (used here) is $m\,\mathrm{polylog}(nU)$. State your convention up front.
- "Almost-linear" ($m^{1+o(1)}$) and "linear-up-to-polylog" are genuinely different targets; don't claim success with a $2^{\sqrt{\log n}}$-type factor.
- Don't confuse approximate ($1-\varepsilon$) with exact; rounding approximate to exact typically costs $\log(nU)$ or worse factors and needs care with integrality.
- Randomized model conventions (worst-case expected vs. high probability) matter for acceptance; specify them.
- Beware claimed "linear-time" max-flow results for special graph classes or unit/small capacities (e.g., $O(m\sqrt{\log n})$-type or bipartite-matching-specific bounds) — they do not resolve the general problem.

---
*`ALGO-000120` · unverified*

## Acceptance guidance

A (sequential, randomized is acceptable) algorithm with worst-case expected time $m \cdot (\log nU)^{O(1)}$ with an explicit constant in the exponent, outputting an exact integral max flow, plus a proof of correctness and running time. Partial-but-accepted variants:
1. Time $m\,\mathrm{polylog}(n)\,\mathrm{polylog}(U)$ for $U \le \mathrm{poly}(n)$ (full solution).
2. $m \cdot 2^{O((\log\log m)^c)}$ for constant $c$ — major progress, not full resolution.
3. A conditional impossibility or lower bound showing fixed-polylog overhead is unlikely under some fine-grained/data-structure assumption would also be a significant resolution of the census question.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/edb1465e-dc39-4bbc-8e87-c6189f6ce346?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: ALGO-000120.

- [Public source catalog](https://drewstone.github.io/problems/#/p/ALGO-000120)
