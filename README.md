How to Create a LAMMPS Data file from a PCFF file
=====

This documents aims to explain how to use the [pcff.frc](pcff.frc) force
field file to pull out coefficients required by LAMMPS for this class2 force
field.

**NOTE:** This is currently a work in progress. Better explanations are
needed for each coefficient section, but the current state of the document
should be sufficient to understand how to make a LAMMPS data file for the
PCFF class2 force field.

# Contents

 - [Example Structure](#example-structure)
   - [Sketch](#sketch)
   - [Atom numbers](#atom-numbers)
 - [Forcefield Label](#forcefield-label)
 - [Forcefield Equivalences](#forcefield-equivalences)
   - [Equivalences](#equivalences)
   - [Auto Equivalences](#auto-equivalences)
 - [LAMMPS Data file coefficients](#lammps-data-file-coefficients)
   - [Partial Charges](#partial-charges)
   - [Mass](#mass)
   - [Pair](#pair)
   - [Bond](#bond)
   - [Angle](#angle)
   - [Dihedral](#dihedral)
   - [Improper](#improper)
   - [BondBond](#bondbond)
   - [BondAngle](#bondangle)
   - [AngleAngle](#angleangle)
   - [AngleAngleTorsion](#angleangletorsion)
   - [EndBondTorsion](#endbondtorsion)
   - [MiddleBondTorsion](#middlebondtorsion)
   - [BondBond13](#bondbond13)
   - [AngleTorsion](#angletorsion)

# Example Structure

The examples below use [Poly(isobutylene);
PIB](https://en.wikipedia.org/wiki/Polyisobutene). The structure and atom
numbers are shown in the two sketches below.

## Sketch

Sketch of Chemical Structure showing atom names.

```
            H
            |
      H   H-C-H
      |     |
 -[-  C  -  C  -]-
      |     |
      H   H-C-H
            |
            H
```

## Atom numbers

Sketch of chemical structure where atom names have been replaced with the atom ids that are
used for the example LAMMPS data file sections.

**NOTE:** For this example we will assume that atoms 1 and 2 are each
connected to another carbon at the repeat unit ends.

```
            7
            |
      5   6-3-8
      |     |
 -[-  1  -  2  -]-
      |     |
     12  11-4-9
            |
           10

```

# Forcefield Label

PCFF section title: _atom_types_ (line #[39](pcff.frc#L39))

Use the comments in this section of the `pcff.frc` file to determine the
appropriate force field label for each atom.

| Atom # | Atom  | FF label |
| :---   | :---: | :---     |
| 1      | C     | c2       |
| 2      | C     | c        |
| 3      | C     | c3       |
| 4      | C     | c3       |
| 5      | H     | hc       |
| 6      | H     | hc       |
| 7      | H     | hc       |
| 8      | H     | hc       |
| 9      | H     | hc       |
| 10     | H     | hc       |
| 11     | H     | hc       |
| 12     | H     | hc       |

# Forcefield Equivalences

When the exact force field can not be found in a section of
[pcff.frc](pcff.frc), use the [equivalence](#equivalences) force field
label.

Some sections of [pcff.frc](pcff.frc) use [Auto
Equivalences](#auto-equivalences) instead of the plain equivalence force
field labels; these sections are clearly noted in the relevant coefficients
section below, and in the [Auto Equivalences](#auto-equivalences) section.

## Equivalences

PCFF section title: _equivalence_ (line #[181](pcff.frc#L181))

When the exact force field can not be found in a section of `pcff.frc`, use
the equivalence force field label.

| FF Label | Equivalence        |
| :---     | :---               |
| c        | [c](pcff.frc#L209) |
| c2       | [c](pcff.frc#L213) |
| c3       | [c](pcff.frc#L214) |
| hc       | [h](pcff.frc#L244) |

## Auto Equivalences

PCFF section title: _auto_equivalence_ (line #[323](pcff.frc#L323))

These equivalences are used for [Partial Charges](#partial-charges) (Bond
Inct) and [Improper](#improper) (OOP End Atom and OOP Center Atom)
coefficients.

| FF Label | Bond Inct          | OOP End atom        | OOP Center Atom     |
| :---     | :---               | :---                | :---                |
| c        | [c](pcff.frc#L335) | [c_](pcff.frc#L335) | [c_](pcff.frc#L335) |
| c2       | [c](pcff.frc#L339) | [c_](pcff.frc#L339) | [c_](pcff.frc#L339) |
| c3       | [c](pcff.frc#L340) | [c_](pcff.frc#L340) | [c_](pcff.frc#L340) |
| hc       | [h](pcff.frc#L369) | [h_](pcff.frc#L369) | [h_](pcff.frc#L369) |

# LAMMPS Data File Coefficients

Below is a detailed description of how to pull out PCFF forcefield
coefficients from [pcff.frc](pcff.frc) in a format suitable to LAMMPS.

Each coefficient section below includes a table showing the values obtained
from [pcff.frc](pcff.frc) and a section showing what the relevant section of
a LAMMPS data file should look like.

Most values are linked to the exact line number of [pcff.frc](pcff.frc) from
which the value was obtained. Values which are not linked to in
[pcff.frc](pcff.frc) were most likely found already in an earlier
coefficients section, notably R0 in the [bond](#bond) section and Theta0 in
the [Angle](#angle) section.

## Partial Charges

PCFF section title: _bond_increments_ (line #[440](pcff.frc#L440))

Use the [Auto Equivalences](#auto-equivaleces) `Bond Inct` for this section.

The partial change is found by adding values for all bonds to each of the
atoms. Order matters for this coefficient; ij and ji are different
by a factor of -1.

| Atom # | i    | Partial Charge | Equation                                                 |
| :---   | :--- | :---:          | :---                                                     |
| 1      | c2   | -0.1060        | 2\*[0.0000](pcff.frc#L493) + 2\*[-0.0530](pcff.frc#L505) |
| 2      | c    | 0.0000         | 4\*[0.0000](pcff.frc#L493)                               |
| 3      | c3   | -0.1590        | [0.0000](pcff.frc#L493) + 3\*[-0.0530](pcff.frc#L505)    |
| 4      | c3   | -0.1590        | [0.0000](pcff.frc#L493) + 3\*[-0.0530](pcff.frc#L505)    |
| 5      | hc   | 0.0530         | [0.5030](pcff.frc#L505)                                  |
| 6      | hc   | 0.0530         | [0.0530](pcff.frc#L505)                                  |
| 7      | hc   | 0.0530         | [0.0530](pcff.frc#L505)                                  |
| 8      | hc   | 0.0530         | [0.0530](pcff.frc#L505)                                  |
| 9      | hc   | 0.0530         | [0.0530](pcff.frc#L505)                                  |
| 10     | hc   | 0.0530         | [0.0530](pcff.frc#L505)                                  |
| 11     | hc   | 0.0530         | [0.0530](pcff.frc#L505)                                  |
| 12     | hc   | 0.0530         | [0.0530](pcff.frc#L505)                                  |

### Example LAMMPS Datafile Section

```
Atoms # full

   1  1 2  -0.1060    x y z nx ny nz
   2  1 1   0.0000    x y z nx ny nz
   3  1 3  -0.1590    x y z nx ny nz
   4  1 3  -0.1590    x y z nx ny nz
   5  1 4   0.0530    x y z nx ny nz
   6  1 4   0.0530    x y z nx ny nz
   7  1 4   0.0530    x y z nx ny nz
   8  1 4   0.0530    x y z nx ny nz
   9  1 4   0.0530    x y z nx ny nz
  10  1 4   0.0530    x y z nx ny nz
  11  1 4   0.0530    x y z nx ny nz
  12  1 4   0.0530    x y z nx ny nz
```

The `x`, `y`, and `z` values would be replaced with the atom coordinates and
the `nx`, `ny`, and `nz` values are the image flags of the atom.

## Mass

PCFF section title: _atom_types_ (line #[39](pcff.frc#L39))

| FF Label | Mass      |
| :---     | ---:      |
| c        | 12.011150 |
| c2       | 12.011150 |
| c3       | 12.011150 |
| hc       | 1.007970  |

### Example LAMMPS Datafile Section

```
Masses

   1 12.011150 # c
   2 12.011150 # c2
   3 12.011150 # c3
   4  1.007970 # hc
```

## Pair

PCFF section title: _non-bond(9-6)_ (line #[3254](pcff.frc#L3254))

**NOTE:** The values in the pcff.frc file for this section need to be reversed for
the LAMMPS data file.

| FF Label | Pair                               |
| :---     | :---                               |
| c        | [0.05400   4.0100](pcff.frc#L3288) |
| c2       | [0.05400   4.0100](pcff.frc#L3288) |
| c3       | [0.05400   4.0100](pcff.frc#L3288) |
| hc       | [0.02000   2.9950](pcff.frc#L3305) |

### Example LAMMPS Datafile Section


```
Pair Coeffs #lj/class2/coul/long

   1  0.05400   4.0100 # c
   2  0.05400   4.0100 # c2
   3  0.05400   4.0100 # c3
   4  0.02000   2.9950 # hc
```

## Bond

PCFF section title: _quartic_bond_ (line #[1646](pcff.frc#L1646))

The 1st value is the equilibrium bond lengths (R0 in pcff.frc), which is
used in [BondBond](#bondbond), [BondAngle](#bondangle),
[EndBondTorsion](#endbondtorsion), [MiddleBondTorsion](#middlebondtorsion),
and [BondBond13](#bondbond13) sections below.

Values ordering: Same order as listed in pcff.frc

Acceptable ordering in `pcff.frc` for forcefield of i-j:

| Value | Order          |
| :---  | :---           |
| 1st   | any of: ij, ji |
| 2nd   | any of: ij, ji |
| 3rd   | any of: ij, ji |
| 4th   | any of: ij, ji |

|  i |  j | 1st | 2nd | 3rd | 4th |
|:---|:---|----:|----:|----:|----:|
| c2 | hc | [1.1010](pcff.frc#L1667) | [345.0000](pcff.frc#L1667) | [-691.8900](pcff.frc#L1667) | [844.6000](pcff.frc#L1667) |
| c2 | c  | [1.5300](pcff.frc#L1657) | [300.6700](pcff.frc#L1657) | [-501.7700](pcff.frc#L1657)| [679.8100](pcff.frc#L1657)|
| c  | c3 | [1.5300](pcff.frc#L1657)| [299.6700](pcff.frc#L1657)| [-501.7700](pcff.frc#L1657)| [679.8100](pcff.frc#L1657)|
| c3 | hc | [1.1010](pcff.frc#L1667)| [345.0000](pcff.frc#L1667)| [-691.8900](pcff.frc#L1667)| [844.6000](pcff.frc#L1667)|
| c  | hc | [1.1010](pcff.frc#L1667)| [345.0000](pcff.frc#L1667)| [-691.8900](pcff.frc#L1667)| [844.6000](pcff.frc#L1667)|

### Example LAMMPS Datafile Section

```
Bond Coeffs # class2

   1    1.1010    345.0000   -691.8900    844.6000 # c2-hc
   2    1.5300    299.6700   -501.7700    679.8100 # c2-c
   3    1.5300    299.6700   -501.7700    679.8100 # c-c3
   4    1.1010    345.0000   -691.8900    844.6000 # c3-hc
   5    1.1010    345.0000   -691.8900    844.6000 # c-hc
```

## Angle

PCFF section title: _quartic_angle_ (line #[2119](pcff.frc#L2119))

The 1st value (Theta0 in pcff.frc) is also used in the
[AngleAngle](#angleangle), [AngleAngleTorsion](#angleangletorsion), and
[AngleTorsion](#angletorsion) sections below.

Values ordering: Same order as listed in pcff.frc

Acceptable ordering in `pcff.frc` for forcefield of i-j-k:

| Value | Order            |
| :---  | :---             |
| 1st   | any of, ijk, kji |
| 2nd   | any of, ijk, kji |
| 3rd   | any of, ijk, kji |
| 4th   | any of, ijk, kji |

| i    | j    | k    | 1st  | 2nd  | 3rd  | 4th  |
| :--- | :--- | :--- | ---: | ---: | ---: | ---: |
|hc  | c2 | hc | [107.6600](pcff.frc#L2183) | [39.6410](pcff.frc#L2183) | [-12.9210](pcff.frc#L2183) | [-2.4318](pcff.frc#L2183) |
|hc  | c2 | c  | [110.7700](pcff.frc#L2147) | [41.4530](pcff.frc#L2147) | [-10.6040](pcff.frc#L2147) |  [5.1290](pcff.frc#L2147) |
|c3  | c  | c3 | [112.6700](pcff.frc#L2137) | [39.5160](pcff.frc#L2137) |  [-7.4430](pcff.frc#L2137) | [-9.5583](pcff.frc#L2137) |
|c3  | c  | c2 | [112.6700](pcff.frc#L2137) | [39.5160](pcff.frc#L2137) |  [-7.4430](pcff.frc#L2137) | [-9.5583](pcff.frc#L2137) |
|hc  | c3 | hc | [107.6600](pcff.frc#L2183) | [39.6410](pcff.frc#L2183) | [-12.9210](pcff.frc#L2183) | [-2.4318](pcff.frc#L2183) |
|hc  | c3 | c  | [110.7700](pcff.frc#L2147) | [41.4530](pcff.frc#L2147) | [-10.6040](pcff.frc#L2147) |  [5.1290](pcff.frc#L2147) |
|hc  | c  | c4 | [110.7700](pcff.frc#L2147) | [41.4530](pcff.frc#L2147) | [-10.6040](pcff.frc#L2147) |  [5.1290](pcff.frc#L2147) |
|hc  | c  | c2 | [110.7700](pcff.frc#L2147) | [41.4530](pcff.frc#L2147) | [-10.6040](pcff.frc#L2147) |  [5.1290](pcff.frc#L2147) |
|c2  | c  | c2 | [112.6700](pcff.frc#L2137) | [39.5160](pcff.frc#L2137) |  [-7.4430](pcff.frc#L2137) | [-9.5583](pcff.frc#L2137) |
|c   | c2 | c  | [112.6700](pcff.frc#L2137) | [39.5160](pcff.frc#L2137) | [-7.4430](pcff.frc#L2137) | [-9.5583](pcff.frc#L2137) |

### Example LAMMPS Datafile Section



```
Angle Coeffs # class2

   1   107.6600    39.6410   -12.9210    -2.4318 # hc-c2-hc
   2   110.7700    41.4530   -10.6040     5.1290 # hc-c2-c
   3   112.6700    39.5160    -7.4430    -9.5583 # c3-c-c3
   4   112.6700    39.5160    -7.4430    -9.5583 # c3-c-c2
   5   107.6600    39.6410   -12.9210    -2.4318 # hc-c3-hc
   6   110.7700    41.4530   -10.6040     5.1290 # hc-c3-c
   7   110.7700    41.4530   -10.6040     5.1290 # hc-c-c3
   8   110.7700    41.4530   -10.6040     5.1290 # hc-c-c2
   9   112.6700    39.5160    -7.4430    -9.5583 # c2-c-c2
  10   112.6700    39.5160    -7.4430    -9.5583 # c-c2-c
```

## Dihedral

PCFF section title: _torsion_3_ (line #[2655](pcff.frc#L2655))

Starting on line 2655 of pcff.frc that comes with LAMMPS and in same order.
Forward and reverse should be same.

Acceptable ordering in `pcff.frc` for forcefield of i-j-k-l:

| Value   | Order              |
| :---    | :---               |
| 1st-6th | any of: ijkl, lkji |

Values ordering: Same order as listed in pcff.frc

| i    | j    | k    | l    | 1st through  6th values                                        |
| :--- | :--- | :--- | :--- | ---:                                                           |
| hc   | c2   | c    | c3   | [ 0.0000   0.0   0.0316   0.0   -0.1681   0.0](pcff.frc#L2689) |
| c3   | c    | c3   | hc   | [ 0.0000   0.0   0.0316   0.0   -0.1681   0.0](pcff.frc#L2689) |
| c2   | c    | c3   | hc   | [ 0.0000   0.0   0.0316   0.0   -0.1681   0.0](pcff.frc#L2689) |
| hc   | c2   | c    | hc   | [-0.1432   0.0   0.0617   0.0   -0.1083   0.0](pcff.frc#L2736) |
| hc   | c    | c3   | hc   | [-0.1432   0.0   0.0617   0.0   -0.1083   0.0](pcff.frc#L2736) |
| hc   | c2   | c    | c2   | [ 0.0000   0.0   0.0316   0.0   -0.1681   0.0](pcff.frc#L2689) |
| c3   | c    | c2   | c    | [ 0.0000   0.0   0.0514   0.0   -0.1430   0.0](pcff.frc#L2679) |
| c2   | c    | c2   | c    | [ 0.0000   0.0   0.0514   0.0   -0.1430   0.0](pcff.frc#L2679) |
| c    | c2   | c    | hc   | [ 0.0000   0.0   0.0316   0.0   -0.1681   0.0](pcff.frc#L2689) |

### Example LAMMPS Datafile Section

```
Dihedral Coeffs # class2

   1    0.0000      0.0    0.0316      0.0   -0.1681      0.0 # hc-c2-c-c3
   2    0.0000      0.0    0.0316      0.0   -0.1681      0.0 # c3-c-c3-hc
   3    0.0000      0.0    0.0316      0.0   -0.1681      0.0 # c2-c-c3-hc
   4   -0.1432      0.0    0.0617      0.0   -0.1083      0.0 # hc-c2-c-hc
   5   -0.1432      0.0    0.0617      0.0   -0.1083      0.0 # hc-c-c3-hc
   6    0.0000      0.0    0.0316      0.0   -0.1681      0.0 # hc-c2-c-c2
   7    0.0000      0.0    0.0514      0.0   -0.1430      0.0 # c3-c-c2-c
   8    0.0000      0.0    0.0514      0.0   -0.1430      0.0 # c2-c-c2-c
   9    0.0000      0.0    0.0316      0.0   -0.1681      0.0 # c-c2-c-hc
```

## Improper

PCFF section title: _wilson_out_of_plane_ (line #[3155](pcff.frc#L3155))

If can not find required values, use the _cff91_auto_ section (line
[3234](pcff.frc#L3234)) along with [Auto Equivalences](#auto-equivalences)
for `OOP End Atom` and `OOP Center Atom`.  If still not found, use `0.0
0.0`.

The 1st (i), 3rd (k), and 4th (l) forcefields can be in any order while the
2nd (j) ff must remain the 2nd.

Acceptable ordering in `pcff.frc` for forcefield of i-j-k-l:

| Value   | Order                                      |
| :---    | :---                                       |
| 1st-2nd | any of: ijkl, ijlk, kjil, kjli, ljik, ljki |

Values ordering: Same order as listed in pcff.frc


| i    | j    | k    | l    | Values  |
| :--- | :--- | :--- | :--- | ---:    |
| c    | c2   | hc   | hc   | 0.0 0.0 |
| c2   | c    | c3   | c3   | 0.0 0.0 |
| c    | c3   | hc   | hc   | 0.0 0.0 |
| hc   | c3   | hc   | hc   | 0.0 0.0 |
| hc   | c2   | hc   | hc   | 0.0 0.0 |
| c2   | c    | c3   | hc   | 0.0 0.0 |
| c3   | c    | c3   | hc   | 0.0 0.0 |
| c2   | c    | c3   | c2   | 0.0 0.0 |
| c3   | c    | c3   | c2   | 0.0 0.0 |
| c    | c2   | c    | hc   | 0.0 0.0 |

### Example LAMMPS Datafile Section


```
Improper Coeffs # class2

   1   0.0 0.0 # c-c2-hc-hc
   2   0.0 0.0 # c2-c-c3-c3
   3   0.0 0.0 # c-c3-hc-hc
   4   0.0 0.0 # hc-c3-hc-hc
   5   0.0 0.0 # hc-c2-hc-hc
   6   0.0 0.0 # c2-c-c3-hc
   7   0.0 0.0 # c3-c-c3-hc
   8   0.0 0.0 # c2-c-c3-c2
   9   0.0 0.0 # c3-c-c3-c2
  10   0.0 0.0 # c-c2-c-hc
```

## BondBond

PCFF section title: _bond-bond_ (line #[3363](pcff.frc#L3363))

Starting on line 3363 of pcff.frc that comes with LAMMPS for the 1st value.
The 2nd and 3rd values come from equilibrium bond lengths (R0) in
_quartic_bond_ section of pcff.frc (starting on line 1646); also the 1st
value in the [Bond](#bond) section.

If the 1st value is zero, can set the 2nd and 3rd values to anything.
Forward and reverse should be same for 1st value, but 2nd and 3rd values
must match the forcefield order. The order for each the 2nd and 3rd can be
reversed.

Acceptable ordering in `pcff.frc` for forcefield of i-j-k:

| Value | Order            |
| :---  | :---             |
| 1st   | any of: ijk, kji |
| 2nd   | any of: ij, ji   |
| 3rd   | any of: jk, kj   |

| i    | j    | k    | 1st                      | 2nd    | 3rd    |
| :--- | :--- | :--- | ---:                     | ---:   | ---:   |
| hc   | c2   | hc   | [5.3316](pcff.frc#L3412) | 1.1010 | 1.1010 |
| hc   | c2   | c    | [3.3872](pcff.frc#L3382) | 1.1010 | 1.5300 |
| c3   | c    | c3   | [0.0000](pcff.frc#L3374) | 1.5300 | 1.5300 |
| c3   | c    | c2   | [0.0000](pcff.frc#L3374) | 1.5300 | 1.5300 |
| hc   | c3   | hc   | [5.3316](pcff.frc#L3412) | 1.1010 | 1.1010 |
| hc   | c3   | c    | [3.3872](pcff.frc#L3382) | 1.1010 | 1.5300 |
| hc   | c    | c3   | [3.3872](pcff.frc#L3382) | 1.1010 | 1.5300 |
| hc   | c    | c2   | [3.3872](pcff.frc#L3382) | 1.1010 | 1.5300 |
| c2   | c    | c2   | [0.0000](pcff.frc#L3374) | 1.5300 | 1.5300 |
| c    | c2   | c    | [0.0000](pcff.frc#L3374) | 1.5300 | 1.5300 |

### Example LAMMPS Datafile Section


```
BondBond Coeffs

   1   5.3316 1.1010  1.1010 # hc-c2-hc
   2   3.3872 1.1010  1.5300 # hc-c2-c
   3   0.0000 1.5300  1.5300 # c3-c-c3
   4   0.0000 1.5300  1.5300 # c3-c-c2
   5   5.3316 1.1010  1.1010 # hc-c3-hc
   6   3.3872 1.1010  1.5300 # hc-c3-c
   7   3.3872 1.1010  1.5300 # hc-c-c3
   8   3.3872 1.1010  1.5300 # hc-c-c2
   9   0.0000 1.5300  1.5300 # c2-c-c2
  10   0.0000 1.5300  1.5300 # c-c2-c
```

## BondAngle

PCFF section title: _bond-angle_ (line #[3688](pcff.frc#L3688))

Starting on line 3688 of pcff.frc that comes with LAMMPS for the 1st and 2nd
value. If the 2nd value in pcff.frc is empty, then it is the same as the 1st
value. The 3rd and 4th values come from equilibrium bond lengths (R0) in the
_quartic_bond_ section of pcff.frc (starting on line 1646); also the 1st
value in the [Bond](#bond) section.

If ff order is reversed, swap the 1st and 2nd values. The 3rd and 4th values
must match the forcefield order, but the order for each the 3rd and 4th
values can be reversed.

Acceptable ordering in `pcff.frc` for forcefield of i-j-k:

| Value | Order                             |
| :---  | :---                              |
| 1st   | any of: ijk, kji                  |
| 2nd   | any of: ijk, kji                  |
| 3rd   | if ijk above, then any of: ij, ji |
|       | if kji above, then any of: kj, jk |
| 4th   | if ijk above, then any of: jk, kj |
|       | if kji above, then any of: ji, ij |

| i    | j    | k    | 1st                       | 2nd                       | 3rd    | 4th    |
| :--- | :--- | :--- | ---:                      | ---:                      | ---:   | ---:   |
| hc   | c2   | hc   | [18.1030](pcff.frc#L3737) | [18.1030](pcff.frc#L3737) | 1.1010 | 1.1010 |
| hc   | c2   | c    | [11.4210](pcff.frc#L3707) | [20.7540](pcff.frc#L3707) | 1.1010 | 1.5300 |
| c3   | c    | c3   | [ 8.0160](pcff.frc#L3699) | [ 8.0160](pcff.frc#L3699) | 1.5300 | 1.5300 |
| c3   | c    | c2   | [ 8.0160](pcff.frc#L3699) | [ 8.0160](pcff.frc#L3699) | 1.5300 | 1.5300 |
| hc   | c3   | hc   | [18.1030](pcff.frc#L3737) | [18.1030](pcff.frc#L3737) | 1.1010 | 1.1010 |
| hc   | c3   | c    | [11.4210](pcff.frc#L3707) | [20.7540](pcff.frc#L3707) | 1.1010 | 1.5300 |
| hc   | c    | c3   | [11.4210](pcff.frc#L3707) | [20.7540](pcff.frc#L3707) | 1.1010 | 1.5300 |
| hc   | c    | c2   | [11.4210](pcff.frc#L3707) | [20.7540](pcff.frc#L3707) | 1.1010 | 1.5300 |
| c2   | c    | c2   | [ 8.0160](pcff.frc#L3699) | [ 8.0160](pcff.frc#L3699) | 1.5300 | 1.5300 |
| c    | c2   | c    | [ 8.0160](pcff.frc#L3699) | [ 8.0160](pcff.frc#L3699) | 1.5300 | 1.5300 |

### Example LAMMPS Datafile Section

```
BondAngle Coeffs

   1   18.1030 18.1030 1.1010  1.1010 # hc-c2-hc
   2   11.4210 20.7540 1.1010  1.5300 # hc-c2-c
   3    8.0160  8.0160 1.5300  1.5300 # c3-c-c3
   4    8.0160  8.0160 1.5300  1.5300 # c3-c-c2
   5   18.1030 18.1030 1.1010  1.1010 # hc-c3-hc
   6   11.4210 20.7540 1.1010  1.5300 # hc-c3-c
   7   11.4210 20.7540 1.1010  1.5300 # hc-c-c3
   8   11.4210 20.7540 1.1010  1.5300 # hc-c-c2
   9    8.0160  8.0160 1.5300  1.5300 # c2-c-c2
  10    8.0160  8.0160 1.5300  1.5300 # c-c2-c
```

## AngleAngle

PCFF section title: _angle-angle_ (line #[3930](pcff.frc#L3930))

Starting on line 3930 of pcff.frc that comes with LAMMPS.  This is class2
component of improper. The 4th, 5th, and 6th values come from equilibrium
angles (Theta0) in the quartic_angle section that starts on line 2119 for
Theta_ijk, Theta_ijl, and Theta_kjl; also the 1st value in the
[Angle](#angle) section.

Acceptable ordering in `pcff.frc` for forcefield of i-j-k-l:

| Value | Order              |
| :---  | :---               |
| 1st   | any of: ijkl, ljki |
| 2nd   | any of: ljik, kjil |
| 3rd   | any of: ijlk, kjli |
| 4th   | any of: ijk, kji   |
| 5th   | any of: ijl, lji   |
| 6th   | any of: kjl, ljk   |

| i    | j    | k    | l    | 1st  | 2nd  | 3rd  | 4th  | 5th  | 6th  |
| :--- | :--- | :--- | :--- | ---: | ---: | ---: | ---: | ---: | ---: |
| c  | c2 | hc | hc | [ 0.2738](pcff.frc#L3999) | [-0.4825](pcff.frc#L3965) | [ 0.2738](pcff.frc#L3999) |110.7700|110.7700|107.6600|
| c2 | c  | c3 | c3 | [-0.1729](pcff.frc#L3948) | [-0.1729](pcff.frc#L3948) | [-0.1729](pcff.frc#L3948) |112.6700|112.6700|112.6700|
| c  | c3 | hc | hc | [ 0.2738](pcff.frc#L3999) | [-0.4825](pcff.frc#L3965) | [ 0.2738](pcff.frc#L3999) |110.7700|110.7700|107.6600|
| hc | c3 | hc | hc | [-0.3157](pcff.frc#L4017) | [-0.3157](pcff.frc#L4017) | [-0.3157](pcff.frc#L4017) |107.6600|107.6600|107.6600|
| hc | c2 | hc | hc | [-0.3157](pcff.frc#L4017) | [-0.3157](pcff.frc#L4017) | [-0.3157](pcff.frc#L4017) |107.6600|107.6600|107.6600|
| c2 | c  | c3 | hc | [-1.3199](pcff.frc#L3951) | [-1.3199](pcff.frc#L3951) | [ 0.1184](pcff.frc#L3993) |112.6700|110.7700|110.7700|
| c3 | c  | c3 | hc | [-1.3199](pcff.frc#L3951) | [-1.3199](pcff.frc#L3951) | [ 0.1184](pcff.frc#L3993) |112.6700|110.7700|110.7700|
| c2 | c  | c3 | c2 | [-0.1729](pcff.frc#L3948) | [-0.1729](pcff.frc#L3948) | [-0.1729](pcff.frc#L3948) |112.6700|112.6700|112.6700|
| c3 | c  | c3 | c2 | [-0.1729](pcff.frc#L3948) | [-0.1729](pcff.frc#L3948) | [-0.1729](pcff.frc#L3948) |112.6700|112.6700|112.6700|
| c  | c2 | c  | hc | [ 1.3199](pcff.frc#L3951) | [-1.3199](pcff.frc#L3951) | [ 0.1184](pcff.frc#L3993) |112.6700|110.7700|110.7700|


### Example LAMMPS Datafile Section

```
AngleAngle Coeffs

   1   0.2738 -0.4825  0.2738 110.7700 110.7700 107.6600 # c-c2-hc-hc
   2  -0.1729 -0.1729 -0.1729 112.6700 112.6700 112.6700 # c2-c-c3-c3
   3   0.2738 -0.4825  0.2738 110.7700 110.7700 107.6600 # c-c3-hc-hc
   4  -0.3157 -0.3157 -0.3157 107.6600 107.6600 107.6600 # hc-c3-hc-hc
   5  -0.3157 -0.3157 -0.3157 107.6600 107.6600 107.6600 # hc-c2-hc-hc
   6  -1.3199 -1.3199  0.1184 112.6700 110.7700 110.7700 # c2-c-c3-hc
   7  -1.3199 -1.3199  0.1184 112.6700 110.7700 110.7700 # c3-c-c3-hc
   8  -0.1729 -0.1729 -0.1729 112.6700 112.6700 112.6700 # c2-c-c3-c2
   9  -0.1729 -0.1729 -0.1729 112.6700 112.6700 112.6700 # c3-c-c3-c2
  10  -1.3199 -1.3199  0.1184 112.6700 110.7700 110.7700 # c-c2-c-hc
```

## AngleAngleTorsion

PCFF section title: _angle-angle-torsion_ (line #[5183](pcff.frc#L5183))

Starting on line 5183 of pcff.frc that comes with LAMMPS for the 1st value.
The 2nd and 3rd values come from equilibrium angles of (Theta0) in the
quartic_angle section that starts on line 2119 for Theta_ijk and Theta_jkl.
Theta_ijk = Theta_kji ; also the 1st value in the [Angle](#angle) section.

Acceptable ordering in `pcff.frc` for forcefield of i-j-k-l:

| Value | Order              |
| :---  | :---               |
| 1st   | any of: ijkl, lkji |
| 2nd   | any of: ijk, kji   |
| 3rd   | any of: jkl, lkj   |

| i    | j    | k    | l    | 1st                        | 2nd      | 3rd      |
| :--- | :--- | :--- | :--- | ---:                       | ---:     | ---:     |
| hc   | c2   | c    | c3   | [-16.1640](pcff.frc#L5206) | 110.7700 | 112.6700 |
| c3   | c    | c3   | hc   | [-16.1640](pcff.frc#L5206) | 112.6700 | 110.7700 |
| c2   | c    | c3   | hc   | [-16.1640](pcff.frc#L5206) | 112.6700 | 110.7700 |
| hc   | c2   | c    | hc   | [-12.5640](pcff.frc#L5241) | 110.7700 | 110.7700 |
| hc   | c    | c3   | hc   | [-12.5640](pcff.frc#L5241) | 110.7700 | 110.7700 |
| hc   | c2   | c    | c2   | [-16.1640](pcff.frc#L5206) | 110.7700 | 112.6700 |
| c3   | c    | c2   | c    | [-22.0450](pcff.frc#L5199) | 112.6700 | 112.6700 |
| c2   | c    | c2   | c    | [-22.0450](pcff.frc#L5199) | 112.6700 | 112.6700 |
| c    | c2   | c    | hc   | [-16.1640](pcff.frc#L5206) | 112.6700 | 110.7700 |

### Example LAMMPS Datafile Section

```
AngleAngleTorsion Coeffs

   1   -16.1640 110.7700 112.6700 # hc-c2-c-c3
   2   -16.1640 112.6700 110.7700 # c3-c-c3-hc
   3   -16.1640 112.6700 110.7700 # c2-c-c3-hc
   4   -12.5640 110.7700 110.7700 # hc-c2-c-hc
   5   -12.5640 110.7700 110.7700 # hc-c-c3-hc
   6   -16.1640 110.7700 112.6700 # hc-c2-c-c2
   7   -22.0450 112.6700 112.6700 # c3-c-c2-c
   8   -22.0450 112.6700 112.6700 # c2-c-c2-c
   9   -16.1640 112.6700 110.7700 # c-c2-c-hc
```

## EndBondTorsion

PCFF section title: _end_bond-torsion_ (line #[4208](pcff.frc#L4208))

Starting on line 4208 of pcff.frc that comes with LAMMPS for the first 6
values. If using reversed ordering, swap the LEFT and RIGHT parameters (the
first set of 3 numbers and the second set of 3 numbers). If the RIGHT
parameters are empty, then they are the same as the LEFT parameters. The 7th
and 8th values are the equilibrium bond lengths of the end bonds found from
R0 in the _quartic_bond_ section of pcff.frc (starting on line 1646); also
the 1st value in the [Bond](#bond) section.

Acceptable ordering in `pcff.frc` for forcefield of i-j-k-l:

| Value | Order              |
| :---  | :---               |
| 1st   | any of: ijkl, lkji |
| 2nd   | any of: ijkl, lkji |
| 3rd   | any of: ijkl, lkji |
| 4th   | any of: ijkl, lkji |
| 5th   | any of: ijkl, lkji |
| 6th   | any of: ijkl, lkji |
| 7th   | any of: ij, ji     |
| 8th   | any of: kl, lk     |

| i    | j    | k    | l    | 1st  | 2nd  | 3rd  | 4th  | 5th  | 6th  | 7th  | 8th  |
| :--- | :--- | :--- | :--- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| hc | c2 | c  | c3 |  [0.0814](pcff.frc#L4223) | [0.0591](pcff.frc#L4223) |  [0.2219](pcff.frc#L4223) | [0.2486](pcff.frc#L4223) | [0.2422](pcff.frc#L4223) | [-0.0925](pcff.frc#L4223) | 1.1010 | 1.5300 |
| c3 | c  | c3 | hc |  [0.2486](pcff.frc#L4223) | [0.2422](pcff.frc#L4223) | [-0.0925](pcff.frc#L4223) | [0.0814](pcff.frc#L4223) | [0.0591](pcff.frc#L4223) |  [0.2219](pcff.frc#L4223) | 1.5300 | 1.1010 |
| c2 | c  | c3 | hc |  [0.2486](pcff.frc#L4223) | [0.2422](pcff.frc#L4223) | [-0.0925](pcff.frc#L4223) | [0.0814](pcff.frc#L4223) | [0.0591](pcff.frc#L4223) |  [0.2219](pcff.frc#L4223) | 1.5300 | 1.1010 |
| hc | c2 | c  | hc |  [0.2130](pcff.frc#L4258) | [0.3120](pcff.frc#L4258) |  [0.0777](pcff.frc#L4258) | [0.2130](pcff.frc#L4258) | [0.3120](pcff.frc#L4258) |  [0.0777](pcff.frc#L4258) | 1.1010 | 1.1010 |
| hc | c  | c3 | hc |  [0.2130](pcff.frc#L4258) | [0.3120](pcff.frc#L4258) |  [0.0777](pcff.frc#L4258) | [0.2130](pcff.frc#L4258) | [0.3120](pcff.frc#L4258) |  [0.0777](pcff.frc#L4258) | 1.1010 | 1.1010 |
| hc | c2 | c  | c2 |  [0.0814](pcff.frc#L4223) | [0.0591](pcff.frc#L4223) |  [0.2219](pcff.frc#L4223) | [0.2486](pcff.frc#L4223) | [0.2422](pcff.frc#L4223) | [-0.0925](pcff.frc#L4223) | 1.1010 | 1.5300 |
| c3 | c  | c2 | c  | [-0.0732](pcff.frc#L4216) | [0.0000](pcff.frc#L4216) |  [0.0000](pcff.frc#L4216) |[-0.0732](pcff.frc#L4216) | [0.0000](pcff.frc#L4216) |  [0.0000](pcff.frc#L4216) | 1.5300 | 1.5300 |
| c2 | c  | c2 | c  | [-0.0732](pcff.frc#L4216) | [0.0000](pcff.frc#L4216) |  [0.0000](pcff.frc#L4216) |[-0.0732](pcff.frc#L4216) | [0.0000](pcff.frc#L4216) |  [0.0000](pcff.frc#L4216) | 1.5300 | 1.5300 |
| c  | c2 | c  | hc |  [0.2486](pcff.frc#L4223) | [0.2422](pcff.frc#L4223) | [-0.0925](pcff.frc#L4223) | [0.0814](pcff.frc#L4223) | [0.0591](pcff.frc#L4223) |  [0.2219](pcff.frc#L4223) | 1.5300 | 1.1010 |


### Example LAMMPS Datafile Section

```
EndBondTorsion Coeffs

   1   0.0814  0.0591  0.2219   0.2486  0.2422 -0.0925 1.1010 1.5300 # hc-c2-c-c3
   2   0.2486  0.2422 -0.0925   0.0814  0.0591  0.2219 1.5300 1.1010 # c3-c-c3-hc
   3   0.2486  0.2422 -0.0925   0.0814  0.0591  0.2219 1.5300 1.1010 # c2-c-c3-hc
   4   0.2130  0.3120  0.0777   0.2130  0.3120  0.0777 1.1010 1.1010 # hc-c2-c-hc
   5   0.2130  0.3120  0.0777   0.2130  0.3120  0.0777 1.1010 1.1010 # hc-c-c3-hc
   6   0.0814  0.0591  0.2219   0.2486  0.2422 -0.0925 1.1010 1.5300 # hc-c2-c-c2
   7  -0.0732  0.0000  0.0000  -0.0732  0.0000  0.0000 1.5300 1.5300 # c3-c-c2-c
   8  -0.0732  0.0000  0.0000  -0.0732  0.0000  0.0000 1.5300 1.5300 # c2-c-c2-c
   9   0.2486  0.2422 -0.0925   0.0814  0.0591  0.2219 1.5300 1.1010 # c-c2-c-hc
```

## MiddleBondTorsion

PCFF section title: _middle_bond-torsion_ (line #[4509](pcff.frc#L4509))

Starting on line 4509 of pcff.frc that comes with LAMMPS for the first 3
values. The 4th value is the equilibrium bond length of the middle bond
found from R0 in the _quartic_bond_ section (starting on line 1646); also
the 1st value in the [Bond](#bond) section.

Acceptable ordering in `pcff.frc` for forcefield of i-j-k-l:

| Value | Order              |
| :---  | :---               |
| 1st   | any of: ijkl, lkji |
| 2nd   | any of: ijkl, lkji |
| 3rd   | any of: ijkl, lkji |
| 4th   | any of: jk, kj     |

| i    | j    | k    | l    | 1st  | 2nd  | 3rd  | 4th  |
| :--- | :--- | :--- | :--- | ---: | ---: | ---: | ---: |
| hc | c2 | c  | c3 | [-14.8790](pcff.frc#L4523) | [-3.6581](pcff.frc#L4523) | [-0.3138](pcff.frc#L4523) | 1.5300 |
| c3 | c  | c3 | hc | [-14.8790](pcff.frc#L4523) | [-3.6581](pcff.frc#L4523) | [-0.3138](pcff.frc#L4523) | 1.5300 |
| c2 | c  | c3 | hc | [-14.8790](pcff.frc#L4523) | [-3.6581](pcff.frc#L4523) | [-0.3138](pcff.frc#L4523) | 1.5300 |
| hc | c2 | c  | hc | [-14.2610](pcff.frc#L4558) | [-0.5322](pcff.frc#L4558) | [-0.4864](pcff.frc#L4558) | 1.5300 |
| hc | c  | c3 | hc | [-14.2610](pcff.frc#L4558) | [-0.5322](pcff.frc#L4558) | [-0.4864](pcff.frc#L4558) | 1.5300 |
| hc | c2 | c  | c2 | [-14.8790](pcff.frc#L4523) | [-3.6581](pcff.frc#L4523) | [-0.3138](pcff.frc#L4523) | 1.5300 |
| c3 | c  | c2 | c  | [-17.7870](pcff.frc#L4516) | [-7.1877](pcff.frc#L4516) |  [0.0000](pcff.frc#L4516) | 1.5300 |
| c2 | c  | c2 | c  | [-17.7870](pcff.frc#L4516) | [-7.1877](pcff.frc#L4516) |  [0.0000](pcff.frc#L4516) | 1.5300 |
| c  | c2 | c  | hc | [-14.8790](pcff.frc#L4523) | [-3.6581](pcff.frc#L4523) | [-0.3138](pcff.frc#L4523) | 1.5300 |


### Example LAMMPS Datafile Section

```
MiddleBondTorsion Coeffs

   1   -14.8790 -3.6581 -0.3138 1.5300 # hc-c2-c-c3
   2   -14.8790 -3.6581 -0.3138 1.5300 # c3-c-c3-hc
   3   -14.8790 -3.6581 -0.3138 1.5300 # c2-c-c3-hc
   4   -14.2610 -0.5322 -0.4864 1.5300 # hc-c2-c-hc
   5   -14.2610 -0.5322 -0.4864 1.5300 # hc-c-c3-hc
   6   -14.8790 -3.6581 -0.3138 1.5300 # hc-c2-c-c2
   7   -17.7870 -7.1877  0.0000 1.5300 # c3-c-c2-c
   8   -17.7870 -7.1877  0.0000 1.5300 # c2-c-c2-c
   9   -14.8790 -3.6581 -0.3138 1.5300 # c-c2-c-hc
```

## BondBond13

PCFF section title: _bond-bond_1_3_ (line #[3616](pcff.frc#L3616))

Starting on line 3616 of pcff.frc that comes with LAMMPS for the 1st value.
If not found, use 0.0 for the 1st value. The 2nd and 3rd values are the equilibrium bond length of the end bonds
found from R0 in the _quartic_bond_ section (starting on line 1646); also
the 1st value in the [Bond](#bond) section.

Acceptable ordering in `pcff.frc` for forcefield of i-j-k-l:

| Value | Order              |
| :---  | :---               |
| 1st   | any of: ijkl, lkji |
| 2nd   | any of: ij, ji     |
| 3rd   | any of: kl, lk     |

| i    | j    | k    | l    | 1st    | 2nd    | 3rd    |
| :--- | :--- | :--- | :--- | ---:   | ---:   | ---:   |
| hc   | c2   | c    | c3   | 0.0000 | 1.1010 | 1.5300 |
| c3   | c    | c3   | hc   | 0.0000 | 1.5300 | 1.1010 |
| c2   | c    | c3   | hc   | 0.0000 | 1.5300 | 1.1010 |
| hc   | c2   | c    | hc   | 0.0000 | 1.1010 | 1.1010 |
| hc   | c    | c3   | hc   | 0.0000 | 1.1010 | 1.1010 |
| hc   | c2   | c    | c2   | 0.0000 | 1.1010 | 1.5300 |
| c3   | c    | c2   | c    | 0.0000 | 1.5300 | 1.5300 |
| c2   | c    | c2   | c    | 0.0000 | 1.5300 | 1.5300 |
| c    | c2   | c    | hc   | 0.0000 | 1.5300 | 1.1010 |

### Example LAMMPS Datafile Section

```
BondBond13 Coeffs

   1   0.0000 1.1010 1.5300 # hc-c2-c-c3
   2   0.0000 1.5300 1.1010 # c3-c-c3-hc
   3   0.0000 1.5300 1.1010 # c2-c-c3-hc
   4   0.0000 1.1010 1.1010 # hc-c2-c-hc
   5   0.0000 1.1010 1.1010 # hc-c-c3-hc
   6   0.0000 1.1010 1.5300 # hc-c2-c-c2
   7   0.0000 1.5300 1.5300 # c3-c-c2-c
   8   0.0000 1.5300 1.5300 # c2-c-c2-c
   9   0.0000 1.5300 1.1010 # c-c2-c-hc
```

## AngleTorsion

PCFF section title: _angle-torsion_3_ (line #[4869](pcff.frc#L4869))

Starting on line 4869 of pcff.frc that comes with LAMMPS for the first 6
values. If using reversed ordering (lkji), swap the LEFT and RIGHT parameters (the
first set of 3 numbers and the second set of 3 numbers). The 7th and 8th
values are the equilibrium bond angles (Theta0) in the quartic_angle section
that starts on line 2119 for Theta_ijk, Theta_jkl; also the 1st value in
the [Angle](#angle) section.

Acceptable ordering in `pcff.frc` for forcefield of i-j-k-l:

| Value | Order              |
| :---  | :---               |
| 1st   | any of: ijkl, lkji |
| 2nd   | any of: ijkl, lkji |
| 3rd   | any of: ijkl, lkji |
| 4th   | any of: ijkl, lkji |
| 5th   | any of: ijkl, lkji |
| 6th   | any of: ijkl, lkji |
| 7th   | any of: ijk, kji   |
| 8th   | any of: jkl, lkj   |

| i    | j    | k    | l    | 1st  | 2nd  | 3rd  | 4th  | 5th  | 6th  | 7th  | 8th  |
| :--- | :--- | :--- | :--- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| hc | c2 | c  | c3 | [ 0.3113](pcff.frc#L4885) | [0.4516](pcff.frc#L4885) | [-0.1988](pcff.frc#L4885) | [-0.2454](pcff.frc#L4885)| [0.0000](pcff.frc#L4885) | [-0.1136](pcff.frc#L4885) |110.7700|112.6700|
| c3 | c  | c3 | hc | [-0.2454](pcff.frc#L4885) | [0.0000](pcff.frc#L4885) | [-0.1136](pcff.frc#L4885) | [ 0.3113](pcff.frc#L4885)| [0.4516](pcff.frc#L4885) | [-0.1988](pcff.frc#L4885) |112.6700|110.7700|
| c2 | c  | c3 | hc | [-0.2454](pcff.frc#L4885) | [0.0000](pcff.frc#L4885) | [-0.1136](pcff.frc#L4885) | [ 0.3113](pcff.frc#L4885)| [0.4516](pcff.frc#L4885) | [-0.1988](pcff.frc#L4885) |112.6700|110.7700|
| hc | c2 | c  | hc |  [-0.8085](pcff.frc#L4920) | [0.5569](pcff.frc#L4920) |  [-0.2466](pcff.frc#L4920) |  [-0.8085](pcff.frc#L4920)| [0.5569](pcff.frc#L4920) |  [-0.2466](pcff.frc#L4920) |110.7700|110.7700|
| hc | c  | c3 | hc |  [-0.8085](pcff.frc#L4920) | [0.5569](pcff.frc#L4920) |  [-0.2466](pcff.frc#L4920) |  [-0.8085](pcff.frc#L4920)| [0.5569](pcff.frc#L4920) |  [-0.2466](pcff.frc#L4920) |110.7700|110.7700|
| hc | c2 | c  | c2 |  [0.3113](pcff.frc#L4885) | [0.4516](pcff.frc#L4885) | [-0.1988](pcff.frc#L4885) | [-0.2454](pcff.frc#L4885)| [0.0000](pcff.frc#L4885) | [0.1389](pcff.frc#L4885) |110.7700|112.6700|
| c3 | c  | c2 | c  | [0.3886](pcff.frc#L4878) | [-0.3139](pcff.frc#L4878) |  [0.1389](pcff.frc#L4878) | [0.3886](pcff.frc#L4878)| [-0.3139](pcff.frc#L4878) |  [0.1389](pcff.frc#L4878) |112.6700|112.6700|
| c2 | c  | c2 | c  | [0.3886](pcff.frc#L4878) | [-0.3139](pcff.frc#L4878) |  [0.1389](pcff.frc#L4878) | [0.3886](pcff.frc#L4878)| [-0.3139](pcff.frc#L4878) |  [0.1389](pcff.frc#L4878) |112.6700|112.6700|
| c  | c2 | c  | hc | [-0.2454](pcff.frc#L4885) | [0.0000](pcff.frc#L4885) | [-0.1136](pcff.frc#L4885) | [ 0.3113](pcff.frc#L4885)| [0.4516](pcff.frc#L4885) | [-0.1988](pcff.frc#L4885) |112.6700|110.7700|


### Example LAMMPS Datafile Section

```
AngleTorsion Coeffs

   1    0.3113  0.4516 -0.1988 -0.2454  0.0000 -0.1136 110.7700 112.6700 # hc-c2-c-c3
   2   -0.2454  0.0000 -0.1136  0.3113  0.4516 -0.1988 112.6700 110.7700 # c3-c-c3-hc
   3   -0.2454  0.0000 -0.1136  0.3113  0.4516 -0.1988 112.6700 110.7700 # c2-c-c3-hc
   4   -0.8085  0.5569 -0.2466 -0.8085  0.5569 -0.2466 110.7700 110.7700 # hc-c2-c-hc
   5   -0.8085  0.5569 -0.2466 -0.8085  0.5569 -0.2466 110.7700 110.7700 # hc-c-c3-hc
   6    0.3113  0.4516 -0.1988 -0.2454  0.0000 -0.1136 110.7700 112.6700 # hc-c2-c-c2
   7    0.3886 -0.3139  0.1389  0.3886 -0.3139  0.1389 112.6700 112.6700 # c3-c-c2-c
   8    0.3886 -0.3139  0.1389  0.3886 -0.3139  0.1389 112.6700 112.6700 # c2-c-c2-c
   9   -0.2454  0.0000 -0.1136  0.3113  0.4516 -0.1988 112.6700 110.7700 # c-c2-c-hc
```
