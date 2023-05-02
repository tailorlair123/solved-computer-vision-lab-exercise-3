Download Link: https://assignmentchef.com/product/solved-computer-vision-lab-exercise-3
<br>
<h1> Image Alignment and Stitching</h1>

Students should work on this lab exercise in groups of two people. In this assignment you will write a function that takes two images as input and computes the affine transform between them. You are provided three template files, that have to be filled in using the below information:

<ul>

 <li><em>&lt; imageAling.m &gt; </em>(aligns two images using the RANSAC algorithm for finding the affine transformation),</li>

 <li><em>&lt; ransac affine.m &gt; </em>(implements the RANSAC algorithm for finding the affine transformation),</li>

 <li><em>&lt; mosaic.m &gt; </em>(stitches the two images into one).</li>

</ul>

<h2>1       Image Alignment</h2>

You will work with the supplied boat images. The overall scheme is as follows:

<ol>

 <li>Detect interest points in each image.</li>

 <li>Describe the local appearance of the regions around interest points.</li>

 <li>Get the set of possible matches between descriptors in two image.</li>

 <li>Implement RANSAC to discover the transformation between images.</li>

</ol>

The first three steps can be performed using David Lowe’s SIFT (lab exercise 2): You can use the following functions to obtain the pairs for your RANSAC algorithm.

<table width="527">

 <tbody>

  <tr>

   <td width="527">1      [frames1, desc1]=sift(im1)2      [frames2, desc2]=sift(im2)34 [matches] = vlubcmatch(desc1, desc2)</td>

  </tr>

 </tbody>

</table>

Compare these results with your own implementation from lab 2. <strong>Note: </strong>For the final project you will be graded for your own Harris corner point detection and feature matching implementation.

For step 4, RANSAC, should be implemented as follows:

<ol>

 <li>Pick <em>P </em>matches at random from the total set of matches <em>T</em>.</li>

</ol>

<table width="488">

 <tbody>

  <tr>

   <td width="39">1</td>

   <td width="448">perm = randperm(?);</td>

  </tr>

  <tr>

   <td width="39">2</td>

   <td width="448">seed = perm(1:P);</td>

  </tr>

 </tbody>

</table>

How many matches do we need to solve an affine transformation which can be formulated as shown in Fig 1

Figure 1: Affine transform

<ol start="2">

 <li>Construct a matrix <strong>A </strong>and vector <strong>b </strong>using the <em>P </em>pairs of points to find the affine transformation parameters (Fig.1) (<em>m</em><sub>1</sub><em>,m</em><sub>2</sub><em>,m</em><sub>3</sub><em>,m</em><sub>4</sub><em>,t</em><sub>1</sub><em>,t</em><sub>2</sub>) by solving the equation <strong>Ah </strong>= <strong>b</strong>, where:</li>

</ol>

 <em>m</em>1

 <em>m</em>2

 

” <em>x    y    </em>0   0   1   0#        <em>m</em>3       ” <em>x</em>0#

<em>A </em>=     0    0    <em>x    y    </em>0    1 <em>,h </em>=  <em>m</em>4<em>,b </em>=      <em>y</em><sub>0                              </sub>(1)

 

 <em>t</em>1 <sub></sub>

  <em>t</em>2

<ol start="3">

 <li>Fit the model</li>

</ol>

Hint: Equation <strong>Ah </strong>= <strong>b </strong>can be solved using pseudo-inverse. In Matlab use:

<table width="488">

 <tbody>

  <tr>

   <td width="35">1</td>

   <td width="453">h = pinv(A)* b’</td>

  </tr>

 </tbody>

</table>

Figure 2: Correspondence in two image

<ol start="4">

 <li>Using the affine transform parameters, transform the locations of all <em>T </em>points in image 1. If the transform is correct, they should lie close to their counterparts in image 2. Plot the two images side by side with a line connecting the original <em>T </em>points image 1 and transformed <em>T </em>points over image 2 as shown in Fig 2.</li>

</ol>

Recompute the matrix <strong>A </strong>over all points, and estimate the new <strong>b</strong><sup>0</sup>.

<table width="488">

 <tbody>

  <tr>

   <td width="59">1</td>

   <td width="429">bprim = A*h</td>

  </tr>

 </tbody>

</table>

<ol start="5">

 <li>Find inliers: Count the numbers of inliers being defined as the number of transformed points from image 1 that lie within a radius of 10 pixels of their pair in image 2.</li>

</ol>

<table width="488">

 <tbody>

  <tr>

   <td width="35">1</td>

   <td width="453">inliers = find(sqrt(sum((?).ˆ2)) <em>&lt;</em>threshold);</td>

  </tr>

 </tbody>

</table>

<ol start="6">

 <li>Refit the model and save the best model (inliers and the parameters) so far and repeat previous steps <em>N </em> How do you choose <em>N</em>? Hint : Let <em>q </em>be the probability of choosing an inlier each time a point is sampled, <em>q </em>is given by <em>q </em>= <u><sup>number</sup></u><sub>sample</sub><u><sup>inliers</sup></u><sub>size </sub>when <em>p </em>points are needed for the model estimation, over <em>i </em>iterations. The probability that the algorithm never selects a set of <em>p </em>points of which all are inliers is given by (1−<em>q<sup>p</sup></em>)<em><sup>i</sup></em>. This can be set to an acceptable error threshold (say 0.001).</li>

</ol>

Once the estimation of <strong>h </strong>is finished, transform image 1 using this final set of transformation parameters. If you display this image you should find that the pose of the object in the scene should correspond to its pose in image 2. To transform the image, use the build-in Matlab functions as follows

<table width="527">

 <tbody>

  <tr>

   <td width="28">1</td>

   <td width="499">affinetransform = [h(1) h(2) h(5);…</td>

  </tr>

  <tr>

   <td width="28">2</td>

   <td width="499">h(3) h(4) h(6);…</td>

  </tr>

  <tr>

   <td width="28">3</td>

   <td width="499">                                                               0           0           1         ];</td>

  </tr>

  <tr>

   <td width="28">4</td>

   <td width="499">tform = maketform(‘affine’, affinetransform’);</td>

  </tr>

  <tr>

   <td width="28">5</td>

   <td width="499">image1transformed = imtransform(image1, tform, …‘bicubic’);</td>

  </tr>

 </tbody>

</table>

Do the same procedure for the second image, but then with the inverse transformation, as:

<table width="527">

 <tbody>

  <tr>

   <td width="28">1</td>

   <td width="499">tform = maketform(‘affine’, inv(affinetransform)’ );</td>

  </tr>

  <tr>

   <td width="28">2</td>

   <td width="499">image2transformed = imtransform(image2, tform, …‘bicubic’);</td>

  </tr>

 </tbody>

</table>

The final images that you should plot for this exercise are depicted in Fig 3.

<strong>Image 1                                                               Image 2</strong>

<strong>Image 2 transformed to image 1                          Image 1 transformed to image 2</strong>

Figure 3: Transformed images

<h2>2       Image Stitching</h2>

In this assignment you will write a function that takes two images as input and stitch them together. You will work with supplied image <em>left.jpg </em>and <em>right.jpg</em>. The overall scheme can be summarized as follows:

<ol>

 <li>Find the best transformation between input images, i.e., by taking the first image as original (no transform) and estimating the affine transformation of the second image with respect to the first image. Use the image alignment function that you developed in the previous task.</li>

 <li>Note that images do not have the same size. You need to think about the size of your final panoramic image. How do we determine that? Hint: calculate the transformed coordinate of corners of the <em>jpg</em></li>

 <li>Finally, combine the <em>jpg </em>with the transformed <em>right.jpg </em>in one image. The final output should look like Fig 4. Hint: Use <em>nanmean </em>function in Matlab if you need to compute the mean of a matrix with ”NaN” values in it.</li>

</ol>

Figure 4: Stitched image