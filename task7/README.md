# Task7: Optimization of Rotation 

- **Deadline: June 17th (Thursday) at 15:00**

![preview](preview.png)


## Setting Up

Pleae look at the following document for environment setup, creating branch, and making pull request.

- [How to Submit the Assignment](../doc/submit.md)

In this assignment, it is necessary to install [Eigen](https://eigen.tuxfamily.org/index.php?title=Main_Page) library.  Please follow the following document for setting up.    

- [How to set up the Eigen Library](../doc/setup_eigen.md)  

Additionally, you need the library [DelFEM2](https://github.com/nobuyuki83/delfem2) installed and updated in `pba-<username>/3rd_party` 

```bash
$ cd pba-<username> # go to the top of local repository
$ git submodule update --init 3rd_party/delfem2
```

(DelFEM2 is a collection of useful C++ codes written by the instructer.)



## Problem1

Build the `main.cpp` using `cmake`. Run the program and take a screenshot image of the window.  Paste the screenshot image below by editing this mark down document:

=== paste screenshot here ===
![task7_problem1](task7_problem1.PNG)



## Problem 2

Optimize the rotation matrix `R` such that the sum of squared distances between the rotated source points and fixed target points are minimized. Use the Netwon's method for the energy minimization. Write some code around line #71 in the `main.cpp` to compute the gradient and hessian of the energy of one pair of source and target points. (The modifications are probably very little, just two lines of short codes). **Once the implementation is successful, the energy should steadly decrease.** 

The following slides **may be** useful for this assignments:

![](slide_rotation0.png)

![](slide_rotation1.png)

![](slide_rotation2.png)



Report the energy after convergence: 

- converged energy = 100

This is the sum of a squared distance of 1 between 100 points


Paste the resulting screenshot image below:

=== paste screenshot image here ===
![task7_problem2](task7_problem2.PNG)

  
  ### modified code
  ```c++
  dW = -2*(Rp).cross(q);
  ddW = -2*(q*Rp.transpose() - Rp.dot(q)*Eigen::Matrix3d::Identity());
  ```

  ### Approach
```c++
  // compute gradient and hessian of the energy below.
  // The squared norm (W) = ||Rp-q|| = (Rp-q).(Rp-q) 
  // W = Rp.Rp - 2*Rp.q + q.q 
  // Rp.Rp = ||Rp|| = ||p|| = Constant && q.q = ||q|| = Constant
  // R(w) = exp(skew(w))*R0
  // w = [w1,w2,w3]
  // skew(w) = [[0 -w3 w2]
  //            [w3 0 -w1]
  //            [-w2 w1 0]]
  // We define dw1, dw2, dw3 as:
  // dw1 = d/dw1(skew(w)) = [[0 0 0],[0 0 -1],[0 1 0]] = skew([1,0,0])
  // dw2 = d/dw2(skew(w)) = [[0 0 1],[0 0 0],[-1 0 0]] = skew([0,1,0])
  // dw3 = d/dw3(skew(w)) = [[0 -1 0],[1 0 0],[0 0 0]] = skew([0,0,1])
  // The first derivative of W with respect to first component dW_dw1
  // dW_dw1 = 0 + d/dw1(-2*(R(w)*p).q) + 0 = (dw1*Rp).q
  // Given: skew(U)*V = U x V  -- cross product
  // Given: (b x c).a = (a x c).b = (a x b).c
  // w1 = [1,0,0] && w2 = [0,1,0] && w3 = [0,0,1] 
  // dW_dw1 = -2*(w1 x Rp).q = -2* (Rp x q).w1
  // dW_dw2 = -2* (Rp x q).w2
  // dW_dw3 = -2* (Rp x q).w3
  // Combine all in one equation
  // dW = -2* (Rp x q).[w1 w2 w3] = -2* (Rp x q).I = -2* (Rp x q)
```


----









