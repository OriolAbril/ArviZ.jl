# [ArviZ.jl: Exploratory analysis of Bayesian models in Julia](@id arvizjl)

![CI](https://github.com/arviz-devs/ArviZ.jl/workflows/CI/badge.svg)
[![codecov.io](https://codecov.io/github/arviz-devs/ArviZ.jl/coverage.svg?branch=main)](https://codecov.io/github/arviz-devs/ArviZ.jl?branch=main)
[![Code Style: Blue](https://img.shields.io/badge/code%20style-blue-4495d1.svg)](https://github.com/invenia/BlueStyle)

ArviZ.jl is a Julia interface to the [ArviZ](https://arviz-devs.github.io/arviz/) package for exploratory analysis of Bayesian models.

Please see [ArviZ's documentation](https://arviz-devs.github.io/arviz/) for detailed descriptions of features and usage.
See for example the [gallery](https://arviz-devs.github.io/arviz/examples/index.html) for a sample of the plots and backends ArviZ supports.

This documentation will focus on differences between ArviZ.jl and ArviZ, applications using Julia's probabilistic programming languages (PPLs), and examples in Julia.

## [Purpose](@id purpose)

Besides removing the need to explicitly import ArviZ with [PyCall.jl](https://github.com/JuliaPy/PyCall.jl), ArviZ.jl extends ArviZ with functionality for converting Julia types into ArviZ's [`InferenceData`](https://arviz-devs.github.io/arviz/getting_started/XarrayforArviZ.html) format.
It also allows smoother usage with [PyPlot.jl](https://github.com/JuliaPy/PyPlot.jl) and provides functions that can be overloaded by other packages to enable their types to be used with ArviZ.

## [Installation](@id installation)

To use with the default Python environment, first [install ArviZ](https://github.com/arviz-devs/arviz#installation).
From the Julia REPL, type `]` to enter the Pkg REPL mode and run

```
pkg> add ArviZ
```

To install ArviZ.jl with its Python dependencies in Julia's private conda environment, in the console run

```console
PYTHON="" julia -e 'using Pkg; Pkg.add("PyCall"); Pkg.build("PyCall"); Pkg.add("ArviZ")'
```

For specifying other Python versions, see the [PyCall documentation](https://github.com/JuliaPy/PyCall.jl).

## [Design](@id design)

ArviZ.jl supports all of ArviZ's [API](https://arviz-devs.github.io/arviz/api/index.html), except for its [Numba functionality](@ref knownissues).
See ArviZ's API documentation for details.

ArviZ.jl wraps ArviZ's API functions and closely follows ArviZ's design.
It also supports conversion of [MCMCChains.jl](https://github.com/TuringLang/MCMCChains.jl)'s `Chains` as returned by [Turing.jl](https://turing.ml), [CmdStan.jl](https://github.com/StanJulia/CmdStan.jl), [StanSample.jl](https://github.com/StanJulia/StanSample.jl), and others into ArviZ's `InferenceData` format and of [SampleChains.jl](https://github.com/cscherrer/SampleChains.jl)'s `AbstractChain`s and `MultiChain` as returned by [Soss.jl](https://github.com/cscherrer/Soss.jl).
See [Quickstart](./quickstart) for examples.

The package is intended to be used with [PyPlot.jl](https://github.com/JuliaPy/PyPlot.jl).

ArviZ.jl development occurs on [GitHub](https://github.com/arviz-devs/ArviZ.jl).
Issues and pull requests are welcome.

## [Differences from ArviZ](@id differences)

In ArviZ, functions in the [API](https://arviz-devs.github.io/arviz/api/index.html) are usually called with the package name prefix, (e.g. `arviz.plot_posterior`).
In ArviZ.jl, most of the [same functions](@ref api) are exported and therefore can be called without the prefix (e.g. `plot_posterior`).
The exception are `from_xyz` converters for packages that have no (known) Julia wrappers.
These functions are not exported to reduce namespace clutter.

For `InferenceData` inputs, [`summarystats`](@ref) replaces `arviz.summary` to avoid confusion with `Base.summary`.
For arbitrary inputs and the full functionality of `arviz.summary`, use [`ArviZ.summary`](@ref), which is not exported.

ArviZ.jl transparently interconverts between `arviz.InferenceData` and our own [`InferenceData`](@ref), used for dispatch.
`InferenceData` has identical usage to its Python counterpart.

Functions that in ArviZ return Pandas types here return [DataFrames.jl](https://github.com/JuliaData/DataFrames.jl) types.

ArviZ includes the context managers `rc_context` and `interactive_backend`.
ArviZ.jl includes the functions [`with_rc_context`](@ref) and [`with_interactive_backend`](@ref), which can be used with a nearly identical syntax.
`with_interactive_backend` here is not limited to an IPython/IJulia context.

In place of `arviz.style.use` and `arviz.style.available`, ArviZ.jl provides [`ArviZ.use_style`](@ref) and [`ArviZ.styles`](@ref).

## [Extending ArviZ.jl](@id extendingarviz)

To use a custom data type with ArviZ.jl, simply overload [`convert_to_inference_data`](@ref) to convert your input(s) to an [`InferenceData`](@ref).

## [Known Issues](@id knownissues)

ArviZ.jl uses [PyCall.jl](https://github.com/JuliaPy/PyCall.jl) to wrap ArviZ.
At the moment, Julia segfaults if Numba is imported, which ArviZ does if it is available.
For the moment, the workaround is to [specify a Python version](https://github.com/JuliaPy/PyCall.jl#specifying-the-python-version) that doesn't have Numba installed.
See [this issue](https://github.com/JuliaPy/PyCall.jl/issues/220) for more details.
