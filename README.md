# LAOS data analysis
Quite note: this app only supports JSON data file exported via TA TRIOS software Version 5.8.0.38.
## Introduction
In the large amplitude oscillatory shear (LAOS) experiment, the material is deformed in its nonlinear viscoelastic regime. The information of nonlinear viscoelasticity can be extracted from the higher harmonic tones of the Fourier transform of the response. To date, different parameters have been proposed to characterize many aspects of the nonlinear viscoelasticity under LAOS; among them widely used are

* the related intensity of higher harmonics, *I*<sub>n/1</sub>,
* the shape parameters of the Lissajous curve, *G*<sub>M</sub>, *G*<sub>L</sub> and *G*<sub>k</sub>.

For their definition and more detailed information of the LAOS rheology, please refer to the review by Hyun et al.[^1]

R. Ewoldt et al. have written the [MITlaos](http://web.mit.edu/nnf/research/phenomena/mit_laos.html) code specifically for the analysis of Lissajous curves and the Chebyshev coefficients. The code calculates the Lissajous parameters (alternative moduli) from the Fourier transformed harmonics instead of finding the slope of the curve as the definition of the parameters. The code is not compiled. My motivation is to design another tool for LAOS data analysis that

* complements the functions of MITlaos,
* allows the user to collect a series of results, e.g. a strain sweep test series, and
* provides a new GUI in the form of MATLAB App.

## Technical features
### JSON data support
The app handles (to some extent) the JSON data format exported by TRIOS software. It can list all result steps in one JSON (the TRIOS software always exports all steps of an experiment into one JSON file). It can recognize the units of strain and stress data and display them accordingly.

### Coherent sampling
To minimize spectral leakage, coherent sampling is done before the Fourier transform. To do this, the accurate strain frequency is obtained first by sine fitting following IEEE Standard 1057.

### Signal reconstruction
Parameters defined via Lissajous curve involve differentiating the latter. These are performed on the reconstructed signal. The reconstructed signal is the sum of the odd harmonic components analyzed by the Fourier transform. This is based on knowledge of LAOS that the shear stress of an ideal experiment should be a sum of only the odd harmonics. So, the reconstructed signal is free from noise, bias, or non-ideal factors.

## Installation
Unzip and drag the `.mlappinstall` file to the command window of MATLAB. After installation it will appear in the app list.

## Usage
Click `Open...` and open the JSON file. The data file is often quite large, so the parsing process takes a long time. A dialog pops out to indicate the status. When the process is complete, you must click `Close` to continue. The dialog will indicate if any errors occured during parsing the JSON file.

If the parsing process is successful, the file name will show next to the `Open...` button, and the result steps will be listed in the list box under `Result Steps:`. If the first result step is a suitable LAOS experiment, the wave data will be plotted on the right. Otherwise, the plot is empty except for a line saying, "No suitable data for analysis." You can select different result steps in the list box.

Select the variable (Strain or Stress) to plot with the `Var to plot` option buttons. Please note the variables and the units in the plot.

A time range is always set on the raw waves for further analysis. By default, full time range is set. To change the time range, click the button `select 1st/last point` once, and the mouse will become a cross. Click once on the intended position in the plot and the 1st/last point of the time range will be updated. The current time range is indicated by black dash lines in the plot. Time range selection is useful if your stress wave becomes stationary after several cycles. You want to select the stationary portion for analysis. Also, if there are too many cycles, the strain signal may be difficult to analyze. In this case, choosing fewer cycles might help. Click `Full range` to set the time range to full again. The time span you set for a step will be remembered.

Click `Analysis` for LAOS analysis. If the "Bad strain data" error pops up, one thing you can check is if your time range contains less than one cycle. If the error keeps popping out, please send me the JSON data for debugging.

If the strain data is good, the analysis will proceed. The raw and reconstructed Lissajous curve of the current data will be shown in the `Lissajous curve` plot. If the reconstructed curve briefly reproduces the raw curve, then the results of this analysis are reliable. You may consider appending the LAOS parameters to the table by clicking `Append to table`, or exporting the reconstructed Lissajous data by clicking `Export Lissajous`.

The nonlinear viscoelastic parameters provided by the tool are shown in the header row of the table: The strain amplitude and strain frequency are estimated from the strain signal. The sampling frequency is that of the raw signals. *G*<sub>1</sub>′ and *G*<sub>1</sub>″ are the real and imaginary moduli of the first harmonic response. They reduce to the storage and loss modulus at small strain amplitudes. The Lissajous parameters *G*<sub>M</sub>, *G*<sub>L</sub> and *G*<sub>k</sub> are calculated by slope finding on the reconstructed signal.[^1] The slope finding is performed for all cycles and the results are averaged. These maximize the signal-to-noise ratio (SNR) of the results. The *N*<sub>E</sub> parameter was suggested by the author.[^2] The last 4 parameters are the signal-to-noise ratios of the 3rd, 5th, 7th, and 9th harmonics in dB. Typically, when and only when SNR<sub>n</sub> ≳ 6 dB do you need to pay attention to the n-th harmonics.

The rows of the table can be multi-selected. Click `Delete row(s)` to delete the current selected row(s).  Click `Clear table` to clean all the data. You can copy the data from the table by keyboard shortcut `Ctrl_C` and paste to other software for further plotting. Carefully choosing the result steps and appending the LAOS parameters to the table, you can construct a table of strain sweep, frequency sweep or both for Pipkin diagram.

If you click the `Export Lissajous` button, a text based data file will be saved under the same path of the JSON file. The file name consists of that of the JSON file, the name of the current result step, and the string "Lissajous". In the beginning of the file, the strain amplitude, strain frequency, and the variables of the 4 columns of the rest of data will be indicated. Please take care of these lines and import the actual 4-column data after them in your software.

If you click the `Do all` button, all steps listed will be automatically processed. Each of them will be analyzed based on the selected range of time span. The data will be appended to the table. Lissajous curves will also be exported.

## Typical LAOS experiment
On TRIOS software, multiple oscillatory shear steps can be appended to the same procedure of an experiment. It is the author's opinion that a complete analysis of the nonlinear viscoelasticity of a complex fluid requires testing over a Pipkin's space, i.e. ranges of both strain amplitude and frequency. So multiple oscillatory frequency sweeps of different strain amplitudes, or multiple oscillatory strain sweeps of different frequencies should be set of a procedure of an experiment. Take note of which step has what strain amplitude and frequency, since after loading the JSON to the app, the list box only lists the step names.

To save time and avoid irreversible structural change, I recommend setting several oscillatory frequency sweep steps of increasing strain amplitudes, each of which is a sweep from high frequency to low frequency. 

Please make sure the number of cycles of each step is large enough for the response to be stationary for quite a few cycles. Also, take care of the points per cycle setting; let it be orders-of-magnitude larger than is required by the [Nyquist--Shannon sampling theorem](https://en.wikipedia.org/wiki/Nyquist%E2%80%93Shannon_sampling_theorem). 

An example [JSON file (351 MB)](https://www.andrewsun.net/wp-content/uploads/example.json) and its corresponding [`.tri` file (9.22 MB)](https://www.andrewsun.net/wp-content/uploads/example.tri) are provided for testing the app.

Cite this repo: [![DOI](https://zenodo.org/badge/919745935.svg)](https://doi.org/10.5281/zenodo.15430367)

[^1]: K. Hyun, M. Wilhelm, C.O. Klein, K.S. Cho, J.G. Nam, K.H. Ahn, S.J. Lee, R.H. Ewoldt, and G.H. McKinley, "A review of nonlinear oscillatory shear tests: Analysis and application of large amplitude oscillatory shear (LAOS)", Progress in Polymer Science, vol. 36, pp. 1697-1753, 2011.
[^2]: W. Sun, Y. Yang, T. Wang, X. Liu, C. Wang, and Z. Tong, "Large amplitude oscillatory shear rheology for nonlinear viscoelasticity in hectorite suspensions containing poly(ethylene glycol)", _Polymer_, vol. 52, pp. 1402-1409, 2011.
