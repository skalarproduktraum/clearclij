# ClearCLIJ

ClearCLIJ is an ImageJ/Fiji plugin allowing you to run OpenCL code from withing Fijis script editor (e.g. jython). ClearCLIJ is based on [ClearCL](http://github.com/ClearVolume/ClearCL), [FastFuse](https://github.com/ClearControl/FastFuse), [Imglib2](https://github.com/imglib) and [SciJava](https://github.com/SciJava).

Example code:

```
# initialize the GPU context
lCLIJ = ClearCLIJ.getInstance();

# convert ImageJ image to CL images (ready for the GPU)
lInputCLImage = lCLIJ.converter(lImagePlus).getClearCLImage();
lOutputCLImage = lCLIJ.converter(lImagePlus).getClearCLImage(); # copy again to allocate memory for result image

# downsample the image stack using ClearCL / OpenCL
lResultStack = lCLIJ.execute(DownsampleXYbyHalfTask, "kernels/downsampling.cl", "downsample_xy_by_half_nearest", {"src":lInputCLImage, "dst":lOutputCLImage});

# convert the result back to imglib2 and show it
lResultImg = lCLIJ.converter(lResultStack).getRandomAccessibleInterval();
ImageJFunctions.show(lResultImg);
```
More examples can be found in the package clearcl.imagej.demo

## OpenCL Kernel calls with CLIJ.execute()
The execute function asks for four parameters
```
lCLIJ.execute(<Class>, "filename_open.cl", "kernelfunction", {"src":image, "dst":image, "more":5, "evenmore":image})
```
* An optional class file as an anchor to have a point for where to start
  searching for the program file (second parameter).
* The open.cl program file will be searched for in the same folder where the
  class (first parameter) is defined. In the example here, this class
  comes with the dependency FastFuse
* The name of the kernel function defined in the program file
* A dictionary with all the parameters of the kernel function, such as
  "src" and "dst". It is recommended to have at least a "src" and a "dst"
  parameter, because CLIJ derives image data types and global space from
  these parameters.

## Supported / tested platforms
There is a rudimentary list of tests implemented in the clearcl.imagej.test package mainly testing conversion of types between CPU, GPU and JVM. Following OpenCL devices were tested successfully:
* GeForce 940MX (OpenCL 2.0, Feb 2018)
* Intel(R) Core(TM) i7-7500U CPU @ 2.70GHz (OpenCL 1.2, Feb 2018)
* Intel(R) HD Graphics 620 (OpenCL 1.2, Feb 2018)


## Installation

Clone this repo
```
git clone https://github.com/ClearControl/ClearCLIJ
```

Open pom.xml and enter the path of your Fiji installation in the line containing

```
<imagej.app.directory>C:/path/to/Fiji.app
```

Go to the source dir and deploy to your Fiji.app

```
cd ClearCLIJ
deploy.bat
```

Take care: ClearCLIJ is in early developmental stage. Installing it to your Fiji may harm your Fiji installation as it brings dependencies which may be incompatible with other plugins. It is recommended not to work in a production environment.
