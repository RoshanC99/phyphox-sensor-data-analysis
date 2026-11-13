# Sensor Data Analysis

Accelerometer- and gyroscope-based activity analysis of smartphone sensor data collected with the [phyphox](https://phyphox.org/) app. The project walks through the core stages of an Activity Recognition Chain: data acquisition, cleaning, filtering, feature extraction (vector magnitude), and classification (step counting and climbing/walking segmentation).

## Tasks

- **Task 1 — Understand the data.** Compute the accelerometer sampling rate (~99.67 Hz) and its variance, and discuss sampling stability and the Nyquist requirement for capturing walking.
- **Task 2 — Clean the data.** Trim the standing-still/handling segments from each walking recording and save `*_clean.csv` files.
- **Task 3 — Count walking steps.** Take the orientation-independent vector magnitude, smooth it with a moving-average low-pass filter, and count steps by peak detection. Results: 96 and 100 steps vs. a ground truth of 100.
- **Task 4 — Segment and count stair climbing.** Use the gyroscope yaw rate to detect landing turns, remove the walking segments, and count only climbing steps. Result: 58 steps vs. a ground truth of 58.

## Repository layout

```
.
├── SensorDataAnalysisActivity.ipynb   # main notebook (all tasks, with outputs)
├── parser_data.py                     # get_data(): reads a CSV into (time, x, y, z) tuples
├── plot_data.py                       # plot_data(): x/y/z line plot helper
├── constants.py                       # data_folder path
└── data/
    ├── test.csv                       # provided sample
    ├── walking_steps_1.csv            # raw walking, phone in pocket
    ├── walking_steps_2.csv            # raw walking, phone in hand
    ├── walking_steps_1_clean.csv      # cleaned (Task 2)
    ├── walking_steps_2_clean.csv      # cleaned (Task 2)
    ├── stairs_1.csv                   # stair climb — accelerometer
    └── stairs_2.csv                   # stair climb — gyroscope
```

## Running

The notebook was developed in Google Colab. To run locally:

```bash
pip install numpy scipy matplotlib
jupyter notebook SensorDataAnalysisActivity.ipynb
```

Then run the cells top to bottom (skip the first two Colab/Drive-mount cells when running locally; `constants.py` already points at the local `data/` folder).

## Methods

- **Vector magnitude** `sqrt(x² + y² + z²)` collapses the three accelerometer axes into one orientation-independent signal.
- **Moving average** (box-kernel convolution) is used as a low-pass filter to smooth noise before peak detection.
- **Peak detection** (`scipy.signal.find_peaks`) with an adaptive height threshold and a minimum spacing counts steps.
- **Gyroscope yaw** (`stairs_2.csv`, z-axis) locates the ~180° landing turns on a U-shaped staircase; low-rotation stretches are kept as climbing.

## References

- NumPy — [`numpy.convolve`](https://numpy.org/doc/stable/reference/generated/numpy.convolve.html), [`numpy.interp`](https://numpy.org/doc/stable/reference/generated/numpy.interp.html)
- SciPy — [`scipy.signal.find_peaks`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.signal.find_peaks.html), [`scipy.signal.resample`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.signal.resample.html)
- Stisen et al. (2015), *Smart Devices are Different: Assessing and Mitigating Mobile Sensing Heterogeneities for Activity Recognition.*
