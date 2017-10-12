# **Finding Lane Lines on the Road** 

## Approach and descriptions

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Explaining the pipeline and the approach of finding lanes

---

### Reflection

### 1. Algorithms for pipeline preparation

From the beginning of the course i wrote jupyter notebooks for all steps to better understand the approaches.

https://github.com/kkargi/Finding-Lane-Line-CV-1/blob/master/Jupyter-Practice-Files/1-Color-Selection-Code.ipynb

https://github.com/kkargi/Finding-Lane-Line-CV-1/blob/master/Jupyter-Practice-Files/2-Region-Masking.ipynb

https://github.com/kkargi/Finding-Lane-Line-CV-1/blob/master/Jupyter-Practice-Files/3-Combined-Color-and-Region-Selections.ipynb

https://github.com/kkargi/Finding-Lane-Line-CV-1/blob/master/Jupyter-Practice-Files/4-Canny-Edge-Detection.ipynb

https://github.com/kkargi/Finding-Lane-Line-CV-1/blob/master/Jupyter-Practice-Files/5-Hough-Transform.ipynb

So at the last in the project working we used these steps as helper functions and we already found the parameters for each algorithm needs.


### 2. My Pipeline

First we get all of the images from the video.

For all images the operations below did one by one for every image, and the result added the result video. Video reading and writing was easy with moviepy, but it was a challenge to first run it.

1. Convert the image to grayscale
    grayImage = grayscale(image)
2. Do blur filter to grayscale image to smooth the sharp areas and clear the small blobs. I used a 5x5 kernel for blurring.
    blurgrayImage = gaussian_blur(grayImage,5)
3. Get the edges of the image with Canny Edge Detection algorithm. The edges are color changes of pixels. We use two thresholds and set the low one 50 and the high threshold 150. 
    edgeImage=canny(blurgrayImage,50,150)    
    
4. Get the image shape to use the width and height    
    imshape = image.shape

5. Set a ROI (Region of interest) not to process all image. Because other parts are not related for this problem and we want to ignore and filter other parts based on ROI
    vertices = np.array([[(50,imshape[0]),(475, 312), (505, 312), (imshape[1],imshape[0])]], dtype=np.int32)
    ROIedgeImage=region_of_interest(edgeImage,vertices)
  
6. Make a hough transformation and find the intersecting points on the hough space to find which point are on the same line in normal space. This function also draws the line on black image with red color. I added some additional code to draw lines internal functions to put the bottom point of the line same with the image bottom point (max Y)
    line_image=hough_lines(ROIedgeImage,1,np.pi/270, 30, 20, 120)
7. Merge the lines image and original image and create a new image line has drawn on original image
    lines_edges = weighted_img(image, line_image, 0.8, 1, 0)     

8. Send back this new image as a result to add it to result video.


### 3. Identify potential shortcomings with your current pipeline


It is not adapted to turning roads, to shadows, not suitable for crowded traffic which we cannot see the lines easily. I think in real world there are many other challenges which this solution is not suitable. Also in this sample videos sun light is good and it is at nearly noon. Afternoon the shadows will go longer and change the colors on the road. Also in rainy, snowy and many other outside effects, this solution will not solve the problem.


### 4. Suggest possible improvements to your pipeline

I have to connect the partial lines. Now they are all seperates lines in one lane line but seems like one line because of same red color. I changed the colors of all lines and saw that there i have to improve the quality.

I can use some other color spaces like HSL (Hue, saturation, luminance) with supporting of RGB space to find the lanes by color. We can check the edge areas bycolor spaces on the original image, if they are like a lane line or something else, after finding edges.