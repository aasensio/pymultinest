# pymultinest


This repository contains the source function for the [Multinest](http://arxiv.org/abs/0809.3437) 
nested sampling code of F. Feroz, M.P. Hobson, & M. Bridges. The Python wrapper has been developed by
Johannes Buchner [PyMultinest](https://github.com/JohannesBuchner/PyMultiNest)

## Installation

Following these steps, you should be able to call Multinest from Python:

1. Compile the library. Enter into directory ``src`` and type ``make libmultinest.so``. You first need
to be sure that the LAPACK library is accesible in your system. Additionally, you need
to have the GFortran compiler installed in your system.

2. The shared library ``libmultinest.so`` can be placed wherever you want in your
file system, provided you add the directory to LD_LIBRARY_PATH.

3. Install the python wrapper. This is easily done with ``pip install pymultinest``.

## Example

    from __future__ import absolute_import, unicode_literals, print_function
    import pymultinest
    import math, os
    if not os.path.exists("chains"): os.mkdir("chains")

    # our probability functions
    # Taken from the eggbox problem.

    def myprior(cube, ndim, nparams):
        for i in range(ndim):
            cube[i] = cube[i] * 10 * math.pi

    def myloglike(cube, ndim, nparams, lnew):
        chi = 1.
        for i in range(ndim):
            chi *= math.cos(cube[i] / 2.)    
        return math.pow(2. + chi, 5)

    # number of dimensions our problem has
    parameters = ["x", "y"]
    n_params = len(parameters)
    print(n_params)

    # run MultiNest
    pymultinest.run(myloglike, myprior, n_params, 
        resume = False, verbose = True)

    a = pymultinest.Analyzer(n_params = n_params, outputfiles_basename='chains/1-')
    p = pymultinest.PlotMarginalModes(a)

    p.plot_conditional(0,1)