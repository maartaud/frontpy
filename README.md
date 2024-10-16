# FrontPy

FrontPy is a Python package designed for the detection and analysis of atmospheric fronts. It uses the Thermal Front Parameter (TFP) method to identify cold and warm fronts. The package also includes visualization capabilities, allowing users to plot the identified fronts over GOES-16 East satellite imagery.

## Installation of FrontPy

### Step 1: Create a new Conda environment

First, you should create a new environment with the specific version of Python and some necessary dependencies. Run the following command in your terminal or command prompt:

```bash
conda create -n your_environment_name python=3.9.15 gdal=3.3.3 poppler=21.09 -c conda-forge
```

Replace **your_environment_name** with your desired name for the environment. The conda will create an isolated environment with Python 3.9.15 and the gdal and poppler libraries, which are necessary for the functionality of FrontPy. Now, FrontPy will probably run smoothly without any errors.

### Step 2: Activate your new Conda environment

Once the environment is created, activate it using the command:

```bash
conda activate your_environment_name
```

### Step 3: Install FrontPy using pip

Now that you are in an isolated environment, install FrontPy using pip:

```bash
pip install frontpy
```

## Usage

To use FrontPy, follow these steps:

**Step 1: Open an .py file or a Jupyter Notebook and import the main and plot_results functions from the package**:

```python
from frontpy import main, plot_results
```

**Step 2: Set up the parameters required for downloading the data, calculating the Thermal Front Parameter (TFP), detecting cold and warm fronts, and final visualization**. 

The parameters must be provided in a dictionary, like this:

```python
config = {
    "start_date": "2024-10-11 12",  # Start date and time for the analysis (format: YYYY-MM-DD HH) - string
    "end_date": "2024-10-12 12",    # End date and time for the analysis (format: YYYY-MM-DD HH) - string
    "lat_max": 20,                  # Maximum latitude for the analysis area (degrees, range: -90 to 90) - float
    "lat_min": -60,                 # Minimum latitude for the analysis area (degrees, range: -90 to 90) - float
    "lon_max": -20,                 # Maximum longitude for the analysis area (degrees, range: -180 to 180) - float
    "lon_min": -90,                 # Minimum longitude for the analysis area (degrees, range: -180 to 180) - float
    "model_name": None,            # Name of the dataset to be used. (Options available: ERA5, GFS). Leave it unchanged if you want to use your own files - string
    "filepath": "path_to_your_netcdf_file", #Path to the local NetCDF file containing both u, v, specific humidity and temperature (required if model_name is None)
    "pressure": 850,                # [Default: 850] Atmospheric pressure level (hPa) at which front identification is performed - int
    "thetaw_thresh": 3.0,           # [Default: 3.0] Threshold for the magnitude of the gradient of potential wet-bulb temperature (K/100km) for front identification -  float
    "vf_thresh": 1.0,               # [Default: 1.0] Threshold for wind velocity (m/s) for front classification as cold or warm front - float
    "smooth_sigma": 0.5,            # [Default: 0.5] Smoothing parameter (sigma) for Gaussian filtering - float
    "line_or_area": "area",         # Type of representation for the fronts (line or area) - string
    "min_points": 4,                # [Default: 4] Minimum number of frontal points required for a valid frontal line - int
    "min_length": 500.0,            # [Default: 500.0] Minimum length (km) for the frontal line to be considered - float
    "min_area": 5000.0,             # [Default: 5000.0] Minimum area (km²) for the frontal zone to be considered - float
    "frame_rate": 3.0,              # [Default: 3.0] Frame rate (frames per second) for the generated animation; higher rates result in a faster animation - float
    "output_directory_fronts": "path_to_your_chosen_output_directory"   # Path to your output directory
}
```

Currently, FrontPy allows you to automatically download ERA5 and GFS data (u, v, temperature, specific humidity) for calculating TFP and identifying fronts. To do this, enter "ERA5" or "GFS" in the **model_name** parameter. 

If you want to use your own files, all variables must be contained in one netcdf file. In this case, do not assign any value for the **model_name** parameter. Instead, you must provide the path to the netcdf file in the **filepath** parameter, which should include u, v, specific humidity and temperature.

**Note:** *The default values have been tested only at the 850 hPa level over the the Southern Hemisphere and may need adjustments depending on the dataset and data resolution.*

**Step 3: Call the main function**:

The *main* function will return two dataframes: one for cold fronts and one for warm fronts. Also, it will save these dataframes as CSV files inside the output directory, as well as the corresponding TFP files.

So, you can call the main function like this:

```python
cold_fronts, warm_fronts = main(config)
```

**Step 4: Plot the images and generate an animation**:

Lastly, you can plot the results and generate an animation (GIF) using the **plot_results** function. The images and animation will be saved in your output directory.

```    
plot_results(cold_fronts, warm_fronts, config)
```

**Note**: *For now, it is only possible to generate images within the coverage area and the available data period of GOES-16 East as provided by AWS.*

### Command Line Interface (CLI)

FrontPy also supports command-line execution through the **cli.py** file and the **fronts** function. This allows users to specify the parameters directly from the command line.

To run FrontPy using the CLI, execute the following command in your terminal (without the <>):

```bash
fronts <start_date> <end_date> <lat_max> <lat_min> <lon_max> <lon_min> <line_or_area> <output_directory_fronts> <mode>
```

The above arguments are mandatory and must be provided in string format (in quotes) and in the specified order.

**Mode Argument Explanation:**

- To only **identify and generate the files and dataframes** relative to the TFP field and to cold and warm fronts, type **"identify"** as the `<mode>` argument.

- To only **create the images and animation** based on the files and dataframes that have already been generated, type **"plot"** as the `<mode>` argument.

- To **perform both actions** type **"both"** as the `<mode>` argument.

Optional arguments can also be included using the following prefixes before the values of your choice:

- `--model-name`
- `--filepath`
- `--pressure`
- `--thetaw-thresh`
- `--vf-thresh`
- `--smooth-sigma`
- `--min-points`
- `--min-length`
- `--min-area`
- `--frame-rate`

#### Real example using CLI:

1. Using GFS data to identify and plot the results:

```bash
fronts "2024-10-10 00" "2024-10-11 00" "20" "-60" "-20" "-90" "area" "/output_directory/" "both" --model-name "GFS"
```

2. Using my own data:

```bash
fronts "2024-10-10 00" "2024-10-11 00" "20" "-60" "-20" "-90" "area" "/output_directory/" "both" --filepath "/path_to_my_netcdf_file.nc"
```

*That is it. Have fun working with all the different combinations of parameters and values!*

## Contributing

Contributions are welcome! If you find any issues or have suggestions for improvements, please open an issue or submit a pull request.

## License

This project is licensed under the Apache License 2.0. See the LICENSE file for more details.