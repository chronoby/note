# Ray Tracing

Rendering ways:
- Object-order rendering
- Image-order rendering

Ray tracing is an image-order algorithm for making renderings of 3D scenes.

## The basic Ray-Tracing algorithm

A basic ray tracer therefore has three parts:
1. ray generation, which computes the origin and direction of each pixel’s viewing ray based on the camera geometry;
2. ray intersection, which finds the closest object intersecting the viewing ray;
3. shading, which computes the pixel color based on the results of ray intersection.

The structure of the basic ray tracing program is:
```c
for each pixel do
    compute viewing ray
    find first object hit by ray and its surface normal n
    set pixel color to value computed from hit point, light, and n
```

## Computing viewing rays

### Orthographic views

To fit an image with $n_x × n_y$ pixels into a rectangle of size (r − l) × (t − b), the pixels are spaced a distance $(r − l)/n_x$ apart horizontally and $(t − b)/n_u$ apart vertically, with a half-pixel space around the edge to center the pixel grid within the image rectangle. This means that the pixel at position (i, j) in the raster image has the position
$$  
u = l + (r − l)(i + 0.5)/n_x
v = b + (t − b)(j + 0.5)/n_y(4.1)
$$
where (u, v) are the coordinates of the pixel’s position on the image plane, measured with respect to the origin e and the basis {**u**, **v**}.

The procedure for generating orthographic viewing rays is then:

- compute u and v using (4.1)
- ray.direction ← −**w**
- ray.origin ← e + u **u** + v **v**

### Perspective views

Let d is the image plane distance from the viewpoint, often loosely called the *focal length*.

Procedure:

- compute u and v using (4.1)
- ray.direction ← −d **w** + u **u** + v **v**
- ray.origin ← **e**

![avatar](/CG/Image/Views.png.png)

## Ray-Object Intersection

Given a ray $p(t)=\textbf{e}+t\textbf{d}$ and an implicit surface $f(\textbf{p})=0$, the values of t we seek are those that solve the equation
$$f(\textbf{p}(t)=0)$$

## Shading
Once the visible surface for a pixel is known, the pixel value is computed by evaluating a shading model.
- Lambertian Shading
- Blinn-Phong Shading
- Ambient Shading

## A Ray-Tracing Program
```c
for each pixel do
    compute viewing ray
    if (ray hits an object with t ∈ [0, ∞)) then
        Compute n
        Evaluate shading model and set pixel to that color
    else
        set pixel color to background color
```

