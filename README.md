# TSINFER

Infer population size and selection coefficient from time-series allele-frequency data


## Requirements
  * OCaml langugage compiler: http://ocaml.org/
  * GNU Scientific Library (GSL): http://www.gnu.org/software/gsl/
  * GSL Interface for OCaml language: https://github.com/mmottl/gsl-ocaml 
  
On Ubuntu these can be installed with:

```
sudo apt-get update


$ sudo add-apt-repository ppa:avsm/ppa
$ sudo apt-get update
$ sudo apt-get install curl build-essential m4 ocaml opam -y

Reading package lists... Done
Building dependency tree
Reading state information... Done
build-essential is already the newest version (12.4ubuntu1).
build-essential set to manually installed.
m4 is already the newest version (1.4.18-1).
ocaml is already the newest version (4.04.0-2ubuntu4).
opam is already the newest version (1.2.2-6).
curl is already the newest version (7.55.1-1ubuntu2.4).

$ sudo apt-get install libgsl-dev gsl-bin libocamlgsl-ocaml-dev -y

Reading package lists... Done
Building dependency tree
Reading state information... Done
libgsl-dev is already the newest version (2.4+dfsg-6).
gsl-bin is already the newest version (2.4+dfsg-6).
libocamlgsl-ocaml-dev is already the newest version (1.19.1-2build1).

$ opam install -y batteries jbuilder gsl lacaml opam-installer

[NOTE] Package batteries is already installed (current version is 2.8.0).
[NOTE] Package jbuilder is already installed (current version is
                                              1.0+beta19.1).
[NOTE] Package gsl is already installed (current version is 1.21.0).
[NOTE] Package lacaml is already installed (current version is 11.0.0).
[NOTE] Package opam-installer is already installed (current version is
                                                    2.0.0~rc).

$ opam info gsl

package: gsl
version: 1.21.0
repository: default
upstream-url: https://github.com/mmottl/gsl-ocaml/releases/download/1.21.0/gsl-1.21.0.tbz
upstream-kind: http
upstream-checksum: a118387c7aa9f9e4dfa8bd0410ad2458
homepage: https://mmottl.github.io/gsl-ocaml
bug-reports: https://github.com/mmottl/gsl-ocaml/issues
dev-repo: https://github.com/mmottl/gsl-ocaml.git
author: Olivier Andrieu <oandrieu@gmail.com>, Markus Mottl <markus.mottl@gmail.com>
  license: GPL-3+
  doc: https://mmottl.github.io/gsl-ocaml/api
depends: conf-gsl & conf-pkg-config & base & stdio & configurator & jbuilder >= 1.0+beta10
installed-version: 1.21.0 [“tezos” system]
available-versions: 1.18.5, 1.19.1, 1.19.3, 1.20.0, 1.20.2, 1.21.0
description: GSL - Bindings to the GNU Scientific Library

gsl-ocaml interfaces the GSL (GNU Scientific Library), providing many of the
most frequently used functions for scientific computation including algorithms
for optimization, differential equations, statistics, random number generation,
linear algebra, etc.

$ git clone https://github.com/mnewberry/tsinfer   
$ cd tsinfer
$ eval $(opam config env)
$ make all

make hash2.cmo
make[1]: Entering directory '/home/osboxes/tsinfer'
make[1]: 'hash2.cmo' is up to date.
make[1]: Leaving directory '/home/osboxes/tsinfer'
make aux.cmo
make[1]: Entering directory '/home/osboxes/tsinfer'
make[1]: 'aux.cmo' is up to date.
make[1]: Leaving directory '/home/osboxes/tsinfer'
make io.cmo
make[1]: Entering directory '/home/osboxes/tsinfer'
make[1]: 'io.cmo' is up to date.
make[1]: Leaving directory '/home/osboxes/tsinfer'
make tsinfer
make[1]: Entering directory '/home/osboxes/tsinfer'
ocamlopt -o tsinfer -I /usr/local/lib/ocaml/ocamlgsl/ bigarray.cmxa gsl.cmxa str.cmxa unix.cmxa nums.cmxa hash2.cmx aux.cmx io.cmx tsinfer.ml
File "tsinfer.ml", line 122, characters 4-29:
  Error: Unbound module Gsl
Makefile:35: recipe for target 'tsinfer' failed
make[1]: *** [tsinfer] Error 2
make[1]: Leaving directory '/home/osboxes/tsinfer'
Makefile:38: recipe for target 'all' failed
make: *** [all] Error 2


```

## Installation
Untar the archive into the desired directory:
```
  tar -zxf tsinfer.tar.gz
```

Compile required modules and the executable:
```
  make all
```


## Execution
Execution syntax is simple
```
  ./tsinfer infile [OPTIONS]
```

`infile` contains the time series data. The file should contain three lines,
each tab separated, with equal number of entries. The first line contains
sample times, the second line contains the number of samples of the focal
allele (must be integers) and the third line containes total number of samples
at each time point (must be integers). For example

```
0 10 20
2000 4000 6000
10000 10000 10000
```

### Options

`-f` - treat sampled frequencies as actual frequencies

`-func_eval:XX` - number of function evalutations in the Monte Carlo integration (posiitve integer). Default : 3e+04

`-h` - help

`-maxiter:XX` - maximum number of iterations (positive integer). Default : 200

`-maxterms:XX` - maximum number of terms in the series (positive integer). Default 2000

`-neut` - evaluate only the likelihood of the data under neutrality

`-prec:XX` - precision (for the size of the simples). Default : 1.0e-06

`-start:s,alpha` - starting guess for s (float) and alpha (positive float). Default : (0, 1.000e+03)

`-stds:XX` - number of standard deviations around the mean of the beta distribution for the approximation of the intergral (positive integer). Default : 3.0

`-step:s,alpha` - step size for s, and alpha (all positive floats). Default : (0.005, 5.000e+01)

`-plot:filename` - instead of finding the maximum likelihood value, plot the likelihood function profile at points<br> specified in the file 'filename'. Each line of the file must consist of 2 tab-separated numbers corresponding to the coordinates (s, alpha). See examples. The 'start', and 'step' options are ignored.

`-v` - verbose mode


### Output
The output of the execution is a list of 10 values, e.g.,
```
STATUS = SUCCESS
MLS = 0.08927
MLALPHA = 1.635e+04
MNLOGL = 20.21
AIC2 = 44.419
S0ALPHA = 7.999e+01
S0NLOGL = 24.837
AIC1 = 51.675
LLR = 9.2554
CHI2P = 2.4e-03
```

`STATUS` indicates whether the program successfully found the ML values of
selection coefficient s and the population size alpha. If procedure is not
successful the failure mode is indicated.

`MLS` is the ML value of the selection coefficient s in the two-parameter
model.

`MLALPHA` is the ML value of the population size alpha in the two-parameter
model.

`MNLOGL` is the obtained minimum value of the negative log-likelihood in the
two-parameter model.

`AIC2` is the value of the Akaike information criterion for the two-parameter model.

`S0ALPHA` is the ML value of population size alpha in the one-parameter model
(i.e., when s = 0).

`S0NLOGL` is the obtained minimum value of the negative log-likelihood in the
one-parameter model.

`AIC1` is the value of the Akaike information criterion for the one-parameter
model.

`LLR` is the twice log likelihood ratio statistic, i.e., 2 * (`S0NLOGL` -
`MNLOGL`).

`CHI2P` is the right-tailed chi2 P-value for the likelihood ratio statistic.

## Examples
**Example 1.** Obtain the maximum likelihood parameters for time series in file test.in, taking sampling into account
```
  ./tsinfer test.in
```
Note that, by default, the procedure of calculating the likelihood of data takes binomial sampling into account. However, this procedure was not extensively tested and may contain errors. It is recommende to use instead the procedure that treats sampled frequecies as exact. See next example below.


**Example 2.** Obtain the maximum likelihood parameters for time series in file test.in, treating sampled frequencies as exact. Note that the input file still needs to have three lines with second and third lines containing integer values.
```
  ./tsinfer test.in -f
```

**Example 3.** Obtain the values of the negative log-likelihood function for time series in file test.in at parameter values specified in file grid, treating sampled frequencies as exact
```
  ./tsinfer test.in -f -plot:grid
```
