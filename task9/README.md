# Task9: Shape Matching Deformation

**Deadline: July 8th (Thursday) at 15:00pm**

![preview](preview.png)


## Setting Up

Please look at the following document for environment setup, creating branch, and making pull request.

- [How to Submit the Assignment](../doc/submit.md)

In this assignment, it is necessary to install [Eigen](https://eigen.tuxfamily.org/index.php?title=Main_Page) library. Please follow the following document for setting up.    

- [How to set up the Eigen Library](../doc/setup_eigen.md)  

Additionally, you need the library [DelFEM2](https://github.com/nobuyuki83/delfem2) in `pba-<username>/3rd_party` 

```bash
$ cd pba-<username> # go to the top of local repository
$ git submodule update --init 3rd_party/delfem2
```

(DelFEM2 is a collection of useful C++ codes written by the instructor.)



## Problem1

Compile the `main.cpp` using the `CMakeLists.txt`, run the program and take a screenshot image. 
Paste the image below.

=== paste screenshot here ===
![task9_problem1](task9_problem1.PNG)


## Problem 2

Edit `main.cpp` to implement the 2D shape matching deformation [1].
The rectangular shape is represented by a quad mesh, and the bottom part of the rectangle is shaken horizontally. 
In the shape matching deformation, we need to fit each quad in the rest shape into the corresponding quad in the tentative shape by optimal rigid transformation. 
The edits should be necessary only around line #123. 
Paste the screenshot image of the program once it is finished.

=== paste screenshot here ===
![task9_problem2](task9_problem2.PNG)


## Material 

The following slides may be useful

![](slide2.png)

![](slide0.png)

![](slide1.png)

-  [JacobiSVD module in Eigen](https://eigen.tuxfamily.org/dox/classEigen_1_1JacobiSVD.html)

- [1] Matthias Müller, Bruno Heidelberger, Matthias Teschner, and Markus Gross. 2005. Meshless deformations based on shape matching. In ACM SIGGRAPH 2005 Papers (SIGGRAPH '05).




### Modified Code:

```c++
// Evaluate the center of gravity of rest position and current position
// Pcg = sum(wi*pi)/sum(wi)
// Initialize zero vectors
//tcg: the center of tentative shape
Eigen::Vector2f tcg=Eigen::Vector2f::Zero();
//Tcg: the center of rest shape
Eigen::Vector2f Tcg=Eigen::Vector2f::Zero();
// Intialize total mass to zero
double amSum = 0;
// Loop over the corners of each quad, and evalute the sum(wi*pi)
for(unsigned int i=0;i<4;++i){
    tcg += am[i]*ap[i];
    Tcg += am[i]*aq[i];
    amSum += am[i];
};
// divide by sum of weights
tcg = tcg/amSum;
Tcg = Tcg/amSum;

// Find the optimum rotation matrix (Ropt) for rigid transformation
// BA = sum(wi*(api - tcg).outer(aqi - Tcg))
// Initialize BA matrix to zero
Eigen::Matrix2f BA=Eigen::Matrix2f::Zero();
// Loop over the corners of the quad and evaluate the sum
for(unsigned int i=0;i<4;++i){
    BA += am[i]*((ap[i]-tcg)*(aq[i]-Tcg).transpose());
}
// Get the Singular Value Decomposition (SVD) of BA
// Ropt = U*V.T
Eigen::JacobiSVD<Eigen::Matrix2f> svd(BA, Eigen::ComputeFullU | Eigen::ComputeFullV);
Eigen::Matrix2f Ropt = svd.matrixU()*svd.matrixV().transpose();
// topt = tcg - Ropt*Tcg
Eigen::Vector2f topt = tcg - Ropt*Tcg;

// Update the position of the tentative corner with same energy transformation
// X_tentative = Ropt*X_rest + topt
// Loop over the corners of the quad
for(unsigned int i=0;i<4;++i){
    Eigen::Vector2f XYopt =  Ropt*aq[i] + topt ;
    aXYt[aQuad[iq*4+i]*2 + 0] = XYopt[0];
    aXYt[aQuad[iq*4+i]*2 + 1]  = XYopt[1];
};

```








