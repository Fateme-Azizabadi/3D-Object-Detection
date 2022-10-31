# **3D Object Detection**

We want to get 3D BBX from only one image in this project.

In this project, we only used the images of a regular camera, the same RGB images that the camera gives us.
Lidar information is usually used to detect 3D objects, but Lidar information has a large volume; for example, in Kiti Dataset, the Lidar size is almost twice the size of images. This process is difficult and time-consuming. In addition to this, lidar has a high cost compared to using only one camera.


The study of this method started in 2016 with [this article](https://ieeexplore.ieee.org/abstract/document/7780605). The basis of this method is to try to perform a 3D object detection from 2D object detection.

## **Method used**

### **3D Bounding Box**

A 3D bbx is defined by its center, dimensions, and direction.
![](https://github.com/Fateme-Azizabadi/3D-Object-Detection/blob/main/Images/3D.Bounding.Box.png)


In this method, we tried to get the dimensions and direction simply with the help of regression. Then we make sure that these dimensions fit perfectly in the two-dimensional window.

![](https://github.com/Fateme-Azizabadi/3D-Object-Detection/blob/main/Images/2D.Constraint.png)

### **Choice of Regression Parameters**

To find the dimensions, we can apply regression on the center of the box or use the dimensions themselves.
We decided to use the dimensions themselves because the variance is much lower. Because: 
* Usually, all cars have the same dimensions. 
* The dimensions depend a lot on the car type, and the objects' labels are also available, so we can quickly check whether the obtained dimensions are reasonable or not.

### **2D Constraint** 

The constraint that the 3D bounding box fits tightly into the 2D detection window requires that each side of the 2D bounding box be touched by the projection of at least one of the 3D box corners.
 We have eight corners and four sides, so we have to check 8 to the power of 4. These calculations are done in parallel and do not cause a problem in terms of time.

![](https://github.com/Fateme-Azizabadi/3D-Object-Detection/blob/main/Images/2D.Constraint.png)

## **Orientation  Module** 

We can show the direction with R(theta, phi, alpha), but since the objects are parallel to the road surface, we can consider phi and theta zero.
Here we have two directions, global direction (red color) and local direction (blue angle).

![](https://github.com/Fateme-Azizabadi/3D-Object-Detection/blob/main/Images/Orientation.Module.png)

The global direction as the car moves, its direction remains almost constant relative to the camera position.
For example, in the picture below, this direction is to the right for all cars. However, the local direction changes with the car's dimensions in the photo, so it is better to use this angle to get the global angle, which is our primary goal. That is, consider the local angle that changes with the change of car dimensions as the input of the regression, and then we obtain the global angle from the local angle.
![](https://github.com/Fateme-Azizabadi/3D-Object-Detection/blob/main/Images/Orientation..Module.png)

### **Loss**
For loss, we used L2, where the value d is accurate dimensions, and the d-bar is average dimensions.

To get direction loss, we can use multi-bin loss. To use this loss, we divide the page into several bins that overlap. Moreover, we show each bin with its central angle number. For example, suppose we divide the screen into six bins from zero to 360 degrees. Each bin is 60 degrees, the central angle of the first bin is 0, and the second bin is 60 degrees until the end. Then we determine in which bin the local angle we obtained is located and how much it differs from the central angle of the bin. Therefore, we can calculate the loss. Because working with angles is difficult, we use sin and cos.

![](https://github.com/Fateme-Azizabadi/3D-Object-Detection/blob/main/Images/Loss.Function.png)

### **Network**
Finally, the network is such that one part performs dimension regression, and the other part obtains the sine and cosine of the local angle.

![](https://github.com/Fateme-Azizabadi/3D-Object-Detection/blob/main/Images/Network.png)

## **Training and Output**
This method is very fast because it uses regression.

![](https://github.com/Fateme-Azizabadi/3D-Object-Detection/blob/main/Images/Train.model.png)


![](https://github.com/Fateme-Azizabadi/3D-Object-Detection/blob/main/Images/Output1.png)

![](https://github.com/Fateme-Azizabadi/3D-Object-Detection/blob/main/Images/Output2.png)

![](https://github.com/Fateme-Azizabadi/3D-Object-Detection/blob/main/Images/Output3.png)

![](https://github.com/Fateme-Azizabadi/3D-Object-Detection/blob/main/Images/Output4.png)
 

 
 
