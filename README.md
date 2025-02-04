# Simple microstrip examples in openEMS

This repository contains simple examples of simulations of RF signal propagation
in microstrip and related transmission lines.

![example](example.gif)

### Repository contents

- `microstrip`: simple microstrip line

- `microstrip_blob`: simple microstrip line, with a circular blob in the middle,
  from which signal reflects backwards

- `cpw`: simple grounded coplanar waveguide

- `cpw_vias`: simple coplanar waveguide, with via fence around the trace

- `cpw_more_vias`: simple coplanar waveguide, with via fence around the trace,
  as well as vias in the rest of the ground pour

- `cpw_fewer_vias`: CPW with only a couple vias around the trace

- `cpw_wide`: CPW on a wide PCB, to be able to see the waves launched
  away from the trace

### Prerequisites

The simulations are done in [openEMS](https://www.openems.de/)
([Github page](https://github.com/thliebig/openEMS)). The geometry is prepared
in [KiCad](https://www.kicad.org/) and converted to the form needed by openEMS
by [gerber2ems](https://github.com/antmicro/gerber2ems)
([quick tutorial](https://nuclearrambo.com/wordpress/gerber2ems-a-short-tutorial-to-simulate-your-pcbs-in-openems/)).
After the simulation has finished, the fields can be viewed in
[ParaView](https://www.paraview.org/). I used Python version 3.10 set up as an
Anaconda environment.

1. Create a new Anaconda environment:

       $ conda create -n test python=3.10
       $ conda activate test

   Replace `test` with any desired environment name.

2. Obtain `gerber2ems` and install it:

       $ git clone git@github.com:antmicro/gerber2ems.git
       $ cd gerber2ems
       $ pip install .

3. Download openEMS and install the Python interface:

       $ cd C:\openEMS\python
       $ pip install CSXCAD-0.6.3-cp310-cp310-win_amd64.whl
       $ pip install openEMS-0.0.36-cp310-cp310-win_amd64.whl

4. Install `h5py`:

       $ pip install h5py

### Getting started with the simulation

1. Go into one of the example folders, such as `microstrip`, and open the KiCad
   project (`basic.kicad_pro`).

2. In KiCad, open the PCB Editor and go to File → Plot. Make sure the Output
   directory is set to `fab`, and press Plot. Under Generate Drill Files...,
   press Generate Drill File.

   Still in the PCB Editor, go to File → Fabrication Outputs → Component
   Placement. Make sure the output directory is set to `fab` and press Generate
   Position File.

3. In the `fab` directory, open `stackup.json` in a text editor and verify that
   the layer stackup is as desired for the simulation. In particular, check the
   dielectric thickness, epsilon, and the loss tangent.

4. Open `simulation.json` in a text editor and adjust the values as desired.
   Note that all dimensions are in microns. In particular, make sure the mesh
   spacing is appropriate to the features on the PCB, that the margin is
   sufficient, and the that ports have the correct width, length, and impedance.

5. Open the Anaconda Prompt and activate the correct environment:

       $ conda activate py10

6. Change to the simulation directory and start the simulation:

       $ gerber2ems -a --export-field

While waiting for the simulation to complete, examine the files in
`ems/geometry`. The Gerber files from KiCad get converted to PNG images, and the
exact physical and mesh geometry is encoded in `geometry.xml`. The XML file can
be opened in the [AppCSXCAD](https://github.com/thliebig/AppCSXCAD) viewer
(included in the openEMS distribution).

When at least a couple timesteps have completed, the fields will be available
for viewing under `ems/simulation/0`. Open the `e_field...` group of files in
ParaView, select Apply in the left Properties sidebar, and change the "Solid
Color" to "E-Field". Click "Rescale to data range over all timesteps" to adjust
the data range, the press play to view the field animation.

Once the simulation completes, `ems/results` will contain a Smith chart, S
parameters, and the port impedance.

### Author

Jakob Kastelic, Stanford Research Systems
