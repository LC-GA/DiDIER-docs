# DiDIER

**DiMES Data Input & Experimental Records**

A Python package for entering, managing, and storing DiMES (Divertor Material Evaluation Studies) experimental data, including pre- and post-exposure characterizations.

## Features

- **Graphical User Interface**: Intuitive tkinter-based GUI for data entry
- **Structured Data Model**: Hierarchical data structures for experiments, heads, samples, and measurements
- **HDF5 Export**: Standardized HDF5 file format for data storage and sharing

## Installation

### Dependencies

- **python** >= 3.10
- **numpy** >= 1.22.0
- **h5py** >= 3.7.0
- **pandas** >= 1.5.0
- **pillow** >= 9.0.0

### Install with pip

Install with pip in editable mode:

```bash
pip install -e .
```

Or simple install

```bash
pip install .
```

Then add the package to your Python path or install it in development mode.

# Quick Start

This package is meant to be used by DIII-D users only to upload DiMES measurements on the cluster.
The package is available as a module on omega.

Type to launch:

```bash
module load didier
didier
```

As of now (February 2026, didier version 1.0) users can upload (for each sample) images and CSV files with the measurements of:

- elemental composition (pre- and post-exposure)
- roughness (pre- and post-exposure)
- morphology (pre- and post-exposure)
- areal density (pre- and post-exposure)
- temperature (per shot)
- bias (per shot)

Each file must be uploaded first in the cluster, preferably within the user `/home` or `/cscracth` directories. Then, it will be possible to store it organically within the DiMES database repository (situated on omega in /fusion/projects/results/dimes) using DiDIER. It is recommended to delete the files from their own home directory once DiDIER was used since the available space in the login nodes is usually limited.

## Permissions and ownership

Each user has the right to read and edit all files they uploaded.
Each user has the right ro read all files available in the database. 

## CSV File Format

**Premise:** For simplicity, only surface-averaged quantities are currently supported. Future versions may allow uploading more complex files tracking the spatial evolution along the surface.

At present, depth-profiling data of the surface elemental composition and areal density are treated as two-dimensional (depth × element), temperature and bias are one-dimensional time series representing thermocouple and power-generator outputs, and roughness is characterized by a set of zero-dimensional scalar parameters.
 

### FORMAT: Surface Elemental Composition & areal density

The first row must contain column headers. **The first column must be named `depth`**, and the remaining columns must list symbols for elements from the periodic table as strings (e.g., `W`, `C`, `O`, etc. **only use literal symbols from the periodic table**).

All subsequent rows contain numerical values. The `depth` column specifies the measurement depth within the sample. Depth values may be provided in micrometers (µm) or nanometers (nm). Units available are listed in the DiDIER GUI as option menus.

Below is an example CSV file showing the surface composition depth profile expressed in weight percentage:

```csv
depth,W,C,O
0,95,3,2
5,97,2,1
10,99,0.5,0.5
15,99.5,0.3,0.2
```

### FORMAT: Surface Roughness

The first row must contain column headers. Below is the list of column headers supported by DiDIER for surface roughness characterization:

- `Ra`, `Rz`, `Rq`, `Rp`, `Rv`, `Rt`, `Rsk`, `Rku`, `Rpk`, `Rvk`, `Rsm`  
- `Sa`, `Sz`, `Sq`, `Sp`, `Sv`, `Sdr`  
- `Pa`, `Pz`  
- `Wa`, `Wz`

Users must upload **at least** either `Sa` and `Sq` (surface average roughness and standard deviation over the analyzed area) **or** `Ra` and `Rq` (line average roughness and standard deviation along a surface profile).

The second (and last) row, must contain values. <u>Values must be reported in micrometers<u>.

Below is an example CSV file showing a list of surface roughness parameters in micrometers:

```csv
Ra,Rp,Rq,Rt
6.3,25.81,7.77,34.14
```

### Surface Geometry

The geometry of exposed samples is crucial for relating spectroscopic measurements to erosion rates and must be precisely defined.

DiDIER includes a built-in default surface geometry corresponding to samples used within the 7-button DiMES head. All exposed samples are assumed to be circular, to have a diameter of 6 mm, and be flush with the DiMES head.

The DiMES head can also be tilted. Users are therefore encouraged to provide at least the exposed surface area (in cm² or mm²) and the surface inclination (in degrees or rad), where 0° corresponds to the flush configuration, while 90° is perpendicular to the portion of vessel in proximity of the DiMES head.

Exposed samples may be **microstructured** to improve the thermo-mechanical performance of Plasma Facing Components (PFCs). A well-known example is tungsten tiles with a *castellated* structure, which are designed to mitigate thermal stresses.  

Currently, DiDIER classifies materials simply as either **microstructured** or **non-microstructured**. The specific type of microstructure cannot yet be selected from an option menu; however, users may manually describe the applied microstructure in the comments section associated with each sample.

### Temperature

Temperature measurements must be provided as **per-shot quantities**.

In each CSV file the first row must contain column headers. The following columns are required <u>literally<u>:

- `time`: time vector in seconds or milliseconds  
- `temp`: measured temperature in Kelvin, Celsius or Fahrenheit  

Example CSV file:

```csv
time,temp
0.,725
1.,742
2.,731
```

### Bias

Bias measurements must be provided as **per-shot quantities**.

In each CSV file the first row must contain column headers. The following columns are required <u>literally<u>:

- `time`: time vector in seconds or milliseconds  
- `bias`: measured voltage in Volts or kiloVolts  

```csv
time,bias
0.,-50
1.,-50
2.,-45
```


## Images

In DiDIER images for each DiMES head, sample morphology (e.g. scanning electron microscopy), and roughness (e.g. profilometry) may be uploaded. 

**Image constraints:**
- Maximum file size per image: **20 MB**
- Supported formats:  
  `PNG`, `JPEG`, `TIFF`, `BMP`, `GIF`, `PPM`, `PGM`, `PBM`, `ICO`

Please ensure all uploaded images meet these requirements before submission.

## Saving Data Using the Experiment ID

In DiDIER, data is organized by experiment. Each experiment performed at DIII-D is identified by a unique ID with the format:

`XXXX-YY-ZZ`

XXXX is the year, YY is the the topical area code, and ZZ is an internal number chosen by run coordinator. XXXX, YY, and ZZ are all integers.

This ID is used by DiDIER as the unique identifier for storing data in an HDF5 file named:

`XXXX-YY-ZZ.hdf5`

located in:

`/fusion/projects/results/dimes`

Some DiMES experiments, however, are conducted as "piggyback" experiments and may not have a formally assigned ID. If a proper naming convention has not been defined prior to using this package, please contact `cappellil@fusion.gat.com` to resolve this issue.


# Using the GUI

Once the CSV files and the MP ID are available, the GUI can be launched to begin uploading data.

In the DIII-D cluster, DiDIER is available as a module. To lunch the GUI within the cluster please type:

```bash
module load didier
didier
```

After launching a window opens where you can:

0. Create or Edit an existing HDF5 file you already created where DiMES experiment data is stored (/fusion/projects/results/dimes/XXX-YY-ZZ.hdf5)
1. Enter/Edit experiment identification
2. Define the number of DiMES heads exposed
3. For each head specify: design, port location, surface element, and number of samples
> **NOTE** If the inserted DiMES head design is set to *blank*, the head itself contains no samples and is therefore treated as a > single sample. For this reason, only **one** sample can be selected when the chosen design is *blank*.

4. For each sample, enter ID, label, comments, geometry, and measurement data
5. Save the data to an HDF5 file

## Data Structure

The package organizes data in a hierarchical structure, using a list of predefined dataclasses:

```
DiMESExperiment (dataclass defined within the package)
├── id (miniproposal ID)
└── heads (list of DimesHead dataclass)
    ├── shots (int or list of int)
    ├── design
    ├── port_location
    ├── surface_species
    ├── images
    └── samples (list of Sample dataclass)
        ├── sample_id
        ├── name
        ├── position
        ├── geometry (Geometry dataclass)
        │   ├── microstructured
        │   ├── area
        │   └── inclination
        └── measurements
            ├── pre_exposure
            │   ├── composition (SurfaceComposition dataclass)
            │   └── roughness (SurfaceRoughness dataclass)
            ├── exposure
            │   ├── temperature (TemperatureMeasurements dataclass)
            │   └── bias (BiasMeasurements dataclass)
            └── post_exposure
                ├── composition (SurfaceComposition dataclass)
                └── roughness (SurfaceRoughness dataclass)
```

## HDF5 File Structure

The saved HDF5 files follow this structure:

```
HDF5 File
├── DESIGN_VERSION (attr) # version of hdf5 design (the hdf5 structure in other words)
├── MINIPROPOSAL (group)
│   └── ID (attr)
└── HEADS (group)
    └── 0, 1, 2, ... (groups, indexed)
        ├── DESIGN, SURFACE_SPECIES, PORT_LOCATION (attrs)
        ├── SHOTS (dataset, integer array)
        ├── IMAGES (group, optional)
        │   └── 0, 1, 2, ... (datasets, gzip compressed)
        └── SAMPLES (group)
            └── 0, 1, 2, ... (groups, indexed)
                ├── ID, NAME, POSITION, POSITION_UNITS, COMMENTS (attrs)
                ├── GEOMETRY (group)
                │   ├── SHAPE, SURFACE_PATTERN (attrs)
                │   ├── AREA (dataset + UNIT attr)
                │   └── INCLINATION (dataset + UNIT attr)
                └── MEASUREMENTS (group)
                    ├── PRE_EXPOSURE (group, only if data exists)
                    │   ├── SURFACE_COMPOSITION (group)
                    │   ├── SURFACE_ROUGHNESS (group)
                    │   ├── MORPHOLOGY (group)
                    │   ├── AREAL_DENSITY (group)
                    │   ├── TEMPERATURE (group)
                    │   └── BIAS (group)
                    └── POST_EXPOSURE (group, only if data exists)
                        ├── SURFACE_COMPOSITION (group)
                        ├── SURFACE_ROUGHNESS (group)
                        ├── MORPHOLOGY (group)
                        ├── AREAL_DENSITY (group)
                        ├── TEMPERATURE (group)
                        └── BIAS (group)
```

## Contributing

Contributions are welcome. Please contact the author for guidelines.

## Author

**Luca Cappelli**  
Email: cappellil@fusion.gat.com  
General Atomics - DIII-D National Fusion Facility
