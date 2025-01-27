# Energy Loss Library

This is a C++ header library that is used for calculating energy losses in Nuclear or Particle Physics that uses SRIM tables, LISE++ tables or "basic" energy loss tables. This library started by calculating energy loss using SRIM tables so they are the default type of table that is to be used although LISE++ tables and "basic" tables can also be read while initializing the class without specifying a table. More information on the setup of input files from SRIM tables can be found in the [Setting Up Input Files](#inputFiles) section. To use this with tables generated from LISE++, more information can be found in [Using LISE++ Tables](#liseTables). To use this class with "basic" tables, more information can be found in [Using "Basic" Tables](#basicTables).

This library requires C++11.

## How to install

```
git clone https://github.com/joshhooker/EnergyLossClass.git
```

Include EnergyLoss.h into your C++ program.

## Initializing an Energy Loss Class
The Energy Loss Class needs the binary file ``EnergyLoss.dat``, which is attached with this repo. By default it looks for ``EnergyLoss.dat`` in the directory where your program runs.

For an unedited SRIM file, we can initialize the Energy Loss Class by for example:
```c++
EnergyLoss* carbon = new EnergyLoss("CarbonCsI");
```
In case you want to specify the location of the ``EnergyLoss.dat`` (e.g., when you use PROOF or other sandbox-like tool), you can initialize the Energy Loss Class in this way:
```C++
EnergyLoss* carbon = new EnergyLoss("CarbonCsI","/path-to/EnergyLoss.dat");
```

For a given SRIM file of just the table and a conversion factor, we initialize the Energy Loss Class by:
```c++
EnergyLoss* carbonUseFactor = new EnergyLoss("CarbonCsIReduced.dat", 4.5099e+02);
```
This conversion factor is to MeV/mm.

## Using the Energy Loss Class
All energies are in MeV and all distances are in mm.

### List of functions:

#### CalcRemainder
```c++
double CalcRemainder(double initialEnergy, double distance)
```
CalcRemainder taken an initial energy and the distance the particle travels and calculates the energy after that distance. There is a parameter that controls when the final energy has converged. This can be controlled by the function "CalcRemainderError". By default, this is set so when the two iterations are within 1.e-8 of each other.

Example of 72 MeV 12C through 0.1mm of CsI:
```c++
double remainEnergy = carbon->CalcRemainder(72.,.1);
```
which yields
```
2.5539 MeV
```

#### AddBack
```c++
double AddBack(double finalEnergy, double distance)
```
AddBack takes a final energy and distance and calculates the initial energy the particle had before going through that distance. There are two parameters that control when the initial energy has converged. The first, "AddBackHighPoint", defines the highest energy it can calculate. This parameter can be controlled by the functon "AddBackHigh". The second parameter that controls the convergence is the parameter "AddBackErr" which is controlled by the function "AddBackError". By default, this is set when the two iterations are within 1.e-8 of each other.

Example of measuring 1 MeV 12C after 0.1mm of CsI:
```c++
double initialEnergy = carbon->AddBack(1.,0.1)
```
which yields
```
71.2000 MeV
```

#### CalcRange
```c++
double CalcRange(double initialEnergy, double finalEnergy)
```
CalcRange calculates how far particle can go through a material with a given starting energy and final energy.

Example to calculate the distance 100 MeV 12C can go in CsI before stopping:
```c++
double distance = carbon->CalcRange(100., 0.);
```
which yields
```
0.1686 mm
```

## Setting Up Input Files<a name="inputFiles"></a>

Currently the EnergyLoss Library can handle the original output file from SRIM that looks like:
```
==================================================================
             Calculation using SRIM-2006
             SRIM version ---> SRIM-2008.04
             Calc. date   ---> August 16, 2014
==================================================================

Disk File Name = SRIM Outputs\Carbon in CsI

Ion = Carbon [6] , Mass = 12 amu

Target Density =  4.5100E+00 g/cm3 = 2.0907E+22 atoms/cm3
======= Target  Composition ========
   Atom   Atom   Atomic    Mass
   Name   Numb   Percent   Percent
   ----   ----   -------   -------
    Cs     55    050.00    051.16
     I     53    050.00    048.84
====================================
Bragg Correction = 0.00%
Stopping Units =  MeV / (mg/cm2)
See bottom of Table for other Stopping units

  Ion        dE/dx      dE/dx     Projected  Longitudinal   Lateral
 Energy      Elec.      Nuclear     Range     Straggling   Straggling
-----------  ---------- ---------- ----------  ----------  ----------
   1.1 eV    1.676E-03  1.601E-03       2 A         4 A         3 A
   1.2 eV    1.676E-03  1.696E-03       2 A         4 A         3 A
   1.3 eV    1.744E-03  1.788E-03       3 A         4 A         3 A
   1.4 eV    1.810E-03  1.877E-03       3 A         4 A         3 A
   1.5 eV    1.874E-03  1.964E-03       3 A         4 A         3 A
   1.6 eV    1.935E-03  2.048E-03       3 A         5 A         3 A
   1.7 eV    1.995E-03  2.131E-03       3 A         5 A         3 A
   1.8 eV    2.053E-03  2.211E-03       3 A         5 A         4 A
     2 eV    2.164E-03  2.367E-03       3 A         5 A         4 A
  2.25 eV    2.295E-03  2.554E-03       3 A         5 A         4 A
2.49999 eV    2.419E-03  2.733E-03       3 A         6 A         4 A
2.74999 eV    2.537E-03  2.905E-03       3 A         6 A         4 A
2.99999 eV    2.650E-03  3.070E-03       3 A         6 A         4 A
3.24999 eV    2.758E-03  3.230E-03       4 A         6 A         5 A
3.49999 eV    2.862E-03  3.385E-03       4 A         7 A         5 A
.........
  8.00 GeV   5.162E-02  8.964E-06  234.02 mm     9.36 mm     4.32 mm
  9.00 GeV   4.991E-02  8.045E-06  277.68 mm    11.25 mm     5.02 mm
 10.00 GeV   4.859E-02  7.302E-06  322.69 mm    12.95 mm     5.71 mm
-----------------------------------------------------------
Multiply Stopping by        for Stopping Units
-------------------        ------------------
 4.5099E+01                 eV / Angstrom
 4.5099E+02                keV / micron
 4.5099E+02                MeV / mm
 1.0000E+00                keV / (ug/cm2)
 1.0000E+00                MeV / (mg/cm2)
 1.0000E+03                keV / (mg/cm2)
 2.1571E+02                 eV / (1E15 atoms/cm2)
 3.9022E+00                L.S.S. reduced units
==================================================================
(C) 1984,1989,1992,1998,2008 by J.P. Biersack and J.F. Ziegler

```

The library can also handle "trimmed files". We just need to record the conversion to MeV/mm which in this case is the line:
```
 4.5099E+02                MeV / mm
```

This will be our conversion from whatever the Stopping Units are to MeV/mm. A trimmed SRIM file looks like:
```
1.1 eV    1.676E-03  1.601E-03       2 A         4 A         3 A
1.2 eV    1.676E-03  1.696E-03       2 A         4 A         3 A
1.3 eV    1.744E-03  1.788E-03       3 A         4 A         3 A
1.4 eV    1.810E-03  1.877E-03       3 A         4 A         3 A
1.5 eV    1.874E-03  1.964E-03       3 A         4 A         3 A
1.6 eV    1.935E-03  2.048E-03       3 A         5 A         3 A
1.7 eV    1.995E-03  2.131E-03       3 A         5 A         3 A
1.8 eV    2.053E-03  2.211E-03       3 A         5 A         4 A
  2 eV    2.164E-03  2.367E-03       3 A         5 A         4 A
2.25 eV    2.295E-03  2.554E-03       3 A         5 A         4 A
2.49999 eV    2.419E-03  2.733E-03       3 A         6 A         4 A
2.74999 eV    2.537E-03  2.905E-03       3 A         6 A         4 A
2.99999 eV    2.650E-03  3.070E-03       3 A         6 A         4 A
3.24999 eV    2.758E-03  3.230E-03       4 A         6 A         5 A
3.49999 eV    2.862E-03  3.385E-03       4 A         7 A         5 A
.........
8.00 GeV   5.162E-02  8.964E-06  234.02 mm     9.36 mm     4.32 mm
9.00 GeV   4.991E-02  8.045E-06  277.68 mm    11.25 mm     5.02 mm
10.00 GeV   4.859E-02  7.302E-06  322.69 mm    12.95 mm     5.71 mm
```

## Debugging

If you are having issues, add the a true boolean to your EnergyLoss initialization:
```c++
EnergyLoss* carbon = new EnergyLoss("CarbonCsI", true);
```
```c++
EnergyLoss* carbonUseFactor = new EnergyLoss("CarbonCsIReduced.dat", 4.5099e+02, true);
```

## Using LISE++ Tables<a name="liseTables"></a>

The LISE++ table implementation in the EnergyLoss class allows for you to use both types of units for stopping power (MeV/um or MeV/mg/cm2) as an input and allows one to use different energy loss calculations that are provided in the table. The base implementation looks like the following:
```c++
ReadLISEdEdx(const char* inputFile, double mass, double density, int column)
```
* inputFile is the path to the LISE++ energy loss table (required)
* mass is the mass of the particle in u (required)
* density is measured in g/cm3. If density is present, then the table is assumed to be in units of MeV/mg/cm2. If density is not present, then the table is assumed to be in units of MeV/um.
* column is which energy loss calculation to use which is described below. If the column is not present, then selection #2 is assumed 

LISE++ tables come with 5 different energy loss calculations and we can toggle between the columns when initializing the tables.
Columns:
* 0 - dE/dx from F.Hubert et al, AD&ND Tables 46 (1990)
* 1 - dE/dx from  J.F.Ziegler et al, Pergamon Press, NY (low energy)
* 2 - dE/dx from ATIMA 1.2  LS-theory (recommended for high energy)
* 3 - dE/dx from ATIMA 1.2  without LS-correction
* 4 - electrical component of J. F. Ziegler et al. (Option #1)
* 5 - nuclear component of J. F. Ziegler et al. (Option #1)

To use LISE++ tables, we will first initialize the EnergyLoss class
```c++
EnergyLoss* carbon = new EnergyLoss();
```

To then use an energy loss table from LISE++ of 12C in CsI where the units are in MeV/mm and we will use energy loss column #2
```c++
carbon->ReadLISEdEdx("12C_in_CsI.lise", 12.0);
```

To use an energy loss table from LISE++ where the units are in MeV/mg/cm^2 and energy loss column #3
```c++
carbon->ReadLISEdEdx("12C_in_CsI.lise", 12.0, 4.51, 3);
```
where 4.51 is the density of CsI in g/cm^3.

## Using "Basic" Tables<a name="basicTables"></a>

Basic tables in this sense correspond to plain text tables with two columns: Energy (in MeV) and stopping power (in MeV/mm).

The initialize and read one of these basic energy loss tables, we will first initialize the EnergyLoss class
```c++
EnergyLoss* proton = new EnergyLoss();
```

To then use an energy loss table in this form, we will then call:
```c++
proton->ReadBasicdEdx("ProtonD2.dat");
```

There is no support currently that supports the basic tables to have units of MeV/mg/cm2 yet.

## License
See the [LICENSE](https://github.com/joshhooker/EnergyLossClass/blob/master/LICENSE.md) file for license rights and limitations (MIT).