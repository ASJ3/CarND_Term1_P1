#**Finding Lane Lines on the Road** 


---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on my work with this report


[//]: # (Image References)

[image1]: ./examples/original.png "Original"
[image2]: ./examples/canny.png "Canny"
[image3]: ./examples/houghLines.png "Hough Lines"
[image4]: ./examples/final.png "Final"

---

### Reflection

###1. Pipeline Description

![alt text][image1]

*Example of an original image*

My pipeline consisted of 7 steps:

1. Convert an image to grayscale (while ensuring I knew how many color channels my original and resulting images had)
2. Blur the image
3. Apply a mask to only analyze the bottom part of the image where the lane lines are
4. Find edges using Canny edge dectection

![alt text][image2]

5. Using Hough lines find the lines that match most of the points found in step 3
6. Averaging the Hough lines found in step 4

![alt text][image3]

7. Superimposing my average lines using open cv addWeighted()

![alt text][image4]


In order to draw a single line on the left and right lanes, I modified the draw_lines() function by separating the lines based on their slopes: 
* lines with negative slopes were assigned to the left lane list.
* lines with positive slopes were assigned to the right lane list.

Even then I created a range of valid slope values (based on the angles these slopes represent). 
Based on trial and error, I determined that lines with angles between 36.5 and 45 degrees were acceptable for the right lane (this corresponds to slope between 0.5 and 1). 
Likewise lines with slopes between -0.5 and -1 were acceptable for the left lane.

This worked well for individual pictures. However for videos the lines thus drawn on screen were jittery. 
To fix this issue I decided to store the line values from previous frames and use those values, together with the value from the current frame, to create a line slope and origin from the bottom of the screen.
This worked out in smoothing the lines on video.

The challenge video added another layer of difficulty, as I realized a bright patch of road meant my code was not finding any valid lines for one or two frames. I had to change my code so as to rely only on past lane data when this occurred.


###2. Potential shortcomings with the current pipeline

One potential shortcoming would be what would happen when driving over a bright patch of road for a long distance.
This is because the settings I found to find edges work best when there is a high contrast between the asphalt of the road and the painted lanes lines.

Another shortcoming could be when driving on roads that are very curvy. Since I rely on data from previous frames, this might create a temporary misalignment between my superimposed line and the actual lane markings. While I do not expect this to be an issue for highways, whose curvature are spread over long distances, this could be an issue on smaller curvy roads.


###3. Possible improvements

A possible improvement would be to check the brightness of the road and adjust accordingly the low and high threshold used to find edges in the Canny function. This would help adapt to changes in brightness on the road.

Another potential improvement could be to reduce the number of line data from previous frames used to draw lines on the current frame if the road is more curvy.