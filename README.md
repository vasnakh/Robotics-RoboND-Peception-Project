[//]: # (Image References)

[image1]: ./writeup_imgs/Original_PC_World_3.png
[image2]: ./writeup_imgs/Table_RANSAC_World3.png
[image3]: ./writeup_imgs/Objects_RANSAC_World3.png
[image4]: ./writeup_imgs/EC_World3.png
[image5]: ./writeup_imgs/Detected_Objects_World3.png
[image6]: ./writeup_imgs/Detected_Objects_World3_2.png
[image7]: ./writeup_imgs/Results_World1_1.png
[image8]: ./writeup_imgs/Results_World1_2.png
[image9]: ./writeup_imgs/Results_World2_1.png
[image10]: ./writeup_imgs/Results_World2_2.png
[image11]: ./writeup_imgs/Results_World3_1.png
[image12]: ./writeup_imgs/Results_World3_2.png
[image13]: ./writeup_imgs/Accuracy_SVM.png
[image14]: ./writeup_imgs/Confusion_Matrix_Unnorm.png
[image15]: ./writeup_imgs/Confusion_Matrix_Norm.png
## Project: Perception Pick & Place


---

### Writeup / README


#### 1. Pipeline for filtering and RANSAC plane fitting
The steps taken to have objects and tables in seperate cloud points are as below:

* Downsample the cloud points to have faster processing overall: the LEAF_SIZE is chosen to be `0.005` 
* Passthrough filter over z-axis from `min=0.6` to `max=1.4` to focus on table and objects on the table only
* Passthrough filter over y-axis from `min=-0.4` to `max=0.4` to make sure the area on top of the table is covered only (i.e. workspace on the table)
* Apply outlier filter to remove noise with `mean=5` and `threshold scale factor = 0.05`
* Apply RANSAC Filter to seperate the table from the objects: fit a plane with `max distance = 0.01`

Below is the original image of the table on world #3 of the simulation:
![alt text][image1]
Now after applying all the steps above the table is separated as below picture:
![alt text][image2]
And finally the objects after applying RANSAC are shown below:
![alt text][image3]
#### 2. Pipeline including clustering for segmentation 
The steps to segment each objec is as follows:

* First convert RGBXYZ to XYZ only creating white cloud
* Then make kd-tree from the white cloud for search method when apply Euclidean Clustering in next step
* Apply Euclidean Clustering with `cluster tolerance = 0.05`, `min cluster size = 10`, and `max cluster size = 10000`

The image below shows the result after applying Euclidean Clustering on the objects:
![alt text][image4]
#### 3. Features extraction plus SVM for Object Recognition
To extract features two histograms are created: one for colors (in HUE color space to be more robust) and one for surface normals. Number of bins for the histograms are 32.

Linear SVM is used for training and for each object 25 samples with random distance and orientation is generated to make accuracy higher.

Here are the results for world 3:
![alt text][image13]
![alt text][image14]
![alt text][image15]

Below are the results of detecting objects after SVM is trained:
![alt text][image5]
![alt text][image6]


#### 4. Reading respective pick-list for each world scene and constructing the messages that would comprise a valid Pick/Place request buy outputting them to .yaml file format for each.
**World 1:**

100% of objects from pick-list are detected as shown below:
![alt text][image7]
![alt text][image8]

output_1.yaml contents are:

```
object_list:
- arm_name: right
  object_name: biscuits
  pick_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0.5410418510437012
      y: -0.24128852784633636
      z: 0.7051087021827698
  place_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0
      y: -0.71
      z: 0.605
  test_scene_num: 1
- arm_name: right
  object_name: soap
  pick_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0.544619619846344
      y: -0.019259437918663025
      z: 0.6774629950523376
  place_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0
      y: -0.71
      z: 0.605
  test_scene_num: 1
- arm_name: left
  object_name: soap2
  pick_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0.4448191523551941
      y: 0.22048234939575195
      z: 0.6762260794639587
  place_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0
      y: 0.71
      z: 0.605
  test_scene_num: 1

```

**World 2:**

100% of objects from pick-list are detected as shown below: 
![alt text][image9]
![alt text][image10]

output_2.yaml contents are:

```
object_list:
- arm_name: right
  object_name: biscuits
  pick_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0.570633590221405
      y: -0.24795736372470856
      z: 0.7049769163131714
  place_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0
      y: -0.71
      z: 0.605
  test_scene_num: 2
- arm_name: right
  object_name: soap
  pick_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0.5594627857208252
      y: 0.003025622805580497
      z: 0.6767105460166931
  place_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0
      y: -0.71
      z: 0.605
  test_scene_num: 2
- arm_name: left
  object_name: book
  pick_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0.5782880187034607
      y: 0.28100520372390747
      z: 0.7237452268600464
  place_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0
      y: 0.71
      z: 0.605
  test_scene_num: 2
- arm_name: left
  object_name: soap2
  pick_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0.4443254768848419
      y: 0.22581817209720612
      z: 0.6763569712638855
  place_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0
      y: 0.71
      z: 0.605
  test_scene_num: 2
- arm_name: left
  object_name: glue
  pick_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0.630977988243103
      y: 0.13042046129703522
      z: 0.6793605089187622
  place_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0
      y: 0.71
      z: 0.605
  test_scene_num: 2

```

**World 3:**

100% of objects from pick-list are detected as shown below: 
![alt text][image11]
![alt text][image12]

output_3.yaml contents are:

```
object_list:
- arm_name: left
  object_name: sticky_notes
  pick_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0.43937948346138
      y: 0.214384987950325
      z: 0.6871898174285889
  place_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0
      y: 0.71
      z: 0.605
  test_scene_num: 3
- arm_name: left
  object_name: book
  pick_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0.49141329526901245
      y: 0.08361513167619705
      z: 0.7280077338218689
  place_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0
      y: 0.71
      z: 0.605
  test_scene_num: 3
- arm_name: right
  object_name: snacks
  pick_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0.4240442216396332
      y: -0.3240757882595062
      z: 0.7548038363456726
  place_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0
      y: -0.71
      z: 0.605
  test_scene_num: 3
- arm_name: right
  object_name: biscuits
  pick_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0.587968111038208
      y: -0.21902433037757874
      z: 0.7055451273918152
  place_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0
      y: -0.71
      z: 0.605
  test_scene_num: 3
- arm_name: left
  object_name: eraser
  pick_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0.6079831719398499
      y: 0.28160324692726135
      z: 0.6473881006240845
  place_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0
      y: 0.71
      z: 0.605
  test_scene_num: 3
- arm_name: right
  object_name: soap2
  pick_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0.453364759683609
      y: -0.04272250086069107
      z: 0.6772201061248779
  place_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0
      y: -0.71
      z: 0.605
  test_scene_num: 3
- arm_name: right
  object_name: soap
  pick_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0.6790770292282104
      y: 0.003934708423912525
      z: 0.6765380501747131
  place_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0
      y: -0.71
      z: 0.605
  test_scene_num: 3
- arm_name: left
  object_name: glue
  pick_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0.6134567856788635
      y: 0.13929609954357147
      z: 0.6864029169082642
  place_pose:
    orientation:
      w: 0.0
      x: 0.0
      y: 0.0
      z: 0.0
    position:
      x: 0
      y: 0.71
      z: 0.605
  test_scene_num: 3

```

#### 5. Discussion and further possible improvements

Actual pick and placing did a very poor job therefore I have commented out that section in the code: to improve that, collision map needs to be produced and also the objects need to be picked up in a right manner so the other objects that are close to it don't move or drop to floor. Also stacking the objects in a box is not a really good idea since as it gets full then the rest of the objects drop outside the box. The path planning itlself also needs to be optimal and robust which seems not to be sort of (not sure whether it's due to my computational power being poor or it is actually performing a bit poor in general). Also trainig SVM can be further improved by using more data along with more features to have better accuracy. Fortunately even though the accuracy was not really high for world 3, still the results were pretty good! 
