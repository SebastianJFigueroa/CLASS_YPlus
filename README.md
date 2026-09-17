CLASS YPlus
==============================================

Authors: Alexander Sobotka, Sebastian Figueroa

CLASS YPlus is a modified version of the CLASS Boltzmann solver created by Julien Lesgourgues and Thomas Tram.
CLASS YPlus calculates the effects of a hidden-sector Y particle that decays into a mixture of photons and
dark radiation (DR) with a full perturbative treatment. The Y particle is assumed to be nonrelativistic, and the
DR is relativistic and non-interacting. Three input parameters are needed to describe the decay:

- `Gamma_yp` - the decay rate of the Y particle, which should be provided in units of 1/Mpc.
- `maxyr_yp` - the maximum value of `rho_yp/rho_r`, where `rho_yp` is the energy density of the
               Y particle and `rho_r` is the total radiation energy density.
- `f_yp`     - the fraction of the decaying particle's energy that is transferred to photons,
               where `f_yp = 1` if the Y particle decays completely into photons and `f_yp = 0`
               if the Y particle decays completely into DR.

Adding the Y-photon interactions to the TCA equations led to numerical instabilities on superhorizon scales,
so the full perturbation system must be integrated directly. With the stiff integrators used by CLASS,
the resulting increase in computation time is minimal. The following numerical thresholds ensure
that all perturbation modes are initialized well before Y decay becomes significant and that the TCA
is disabled immediately after the integration of each perturbation mode begins for all `k < 100 h/Mpc`:

    start_small_k_at_tau_c_over_tau_h = 1.e-9
    start_large_k_at_tau_h_over_tau_k = 0.05
    start_large_k_at_gamma_y_over_h = 1.e-4
    tight_coupling_trigger_tau_c_over_tau_h = 1.1e-9
    tight_coupling_trigger_tau_c_over_tau_k = 1.e-10
    tight_coupling_trigger_gamma_y_over_h = 1.1e-4

CLASS implements massive neutrinos by assuming a perfect Fermi-Dirac distribution by default and using
a nonzero `N_ur` to ensure `N_eff = 3.044` at BBN. CLASS YPlus introduces a new feature in which `T_ncdm`
can be varied from its default value of `0.71611`, while `N_ur` is automatically rescaled to preserve
the correct value of `N_eff` at BBN for a given neutrino abundance today. To enable this functionality, set:

    rescale_N_ur_with_T_ncdm = yes

For specifics regarding the Y-decay model, please see [arXiv:2207.14308](https://arxiv.org/abs/2207.14308) and
[arXiv:2609.15970](https://arxiv.org/abs/2609.15970). If you use this code, please cite these works in addition to
the CLASS papers on approximation schemes ([arXiv:1104.2933](https://arxiv.org/abs/1104.2933)) and the implementation
of non-cold relics ([arXiv:1104.2935](https://arxiv.org/abs/1104.2935)). The BibTeX entries for these works are:

    @article{Sobotka:2022vrr,
        author = "Sobotka, Alexander C. and Erickcek, Adrienne L. and Smith, Tristan L.",
        title = "{Was entropy conserved between BBN and recombination?}",
        eprint = "2207.14308",
        archivePrefix = "arXiv",
        primaryClass = "astro-ph.CO",
        doi = "10.1103/PhysRevD.107.023525",
        journal = "Phys. Rev. D",
        volume = "107",
        number = "2",
        pages = "023525",
        year = "2023"
    }

    @article{Figueroa:2026jah,
        author = "Figueroa, Sebastian J. and Sobotka, Alexander C. and Erickcek, Adrienne L.",
        title = "{Less isn't more: Cosmological bounds on the neutrino masses are robust to changes in the neutrino abundance}",
        eprint = "2609.15970",
        archivePrefix = "arXiv",
        primaryClass = "astro-ph.CO",
        month = "9",
        year = "2026"
    }

    @article{Blas:2011rf,
        author = "Blas, Diego and Lesgourgues, Julien and Tram, Thomas",
        title = "{The Cosmic Linear Anisotropy Solving System (CLASS) II: Approximation schemes}",
        eprint = "1104.2933",
        archivePrefix = "arXiv",
        primaryClass = "astro-ph.CO",
        reportNumber = "CERN-PH-TH-2011-082, LAPTH-010-11",
        doi = "10.1088/1475-7516/2011/07/034",
        journal = "JCAP",
        volume = "07",
        pages = "034",
        year = "2011"
    }
    
    @article{Lesgourgues:2011rh,
        author = "Lesgourgues, Julien and Tram, Thomas",
        title = "{The Cosmic Linear Anisotropy Solving System (CLASS) IV: efficient implementation of non-cold relics}",
        eprint = "1104.2935",
        archivePrefix = "arXiv",
        primaryClass = "astro-ph.CO",
        reportNumber = "CERN-PH-TH-2011-084, LAPTH-012-11",
        doi = "10.1088/1475-7516/2011/09/032",
        journal = "JCAP",
        volume = "09",
        pages = "032",
        year = "2011"
    }

See below for details and instructions for installing CLASS.


CLASS: Cosmic Linear Anisotropy Solving System
==============================================

Authors: Julien Lesgourgues, Thomas Tram, Nils Schoeneberg

with several major inputs from other people, especially Benjamin
Audren, Simon Prunet, Jesus Torrado, Miguel Zumalacarregui, Francesco
Montanari, Deanna Hooper, Samuel Brieden, Daniel Meinert, Matteo Lucca, etc.

For download and information, see http://class-code.net


Compiling CLASS and getting started
-----------------------------------

(the information below can also be found on the webpage, just below
the download button)

Download the code from the webpage and unpack the archive (tar -zxvf
class_vx.y.z.tar.gz), or clone it from
https://github.com/lesgourg/class_public. Go to the class directory
(cd class/ or class_public/ or class_vx.y.z/) and compile (make clean;
make class). You can usually speed up compilation with the option -j:
make -j class. If the first compilation attempt fails, you may need to
open the Makefile and adapt the name of the compiler (default: gcc),
of the optimization flag (default: -O4 -ffast-math) and of the OpenMP
flag (default: -fopenmp; this flag is facultative, you are free to
compile without OpenMP if you don't want parallel execution; note that
you need the version 4.2 or higher of gcc to be able to compile with
-fopenmp). Many more details on the CLASS compilation are given on the
wiki page

https://github.com/lesgourg/class_public/wiki/Installation

(in particular, for compiling on Mac >= 10.9 despite of the clang
incompatibility with OpenMP).

To check that the code runs, type:

    ./class explanatory.ini

The explanatory.ini file is THE reference input file, containing and
explaining the use of all possible input parameters. We recommend to
read it, to keep it unchanged (for future reference), and to create
for your own purposes some shorter input files, containing only the
input lines which are useful for you. Input files must have a *.ini
extension. We provide an example of an input file containing a
selection of the most used parameters, default.ini, that you may use as a
starting point.

If you want to play with the precision/speed of the code, you can use
one of the provided precision files (e.g. cl_permille.pre) or modify
one of them, and run with two input files, for instance:

    ./class test.ini cl_permille.pre

The files *.pre are suppposed to specify the precision parameters for
which you don't want to keep default values. If you find it more
convenient, you can pass these precision parameter values in your *.ini
file instead of an additional *.pre file.

The automatically-generated documentation is located in

    doc/manual/html/index.html
    doc/manual/CLASS_manual.pdf

On top of that, if you wish to modify the code, you will find lots of
comments directly in the files.

Python
------

To use CLASS from python, or ipython notebooks, or from the Monte
Python parameter extraction code, you need to compile not only the
code, but also its python wrapper. This can be done by typing just
'make' instead of 'make class' (or for speeding up: 'make -j'). More
details on the wrapper and its compilation are found on the wiki page

https://github.com/lesgourg/class_public/wiki

Plotting utility
----------------

Since version 2.3, the package includes an improved plotting script
called CPU.py (Class Plotting Utility), written by Benjamin Audren and
Jesus Torrado. It can plot the Cl's, the P(k) or any other CLASS
output, for one or several models, as well as their ratio or percentage
difference. The syntax and list of available options is obtained by
typing 'pyhton CPU.py -h'. There is a similar script for MATLAB,
written by Thomas Tram. To use it, once in MATLAB, type 'help
plot_CLASS_output.m'

Developing the code
--------------------

If you want to develop the code, we suggest that you download it from
the github webpage

https://github.com/lesgourg/class_public

rather than from class-code.net. Then you will enjoy all the feature
of git repositories. You can even develop your own branch and get it
merged to the public distribution. For related instructions, check

https://github.com/lesgourg/class_public/wiki/Public-Contributing

Using the code
--------------

You can use CLASS freely, provided that in your publications, you cite
at least the paper `CLASS II: Approximation schemes <http://arxiv.org/abs/1104.2933>`. Feel free to cite more CLASS papers!

Support
-------

To get support, please open a new issue on the

https://github.com/lesgourg/class_public

webpage!
