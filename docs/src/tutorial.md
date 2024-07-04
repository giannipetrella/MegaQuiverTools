# Tutorial

## Installation

At the moment the package is not registered,
so you can install it by running the following command in the Julia REPL:

```julia-repl
pkg> add https://github.com/giannipetrella/MegaQuiverTools.git
```

## Examples

To start using MegaQuiverTools in the REPL, one first must import it.

```julia-repl
julia> using MegaQuiverTools
```

Quivers can be built by passing the adjacency matrix to the Quiver() constructor:

```julia-repl
julia> Quiver([0 3; 0 0])
Quiver with adjacency matrix [0 3; 0 0]
```

The constructor accepts an optional string for naming the quiver:

```julia-repl
julia> MyQ = Quiver([0 3; 0 0], "My personal quiver")
My personal quiver, with adjacency matrix [0 3; 0 0]
```

MegaQuiverTools has several constructors in place for many common examples:

```julia-repl
julia> mKroneckerquiver(4)
4-Kronecker quiver, with adjacency matrix [0 4; 0 0]

julia> loopquiver(5)
5-loop quiver, with adjacency matrix [5;;]

julia> subspacequiver(3)
3-subspace quiver, with adjacency matrix [0 0 0 1; 0 0 0 1; 0 0 0 1; 0 0 0 0]

julia> threevertexquiver(1,6,7)
An acyclic 3-vertex quiver, with adjacency matrix [0 1 6; 0 0 7; 0 0 0]
```


Dimension vectors and stability parameters are represented by `Vector{Int}` objects:

```julia-repl
julia> Q = mKroneckerquiver(3); d = [2,3];

julia> θ = canonical_stability(Q, d)
2-element Vector{Int64}:
  9
 -6

julia> iscoprime(d, θ)
true
```
Here, `iscoprime()` checks if ``d`` is θ-coprime, i.e., if none of the
proper subdimension vectors ``0 \neq d' \nleq d`` satisfies ``\theta \cdot d' = 0``.

The bilinear Euler form relative to a quiver Q of any two vectors
in ``\mathbb{Z}Q`` can be computed:

```julia-repl
julia> Q = mKroneckerquiver(3); d = [2,2]; e = [3,4];

julia> Eulerform(Q, d, e)
-10

julia> Eulerform(Q, e, d)
-4
```

One can check if semistable, respectively stable representations
exist for a given dimension vector and stability parameter:

```julia-repl
julia> Q = mKroneckerquiver(3); d = [2,3]; θ = [3,-2];

julia> hassemistables(Q, d, θ)
true

julia> hasstables(Q, d, θ)
true

julia> K2 = mKroneckerquiver(2);

julia> hasstables(K2, [2,2], [1,-1])
false

julia> hassemistables(K2, [2,2], [1,-1])
true
```

One can also determine whether stable representations exist at all
for a given dimension vector by checking if it is a Schur root:

```julia-repl
julia> Q = mKroneckerquiver(3); d = [2,2];

julia> MegaQuiverTools.isSchurroot(Q,d)
true

julia> K2 = mKroneckerquiver(2);

julia> MegaQuiverTools.isSchurroot(K2,d)
false
```

To investigate the Harder-Narasimhan stratification of the parameter space
``\mathrm{R}(Q,\mathbf{d})``, the module provides a recursive closed formula.

```julia-repl
julia> Q = mKroneckerquiver(3); d = [2,3]; θ = [3,-2];

julia> allHNtypes(Q, d, θ)
8-element Vector{Vector{Vector{Int64}}}:
 [[2, 3]]
 [[1, 1], [1, 2]]
 [[2, 2], [0, 1]]
 [[2, 1], [0, 2]]
 [[1, 0], [1, 3]]
 [[1, 0], [1, 2], [0, 1]]
 [[1, 0], [1, 1], [0, 2]]
 [[2, 0], [0, 3]]

julia> isamplystable(Q, d, θ)
true
```

The method `isamplystable()` determines whether the codimension of the θ-semistable locus,
``\mathrm{R}^{\theta-sst}(Q,\mathbf{d})\subset\mathrm{R}(Q,\mathbf{d})``, is at least 2.

The method `allHNtypes()` provides a list of all the Harder-Narasimhan types that appear in the problem.

The method `allTelemanbounds()` computes the bounds to apply Teleman quantization on the non-dense strata.
The output is a dictionary whose keys are the HN types and whose values are the weights themselves.

```julia-repl
julia> Q = mKroneckerquiver(3); d = [2,3]; theta = [3,-2];

julia> allTelemanbounds(Q, d, theta)
Dict{Vector{Vector{Int64}}, Int64} with 7 entries:
  [[2, 2], [0, 1]]         => 20
  [[2, 1], [0, 2]]         => 100
  [[1, 0], [1, 2], [0, 1]] => 100
  [[1, 0], [1, 3]]         => 120
  [[1, 0], [1, 1], [0, 2]] => 90
  [[1, 1], [1, 2]]         => 15
  [[2, 0], [0, 3]]         => 90
```

### Use cases

The following are examples of use cases for MegaQuiverTools

**Verify Teleman inequalities**

In the following example, for each ``i,j`` and on each Harder-Narasimhan stratum,
we compute the weight of ``\mathcal{U}_i^\vee \otimes \mathcal{U}_j`` relative to the
1-PS corresponding to the HN stratum. These are then compared to the Teleman bounds.

```julia-repl
julia> Q = mKroneckerquiver(3); d = [2,3]; theta = [3,-2];

julia> hn = allTelemanbounds(Q,d,theta)
Dict{Vector{Vector{Int64}}, Int64} with 7 entries:
  [[2, 2], [0, 1]]         => 20
  [[2, 1], [0, 2]]         => 100
  [[1, 0], [1, 2], [0, 1]] => 100
  [[1, 0], [1, 3]]         => 120
  [[1, 0], [1, 1], [0, 2]] => 90
  [[1, 1], [1, 2]]         => 15
  [[2, 0], [0, 3]]         => 90

julia> endom = all_weights_endomorphisms_universal_bundle(Q,d,theta)
Dict{Vector{Vector{Int64}}, Vector{Int64}} with 7 entries:
  [[2, 2], [0, 1]]         => [0, 5, -5, 0]
  [[2, 1], [0, 2]]         => [0, 10, -10, 0]
  [[1, 0], [1, 2], [0, 1]] => [0, 10, 15, -10, 0, 5, -15, -5, 0]
  [[1, 0], [1, 3]]         => [0, 15, -15, 0]
  [[1, 0], [1, 1], [0, 2]] => [0, 5, 10, -5, 0, 5, -10, -5, 0]
  [[1, 1], [1, 2]]         => [0, 5, -5, 0]
  [[2, 0], [0, 3]]         => [0, 5, -5, 0]

julia> all(maximum(endom[key]) < hn[key] for key in keys(hn))
true
```

The fact that all of these inequalities are satisfied allows to conclude that the higher cohomology of
``\mathcal{U}_i^\vee \otimes \mathcal{U}_j`` vanishes.
