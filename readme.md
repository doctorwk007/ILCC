# 3D-LiDAR and panoramic camera 
<!-- based on reflectance intensity of the laser -->

[![License](https://img.shields.io/badge/license-BSD-blue.svg)](LICENSE)<br>
paper (available soon)
<!-- [[paper]](http://www.mdpi.com/journal/remotesensing)-->
## Dependencies
* Python >= 2.7.9
* [OpenCV](http://opencv.org/)
* [OpenGV](https://laurentkneip.github.io/opengv/page_installation.html)
* [Point Cloud Library (PCL)](http://pointclouds.org/)
* [PCL python bindings](<https://github.com/strawlab/python-pcl>)
<!-- * Other python packages: pip install -r [requirements.txt](requirements.txt) -->


## Optional
* [MATLAB engine for Python](https://www.mathworks.com/help/matlab/matlab_external/install-the-matlab-engine-for-python.html): Corner detection from images with MATLAB
* [VTK](https://github.com/Kitware/VTK) >=7.0: 3D Visualization


## Usage
### Installation
```
git clone https://github.com/mfxox/ILCC
cd ILCC
python setup.py install
```


### Explanation of files
```config.py```: parameter settings <br>
<!-- ``` main.py```:  call other functions to process from the the corner detection of image and LiDAR to optimization for the final result <br>
 -->```img_corners_est.py```: estimate corners of chessboard from images with OpenCV or MATLAB<br>
```pcd_corners_est.py```: estimate corners of chessboard from the point cloud<br>
```LM_opt.py```: load corresponding 2D-3D corners, calculate initial values with the PnP method, refine the result with LM method
```utility.py```: utility functions for various of visualization


### Process data
1. Make a folder for example named as __DATA__ and make the image and point cloud folders __DATA/img__ and __DATA/pcd__ respectively. 
1. Put panoramic images into  __DATA/img__ and point cloud files into  __DATA/pcd__. The files should be named like 00XX.png or 00XX.csv.
1. Copy config.yaml to __DATA__ and modify config.yaml according to your situation.
1. ``` cd DATA ```
1. Corner detection from images.<br>
    ```python
    from ILCC import img_corners_est
    ``` <br>
    ```python
    img_corners_est.detect_img_corners()
    ```<br>
    Coordinates of corners from images are saved to __DATA/output/img_corners__ with the filename *00XX_img_corners.txt* and images with marked corners are saved in the same folder with the file name *00XX_detected_corners.jpg* if _'output_img_with_dectected_corners'_ in ```config.yaml``` is set to __True__ .
1. Corner detection from point clouds.<br>
    ```python
    from ILCC import pcd_corners_est
    ``` <br>
    ```python
    pcd_corners_est.detect_pcd_corners()
    ```<br>
    Coordinates of corners from point clouds are save to __output/pcd_seg__ with the filename *00XX_pcd_result.pkl*.  Segments of each point cloud are output to __/DATA/output/pcd_seg/00XX__.
1. Non-linear optimization for final extrinsic parameters.<br>
    ```python 
    from ILCC import LM_opt
    ``` <br>
    ```python
    LM_opt.cal_ext_paras()
    ```<br>
    The extrinsic calibration results are output in the end of the process and saved with the filename *YYYYMMDD_HHMMSS_calir_result.txt*.  Images of back-projected 3D points with the calculated parameters are saved to __DATA/output__ if 'back_proj_corners' is set to **True**. 
1. After the aforementioned process, utility module can be imported for visualizing various of results. <br>
    ```python
    from ILCC import utility
    ``` <br>
    See the example below for how to use.


## Example
### Sample Data
The sample data and processing results of detected corners can be downloaded from [here](https://www.dropbox.com/s/m0ogerftqav0fyx/ILCC_sample_data_and_result.zip?dl=0) (185.6M). <br> These data are acquired with the [chessboard file](readme_files/chessboard_A0_0.75_6_8.pdf) which contains 6*8 patterns and the length of one grid is 7.5cm if it is printed by A0 size.
### Process
```
wget https://www.dropbox.com/s/m0ogerftqav0fyx/ILCC_sample_data_and_result.zip
unzip ILCC_sample_data_and_result.zip
cd ILCC_sample_data_and_result
```
copy ```config.yaml``` to __ILCC_sample_data_and_result__ folder.


### Visualization ([VTK](https://github.com/Kitware/VTK) >=7.0 is necessary)
* visualization of the point cloud from .csv file
```python
    from ILCC import utility
    utility.vis_csv_pcd(ind=1)
```
<div style="text-align: center">
<img src="readme_files/vis_csv.png" width = "50%" />
</div>

* visualization of the segmented results
```
    from ILCC import utility
    utility.vis_segments(ind=1)
```
<div style="text-align: center">
<img src="readme_files/vis_seg.png" width = "50%" />
</div>

* visualization of the detected point cloud segment of the chessboard
```
    from ILCC import utility
    utility.vis_segments_only_chessboard_color(ind=1)
```
<div style="text-align: center">
<img src="readme_files/vis_chessboard_only.png" width = "50%" />
</div>

* visualization of the detected point cloud segment of the chessboard and the estimated chessboard model
```
    from ILCC import utility
    utility.vis_ested_pcd_corners(ind=1)
```
<div style="text-align: center">
<img src="readme_files/vis_est_marker.png" width = "50%" />
</div>

* visualization of all detected chessboards
```
    import utility
    utility.vis_all_markers(utility.vis_all_markers(np.arange(1, 21).tolist())
```
<div style="text-align: center">
<img src="readme_files/all_frames_side.png" width = "60%" />
<img src="readme_files/all_frames_top.png" width = "65%" />
</div>




<!-- ## To do
Integration for ROS -->

