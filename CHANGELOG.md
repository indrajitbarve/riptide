# Changelog
All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).


## 0.1.2 - 2020-04-15

### Fixed
- `Metadata` is now correctly carrying the "tobs" attribute for TimeSeries loaded from SIGPROC data. This was causing a cryptic error when processing SIGPROC dedispersed time series with the pipeline.

### Added
- Can now read and process 8-bit SIGPROC data
- Can now read SIGPROC header keys of boolean type. In particular the "signed" key (which defines the signedness of 8-bit SIGPROC data) is now supported by default.


## 0.1.1 - 2020-04-08

### Fixed
- Module should now properly install on OSX, where some C compilation options had to be adapted. `numpy.ctypeslib` also expects shared libraries to have a `.dylib` extension on OSX rather than the linux standard `.so`


## 0.1.0 - 2020-04-08

**This release contains major improvements and additions but breaks compatibility with** `v0.0.x`. If you have any ongoing large-scale searches or projects, stick with the older release you were using. Other users should **definitely** use this new version.

### Fixed
- Ensure that each subprocess spawned by the pipeline consumes only one CPU, as initially intended to achieve optimal CPU usage. In previous versions, some `numpy` functions would attempt to run on all CPUs at once which was detrimental. As a result the pipeline is now considerably faster.
- The impact of downsampling by a non-integer factor on the noise variance is now correctly dealt with when normalizing the output S/N values. Refer to the paper for details. The difference should be negligible, except when downsampling the original input data by factors close to 1.
- The Makefile used to build the C sources does not explicitly set `CC=gcc` anymore. The default system compiler will now be used instead. `gcc` will be used by default if the environment variable `CC` is undefined.

### Changed
- Clean rewrite of peak detection algorithm. It uses the same method as before, but the arguments of the `find_peaks()` function have changed. See docstring.
- Now using JSON instead of HDF5 as the data product file format. This is vastly easier to maintain and future-proof. Read/write speed and file sizes remain similar.
- Clean rewrite of the pipeline, which has been improved and runs faster, see below for all related changes.
    - Improved DM trial selection, now uses a method similar to PRESTO's `DDPlan` to achieve the least expensive DM space coverage
    - Input de-reddening and normalisation is now common to all search period sub-ranges, further improving pipeline run time
    - Harmonic flagging is now always performed
    - Removing harmonics from the output candidate list is now optional
    - Added option to produce all candidate plots at the end of the pipeline run
    - Candidate plots look nicer
    - Saving candidate files and plots is done with multiple CPUs and runs faster
    - The pipeline configuration file keywords / schema has been adjusted to match all pipeline changes, see example provided and documentation
- `Candidate` class has been refactored, its attribute and method names have changed
- Changed name of high level FFA transforms to `ffa1()` and `ffa2()`. 
- Updated signal generation functions so that the 'amplitude' parameter now represents the expected true signal-to-noise ratio

### Added
- `TimeSeries` now has a `fold()` method that returns a numpy array representing either sub-integrations or a folded profile
- Timing of all pipeline stages
- Dynamic versioning using the `versioneer` module. In python, the current version is accessible via `riptide.__version__`
- Added `ffafreq()` and `ffaprd()` to generate list of FFA transform trial freqs / periods.

### Removed
- Removed the `SubIntegrations` class which added unneeded complexity, sub-integrations are now represented as a 2D numpy array


## 0.0.3 - 2019-11-30
### Added
- Full docstring for `ffa_search()`

### Changed
- Cleaner and faster FFA C kernels, which have been moved to separate files
- Slight optimisation to S/N calculation, where only the best value across all pulse phases is normalized, instead of normalizing at every phase trial
- S/N calculation separated into smaller functions
- Removed OpenMP multithreading from S/N calculation, it was slower in most usual cases. The benefits were visible only for very large input data. As a result, `ffa_search()` does not accept the 'threads' keyword anymore, and the 'threads' keyword has also been removed from the pipeline configuration files (in the 'search' section). Parallelism only happens at the TimeSeries level, i.e. one process per TimeSeries.

### Fixed
- Reinstated the `--fast-math` compilation option that had been accidentally removed in v0.0.2. The code is now much faster.


## 0.0.2 - 2019-11-05
### Added
- `riptide` is now properly packaged with a `setup.py` script. Updated installation instructions in `README.md`
- Updated Dockerfile. Build docker image with `make docker` command.

### Fixed
- When normalising TimeSeries, use a float64 accumulator when calculating mean and variance. This avoid saturation issues on data with high values, e.g. from 8-bit Parkes UWL observations.

### Changed
- Improved candidate plots: docstring for `Candidate` class, DM unit on plots, option to subtract the baseline value of the profile before displaying it, option to plot the profile as either a bar or line chart.

## 0.0.1 - 2018-10-25
### Added
- First stable release of riptide. This is the version that will be run on the LOTAAS survey.
