#**Finding Lane Lines on the Road** 
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

<img src="laneLines_thirdPass.jpg" width="480" alt="Combined Image" />

Overview
---

When we drive, we use our eyes to decide where to go.  The lines on the road that show us where the lanes are act as our constant reference for where to steer the vehicle.  Naturally, one of the first things we would like to do in developing a self-driving car is to automatically detect lane lines using an algorithm.

The Project
---

## If you have already installed the [CarND Term1 Starter Kit](https://github.com/udacity/CarND-Term1-Starter-Kit/blob/master/README.md) you should be good to go!   If not, you can install the starter kit or follow the install instructions below to get started on this project. ##

**Step 1:** Getting setup with Python

To do this project, you will need Python 3 along with the numpy, matplotlib, and OpenCV libraries, as well as Jupyter Notebook installed. 

We recommend downloading and installing the Anaconda Python 3 distribution from Continuum Analytics because it comes prepackaged with many of the Python dependencies you will need for this and future projects, makes it easy to install OpenCV, and includes Jupyter Notebook.  Beyond that, it is one of the most common Python distributions used in data analytics and machine learning, so a great choice if you're getting started in the field.

Choose the appropriate Python 3 Anaconda install package for your operating system <A HREF="https://www.continuum.io/downloads" target="_blank">here</A>.   Download and install the package.

If you already have Anaconda for Python 2 installed, you can create a separate environment for Python 3 and all the appropriate dependencies with the following command:

`>  conda create --name=yourNewEnvironment python=3 anaconda`

`>  source activate yourNewEnvironment`

**Step 2:** Installing OpenCV

Once you have Anaconda installed, first double check you are in your Python 3 environment:

`>python`    
`Python 3.5.2 |Anaconda 4.1.1 (x86_64)| (default, Jul  2 2016, 17:52:12)`  
`[GCC 4.2.1 Compatible Apple LLVM 4.2 (clang-425.0.28)] on darwin`  
`Type "help", "copyright", "credits" or "license" for more information.`  
`>>>`   
(Ctrl-d to exit Python)

run the following commands at the terminal prompt to get OpenCV:

`> pip install pillow`  
`> conda install -c menpo opencv3=3.1.0`

then to test if OpenCV is installed correctly:

`> python`  
`>>> import cv2`  
`>>>`  (i.e. did not get an ImportError)

(Ctrl-d to exit Python)

**Step 3:** Installing moviepy  

We recommend the "moviepy" package for processing video in this project (though you're welcome to use other packages if you prefer).  

To install moviepy run:

`>pip install moviepy`  

and check that the install worked:

`>python`  
`>>>import moviepy`  
`>>>`  (i.e. did not get an ImportError)

(Ctrl-d to exit Python)

**Step 4:** Opening the code in a Jupyter Notebook

You will complete this project in a Jupyter notebook.  If you are unfamiliar with Jupyter Notebooks, check out <A HREF="https://www.packtpub.com/books/content/basics-jupyter-notebook-and-python" target="_blank">Cyrille Rossant's Basics of Jupyter Notebook and Python</A> to get started.

Jupyter is an ipython notebook where you can run blocks of code and see results interactively.  All the code for this project is contained in a Jupyter notebook. To start Jupyter in your browser, run the following command at the terminal prompt (be sure you're in your Python 3 environment!):

`> jupyter notebook`

A browser window will appear showing the contents of the current directory.  Click on the file called "P1.ipynb".  Another browser window will appear displaying the notebook.  Follow the instructions in the notebook to complete the project.  

**Step 5:** Complete the project and submit both the Ipython notebook and the project writeup

Description
---

**Finding Lane Lines on the Road**


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

[1_raw]: ./writeup/1_raw.png "Raw"
[2_regionmask]: ./writeup/2_regionmask.png "Region Mask"
[3_colormask]: ./writeup/3_colormask.png "Color Mask"
[4_edges]: ./writeup/4_edges.png "Edges"
[5_lines_hough]: ./writeup/5_lines_hough.png "Lines Hough"
[5_lines_extrapolated]: ./writeup/5_lines_extrapolated.png "Lines Extrapolated"
---

### Reflection



###1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 4 steps: applying a region mask, applying a color mask, detecting edges, and then detecting lines.

![alt text][1_raw]

1) A region mask insures that we do not include erroneous measurements that bias our line estimates. By doing this step early, we can potentially avoid spending compute power on regions that will not be considered. Keep in mind that we are assuming that all essential road markings can be found within a triangular polygon:

![alt text][2_regionmask]

2) A color mask assumes that road lines are either white or yellow. Of course, thresholds were chosen from examples with fairly consistent and bright lighting conditions.

![alt text][3_colormask]

3) Edge detection assumes that road marking have high constrast with their surroundings. This allows us to use the Canny edge detector (which uses gradients along both x and y directions). Notice that the detector identifies PIXELS with high contrast (as opposed to line segments, which is what we actually want).

![alt text][4_edges]

4) Line detection looks for groups of pixels that agree on the line. Of course, an inherent assumption is that road markings can be modeled as a line.

The result of the hough transform groups neighborhoods of previously identified edges. Unfortunately, these do not fully capture the boundaries in which the car should remain.

![alt text][5_lines_hough]

To identify the lane boundaries, we extrapolate the hough lines by separating the identifies line segments into two sets: left and right lane. These are identified by grouping lines with similar slopes (within some threshold -- to eliminate erroneous cars that also happen to be white). For each group of lines, we fit points of the line segments to a line along the boundaries of the image (from bottom to middle of the image).

![alt text][5_lines_extrapolated]



###2. Identify potential shortcomings with your current pipeline

There are a lot of assumptions mentioned above

1) Reliance on road markings within a particular region

2) Assuming certain types of lighting conditions due to fixed RGB thresholds for color masking

3) Lanes must have high contrast with the road so dirt roads would be a problem

4) Lanes are lines without curves


###3. Suggest possible improvements to your pipeline

1) Adaptive region boundaries

2) Adaptive lighting thresholds

3) Utilize scene understanding

4) Fit lanes to a polynomial instead of a linear function

