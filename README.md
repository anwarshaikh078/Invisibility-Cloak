# Invisibility-Cloak

If you are a Harry Potter fan like me, you would know what an Invisibility Cloak is. Yes! It’s the cloak which Harry Potter uses to become invisible. Of course, we all know that an invisibility cloak is not real — it’s all graphics trickery.
Well, it turns out that you can create this magical experience using an image processing technique called color detection and segmentation. And the good news is, you don’t need to be part of Hogwarts for that!

### All you need is a red colored cloth and invi.py file.

## How does it work ?
The algorithm is very similar in principle to green screening. But unlike green screening where we remove the background, in this application, we remove the foreground!

We are using a red colored cloth as our cloak. Why red? Why not green? Sure, we could have used green, isn’t red the magician’s color? Jokes aside, colors like green or blue will also work fine with a little bit of tweaking.

The basic idea is given below:
1. Capture and store the background frame.
2. Detect the red colored cloth using color detection algorithm.
3. Segment out the red colored cloth by generating a mask.
4. Generate the final augmented output to create the magical effect.


## Step 1: Capture and store a background frame
As explained above, the key idea is to replace the current frame pixels corresponding to the cloth with the background pixels to generate the effect of an invisibility cloak. For this, we need to store a frame of the background.
### Why capture background image using a ‘for loop’ ?
As the background is static can’t we simply use a single frame? Sure, but the image captured is a bit dark compared to a multiple frames image. This is because the camera is just getting started on capturing frames and hence its parameters are not stable yet. Hence capturing multiple images of static background with a for loop does the trick.

Averaging over multiple frames also reduces noise.
## Step 2: Red color detection
Since we are using a red color cloth to convert it into an invisibility cloak we will focus on detection of red color in the frame.

Sound simple? We have an RGB (Red-Green-Blue) image and it is tempting to simply threshold the R channel and get our mask. It turns out that this will not work effectively since the RGB values are highly sensitive to illumination. Hence even though the cloak is of red color there might be some areas where, due-to shadow, Red channel values of the corresponding pixels are quite low.

The right approach is to transform the color space of our image from RGB to HSV (Hue – Saturation – Value).
## What is HSV color space?
The HSV color space represents colors using three values

1. Hue : This channel encodes color color information. Hue can be thought of an angle where 0 degree corresponds to the red color, 120 degrees corresponds to the green color, and 240 degrees corresponds to the blue color.
2. Saturation : This channel encodes the intensity/purity of color. For example, pink is less saturated than red.
3. Value : This channel encodes the brightness of color. Shading and gloss components of an image appear in this channel.
Unlike RGB which is defined in relation to primary colors, HSV is defined in a way that is similar to how humans perceive color.

For our application, the major advantage of using the HSV color space is that the color/tint/wavelength is represented by just the Hue component.The Hue values are actually distributed over a circle (range between 0-360 degrees) but in OpenCV to fit into 8bit value the range is from 0-180. The red color is represented by 0-30 as well as 150-180 values.

We use the range 0-10 and 170-180 to avoid detection of skin as red. High range of 120-255 for saturation is used because our cloth should be of highly saturated red color. The lower range of value is 70 so that we can detect red color in the wrinkles of the cloth as well.

mask1 = mask1 + mask2

Using the above line, we combine masks generated for both the red color range. It is basically doing an OR operation pixel-wise. It is a simple example of operator overloading of +.

Now that you understood how color detection is done you can change the H-S-V range and use some other mono-color cloth in place of red color. In fact, a green cloth would work better than a red one because green is farthest away from the human skin tone.

## Step 3: Segmenting out the detected red colored cloth
We generated a mask to determine the region in the frame corresponding to the detected color. We refine this mask and then use it for segmenting out the cloth from the frame. 

## Step 4: Generating the final augmented output to create a magical effect.
Finally, we replace the pixel values of the detected red color region with corresponding pixel values of the static background and finally generate an augmented output which creates the magical effect, converting our cloth into an invisibility cloak. To do this we use bitwise_and operation first to create an image with pixel values, corresponding to the detected region, equal to the pixel values of the static background and then add the output to the image (res1) from which we had segmented out the red cloth.

## Dependencies
1. OpenCV 
2. Numpy
