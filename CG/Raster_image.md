# Raster Image

## Images, Pixels, and Geometry

- A raster image is a big array of pixels, each of which stores information about the color of the image at its grid point.
- The pixel value is a local average of the color of the image, and it is called a *point sample* of the image.
- The rectangular domain of a $n_x*n_y$ image is

$$R=[-0.5,n_x-0.5]*[-0.5,n_y-0.5].$$

### Pixel values

- 1-bit grayscale—text and other images where intermediate grays are not
desired (high resolution required);
- 8-bit RGB fixed-range color (24 bits total per pixel)—web and email applications, consumer photographs;
- 8- or 10-bit fixed-range RGB (24–30 bits/pixel)—digital interfaces to computer displays;
- 12- to 14-bit fixed-range RGB (36–42 bits/pixel)—raw camera images for
professional photography;
- 16-bit fixed-range RGB (48 bits/pixel)—professional photography and printing; intermediate format for image processing of fixed-range images;
- 16-bit fixed-range grayscale (16 bits/pixel)—radiology and medical imaging;
- 16-bit “half-precision” floating-point RGB—HDR images; intermediate format for real-time rendering;
- 32-bit floating-point RGB—general-purpose intermediate format for software rendering and processing of HDR images.

### Alpha compositing

- The most important piece of information needed to blend a foreground object over a background object is the *pixel coverage*, which tells the fraction of the pixel covered by the foreground layer. We can call this fraction α.  
- If we want to composite a foreground color $c_f$ over background color $c_b$, and the fraction of the pixel covered by the foreground is $\alpha$, then we can use the formula

$$c=\alpha c_f+(1-\alpha)c_b$$

## Image storage

- jpeg. This lossy format compresses image blocks based on thresholds in
the human visual system. This format works well for natural images.
- tiff. This format is most commonly used to hold binary images or losslessly
compressed 8- or 16-bit RGB although many other options exist.
- ppm. This very simple lossless, uncompressed format is most often used
for 8-bit RGB images although many options exist.
- png. This is a set of lossless formats with a good set of open source management tools.
