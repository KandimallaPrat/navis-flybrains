<p align="center">
<img src="https://github.com/KandimallaPrat/navis-flybrains/blob/main/_static/flybrains_logo.png?raw=true" width="400">
</p>

# navis-flybrains
Transforms to map between different _Drosophila_ template brains. Intended to be used with [navis](https://github.com/schlegelp/navis).

This library is analogous to Greg Jefferis' [nat.templatebrains](https://github.com/natverse/nat.templatebrains), [nat.jrcbrains](https://github.com/natverse/nat.jrcbrains) and [nat.flybrains](https://github.com/natverse/nat.flybrains) for R.

`flybrains` ships with:

- meta data + surface meshes for 22+ template brains and nerve cords
- an Elastix transform between `FANC` and `JRCVNC2018F` kindly shared by Jasper Phelps
- a landmark-based transform between `MANC` and `FANC`
- mirror transforms for `FAFB14` and `FANC`
- mirror transforms warp for `PK_L1CNS`

There are plenty additional transforms that need to be downloaded separately (see below).

## Installation
You can install `flybrains` from PyPI:

```bash
pip3 install flybrains
```

To install the dev version from Github:
```bash
pip3 install git+https://github.com/KandimallaPrat/navis-flybrains
```

### External dependencies
In order to use the Jefferis lab or VFB transforms, you will need to have
[CMTK](https://www.nitrc.org/projects/cmtk/) installed.

For the FANC to JRCVNC2018F transform, you will need to download
[elastix](https://elastix.lumc.nl/index.php) and make sure that the path
to the binaries is in your `PATH` variable.

## Usage

<p align="center">
<img src="https://github.com/schlegelp/navis-flybrains/blob/main/_static/bridging_graph.png?raw=true" width="800">
</p>

It's highly recommended that after install, you download the (optional)
bridging transforms to map between template brains/nerve cords.

```Python
>>> import flybrains

# This downloads (or updates) various CMTK bridging and mirror transforms
# generated or collated by the Jefferis lab - see docstring for details
>>> flybrains.download_jefferislab_transforms()

# This downloads H5 bridging transforms between JRC brain templates
# (Saalfeld lab, Janelia Research Campus) - see docstring for details
>>> flybrains.download_jrc_transforms()

# This downloads H5 bridging transforms between JRC VNC templates
# (Saalfeld lab, Janelia Research Campus) - see docstring for details
>>> flybrains.download_jrc_vnc_transforms()

# This downloads (or updates) various CMTK bridging and mirror transforms
# generated or collated by VirtualFlyBrain.org - see docstring for details
>>> flybrains.download_vfb_transforms()

# Register the transforms - this is only necessary if you just downloaded them
# Alternatively just restart your Python session and import flybrains
>>> flybrains.register_transforms()
```

In the future, simply importing `flybrains` is sufficient to make the
transforms available to [navis](https://navis.readthedocs.io/en/latest/):

```Python
>>> import navis
>>> import flybrains
>>> import numpy as np
>>> points = np.array([[429536, 205240,  38400]])
>>> navis.xform_brain(points, source='FAFB', target='JRC2018F')
array([[241.53969657, 100.99399233,  35.96977733]])
```

Please see the [transform tutorial](https://navis.readthedocs.io/en/latest/source/tutorials/transforming.html)
for `navis` to learn how to transform more complex data.

To check which transforms are available (either downloaded or via R) you can
run this:

```Python
>>> # Generate a report - note the mix of transforms downloaded via Python and R
>>> flybrains.report()
Flybrains Status Report
=======================
Data Home: /home/prat/flybrain-data

CMTK registrations (Jefferis lab/VFB): 46 of 45
H5 registrations (JRC/Saalfeld lab): 10 of 8

nat regdirs
-----------
None
```

Meta data and surface meshes for the template brains/VNCs are readily accessible:
I have added the CNS mesh from the L1 CATMAID instance. 

```Python
>>> flybrains.PK_L1CNS
Template brain
--------------
Name: D. melanogaster L1 CNS
Short Name: PK_L1CNS
Type: None
Sex:  female
Dimensions: NA
Voxel size:
  x = 3.8 nanometers
  y = 3.8 nanometers
  z = 50 nanometers
Bounding box (nanometers):
  x = 939, y = 1717, z = 6464,
  x = 105096, y = 119252, z = 246630,
Description: VH|PK : CNS ssTEM at 3.8x3.8x50 nm of whole 1st instar larval CNS.
DOI: None
```

Most templates come with a mesh e.g. for plotting via navis:

```Python
>>> flybrains.PK_L1CNS.mesh
<trimesh.Trimesh(vertices.shape=(6630, 3), faces.shape=(13268, 3), name=`PK_L1CNS.ply`)>
>>> # You can pass the template object directly to navis' plotting functions
>>> navis.plot3d(flybrains.PK_L1CNS)
```

## Changes
- `0.2.9` (2024/01/06): [@KandimallaPrat](https://github.com/KandimallaPrat) added L1 CNS mesh and the left/right mirror transform (warp)
- `0.2.10` (2024/07/28): [@KandimallaPrat](https://github.com/KandimallaPrat) fixed error point (incorrectly in right-hemisphere) in left/right mirror transform (warp) of the L1 CNS mesh
- `0.2.11` (2024/10/02): [@KandimallaPrat](https://github.com/KandimallaPrat) improved left/right registration with VNC points in the L1 CNS. Also generated symmetric registration (right default).
- - `0.2.11` (2025/07/06): [@KandimallaPrat](https://github.com/KandimallaPrat) updated left/right registration with SEZ s-bundle points in the L1 CNS. Also generated corresponding symmetric registration (right default).

## Acknowledgements
`navis-flybrains` is critically based on `nat.flybrains` and `nat.jrcbrains` by Greg Jefferis
_et al._ for both inspiration regarding the implementation as well as template brain meta data.

My ([@KandimallaPrat](https://github.com/KandimallaPrat)) tiny changes are made possible by the very helpful and extensive documentation on [navis-org](https://github.com/navis-org) compiled by [Philipp Schlegel](https://github.com/schlegelp) _et al._! 

## Citing
If you use `navis-flybrains` in for your research please make sure to cite [Philipp Schlegel _et al._](https://doi.org/10.5281/zenodo.5515281), [navis](https://github.com/navis-org/navis) and _importantly_ the
people who generated the meshes & registrations distributed with `flybrains`!

As reference for the Jefferis lab registrations please use:

```
The natverse, a versatile toolbox for combining and analysing neuroanatomical data.
A.S. Bates, J.D. Manton, S.R. Jagannathan, M. Costa, P. Schlegel, T. Rohlfing, G.S. Jefferis
eLife (2020); doi: https://doi:10.7554/eLife.53350
```

As (partial) reference for the Saalfeld lab registrations please see:

```
An unbiased template of the Drosophila brain and ventral nerve cord.
John A Bogovic, Hideo Otsuna, Larissa Heinrich, Masayoshi Ito, Jennifer Jeter, Geoffrey Meissner, Aljoscha Nern, Jennifer Colonell, Oz Malkesman, Kei Ito, Stephan Saalfeld
PLOS One (2018); doi: https://doi.org/10.1371/journal.pone.0236495
```

As reference for the VFB CMTK transforms please use:

```
A Systematic Nomenclature for the Drosophila Ventral Nerve Cord.
Robert Court, Shigehiro Namiki, J. Douglas Armstrong, Jana Börner, Gwyneth Card, Marta Costa, Michael Dickinson, Carsten Duch, Wyatt Korff, Richard Mann, David Merritt, Rod K. Murphey, Andrew M. Seeds, Troy Shirangi, Julie H. Simpson, James W. Truman, John C. Tuthill, Darren W. Williams, David Shepherd
Neuron (2020); doi: https://doi.org/10.1016/j.neuron.2020.08.005.
```

As reference for the FANC<->JRCVNC2018F transform by Jasper Phelps please use:

```
Reconstruction of motor control circuits in adult Drosophila using automated transmission electron microscopy.
Phelps JS, Hildebrand DGC, Graham BJ, Kuan AT, Thomas LA, Nguyen TM, Buhmann J, Azevedo AW, Sustar A, Agrawal S, Liu M, Shanny BL, Funke J, Tuthill JC, Lee WA
Cell (2021); doi: 10.1016/j.cell.2020.12.013
```

For references on individual template brains, please see their docstrings:
```Python
>>> help(flybrains.IBN)
```
