# View 

## View transformation
The 3D to 2D mapping is called viewing transformation, and it plays an important role in object-order rendering.

As with all complicated transformations it is best approached by breaking it up into a product of several simpler transformations. Most graphics systems do this by using a sequence of three transformations:
- A camera transformation or eye transformation, which is a rigid body transformation that places the camera at the origin in a convenient orientation. It depends only on the position and orientation, or pose, of the camera.
- A projection transformation, which projects points from camera space so that all visible points fall in the range −1 to 1 in x and y. It depends only
on the type of projection desired.
- A viewport transformation or windowing transformation, which maps this unit image rectangle to the desired rectangle in pixel coordinates. It depends only on the size and position of the output image.

### Transformation 
Windowing transformations: we need to create a transform matrix that takes points in the rectangle $[x_l, x_h]*[y_l, y_h]$ to the rectangle $[x_l', x_h']*[y_l', y_h']$

process:
1. Move the point $(x_l, y_l)$ to the origin.
2. Scale the rectangle to be the same size as the target rectangle.
3. Move the origin to point $(x_l', y_l')$
$$
window = translate(x_l', y_l')scale(\frac{x_h'-x_l'}{x_h-x_l})translate(-x_l, -y_l)
$$
$$=
\begin{bmatrix}
   \frac{x_h'-x_l'}{x_h-x_l} & 0 & \frac{x_l'x_h-x_h'x_l}{x_h-x_l} \\
   0 & \frac{y_h'-y_l'}{y_h-y_l} & \frac{y_l'y_h-y_h'y_l}{y_h-y_l} \\
   0 & 0 & 1
\end{bmatrix} 
$$

- Quaternions
(reserved)

### The camera transformation

Define the camera first:
- the eye position **e**
- the gaze direction **g**
- the view-up vector **t**

Use the construction:
$$
w = -\frac{g}{||g||}\\
u = \frac{t\times w}{||t\times w||}\\
v = w\times u
$$
The equation is
$$
M_{cam} = 
\begin{bmatrix}
   u & v & w & e \\
   0 & 0 & 0 & 1
\end{bmatrix}
^{-1}=
\begin{bmatrix}
   x_u & y_u & z_u & 0\\
   x_v & y_v & z_v & 0\\
   x_w & y_w & z_w & 0\\
   0 & 0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
   1 & 0 & 0 & -x_e\\
   0 & 1 & 0 & -y_e\\
   0 & 0 & 1 & -z_e\\
   0 & 0 & 0 & 1
\end{bmatrix}
$$

## Porjection transformation

### The viewport transformation

We assume that the geometry we want to view is in the canonical view volume, and we wish to view it with an orthographic camera looking in the -z direction.

If we are drawing into an image (or window on the screen) that has $n_x$ by $n_y$ pixels,

$$
\begin{bmatrix}
   x_{screen} \\
   y_{screen} \\
   0
\end{bmatrix} = 
\begin{bmatrix}
   \frac{n_x}{2} & 0 & 0\\
   0 & \frac{n_y}{2} & \frac{n_y-1}{2}\\
   0 & 0 & 1
\end{bmatrix}

\begin{bmatrix}
   x_{canonical} \\
   y_{canonical} \\
   0
\end{bmatrix}
$$

### The orthographic projection transformation
Just drop z coordinate!

### Projection transformation

$$
y_s = (n/z)y
$$
$$
P = 
\begin{bmatrix}
   n & 0 & 0 & 0\\
   0 & n & 0 & 0\\
   0 & 0 & n+f & -fn\\
   0 & 0 & 0 & 0
\end{bmatrix}
$$

## Field of view
reserved
