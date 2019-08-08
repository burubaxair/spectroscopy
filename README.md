# Spectroscopy
Scripts for light spectroscopy

[AS7265x_plot.py](https://github.com/burubaxair/spectroscopy/blob/master/AS7265x_plot.py) - a python script for plotting light spectra obtained from the Sparkfun's [AS7265x spectrophotometer](https://www.sparkfun.com/products/15050).

[AS7265x_animate.py](https://github.com/burubaxair/spectroscopy/blob/master/AS7265x_animate.py) - Similar to [AS7265x_plot.py](https://github.com/burubaxair/spectroscopy/blob/master/AS7265x_plot.py) but it reads the input from the serial port, updates the figure in real time, saves the measurements into a log file, and saves the figure when the Space key is pressed. 

## Details of the AS7265x_plot.py

`measurements` - the array of measurements from the sensors. Copy the measurements (e.g. from the Arduino serial monitor) to this array.

I sort the measurements in the order of increasing wavelengths and put the result into the array called `data`. This is not required for interpolation but might be needed for further processing.

The measured signal is interpolated using radial basis functions from [scipy](https://docs.scipy.org/doc/scipy/reference/generated/scipy.interpolate.Rbf.html). I tried different functions and found that the multiquadric function works best provided that the following corrections are made:

* When the amplitude of the signal is negative it is put to zero.
* The amplitude of the signal at the wavelengths below the lowest channel (410 nm) and above the largest channel (940nm) is a Gaussian function with the FWHM=20nm according to the sensors' [specifications](https://cdn.sparkfun.com/assets/learn_tutorials/8/3/0/AS7265x_Datasheet.pdf).

You can see, why these corrections are needed by testing them when the signal is a single pulse,

```python
A = np.zeros(18)
A[10] = 1
```

or a train of pulses with increasing amplitudes,

```python
A = np.arange(1,19)
```

## Examples of spectra plotted with AS7265x_plot.py
### Red LED
![](/images/AS7265x_RED.png)
### Orange LED
![](/images/AS7265x_ORANGE.png)
### Blue LED
![](/images/AS7265x_BLUE.png)
### White LED
![](/images/AS7265x_WHITE.png)
### Infrared LED from a remote control
![](/images/AS7265x_IR.png)

## Details of the AS7265x_animate.py

No need to manually copy the measurements from the Arduino serial monitor. You don't even have to start Arduino serial monitor.

Specify the serial port, the name of the log file and the folder where to save the figures

```python
ser = serial.Serial('COM3',9600)
logfile = 'log.txt'
figdir = 'tst' # folder to save figures
```

In the Arduino sketch, comment or remove the lines

```C++
//Serial.println("AS7265x Spectral Triad");

//Serial.println("A,B,C,D,E,F,G,H,I,J,K,L,R,S,T,U,V,W");
```
so that only the numeric arrays of measurements are sent to the serial port.
