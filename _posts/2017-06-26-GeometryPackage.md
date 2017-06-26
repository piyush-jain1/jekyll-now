---
layout: post
title: Geometry Package (Octave)
---

# Geometry package: Implement boolean operations on polygons ##
As part of GSoC 2017 , this project is intended to implement a set of boolean operations and supporting function for acting on polygons. These include the standard set of potential operations such as union/OR, intersection/AND, difference/subtraction, and exclusiveor/XOR. Other things to be implemented are the following functions: polybool, ispolycw, poly2ccw, poly2cw, poly2fv, polyjoin, and polysplit.<br/>

## Bonding Period
After discussion with my mentor and keeping my proposal in mind, I tried to understand and enlist the tasks more specifically and in detail.  I first tried to understand the conventions and other things about the organisation. I tried to understand the first basic thing which I will need throughout this project, i.e. how we create an oct-interface for the C++ codes to be executable in Octave. 
My first goal was to explore the possibility if the already implemented mex-interface geometry package can be improved in performance by replacing it with oct-interface. So, for understanding how these oct-files work and other things , I started to  implement something in oct-files and getting familiar with it.

## First Coding Phase
As stated, there is an already implemented [Geometry3.0 package](https://github.com/piyush-jain1/GSoC17OctaveGeometry). This has mex interface for its functions. I tried to compare its performance with the oct interface version. For benchmarking, I first implemented my own function for polyUnion (using clipper library) with oct-interface [(Find it here)](https://github.com/piyush-jain1/GSoC17OctaveGeometry/tree/master/devel/MyPolyUnion). Then, I compared its performance over a number of different sets of polgons (parametrized over number of vertices) and recorded the elapsed times with both the interfaces. On plotting a graph of Number of vertices V/s Elapsed time (for oct and mex), following obseravtions were made : </br>
- The oct interface had a better performance over mex.
- For 10000 vertices, the oct interface took about 0.008 seconds while the mex interface took about 0.014 seconds. This implies oct interface took 8X10e-3 seconds / 10e4 vertices i.e. 8X10e-7 seconds per vertex. For mex, it was 14X10e-7 seconds per vertex.
- As it can be seen from the above data, the oct interface was not more than twice as good as mex interface.</br>
From these observations, it was concluded that it is not worth to change the interface from mex to oct since there was not much improvement in performance. Thus, our next goal is now to incorporate new algorithms.

After spending decent time over checking out the new algorithm and understanding its implementation, I have now started to implement the polybool function. I also tried to compare its performance with the already implemented clipPolygon in the current geometry package. The new algorithm has a better performance than the old one. 

The implementation of boolean operations on polygons, like **DIFFERENCE** , **INTERSECTION** , **XOR** and **UNION**   with the new algorithm is almost done. A little work on tests, demos and documentation is still needed and I am working on that.

#### More about the algorithm by F. Martínez, A.J. Rueda, F.R. Feito ####
The algorithm is very easy to understand, among other things because it can be seen as an extension of the classical algorithm, based on the plane sweep, for computing the intersection points between a set of segments.When a new intersection between the edges of polygons is found, the algorithm subdivides the edges at the intersection point. This produces a plane sweep algorithm with only two kind of events: left and right endpoints, making the algorithm quite simple. Furthermore, the subdivision of edges provides a simple way of processing degeneracies. </br>
Overall sketch of the approach for computing Boolean operations on polygons:
- Subdivide the edges of the polygons at their intersection points.
- Select those subdivided edges that lie inside the other polygon—or that do not lie depending on the operation.
- Join the edges selected in step 2 to form the result polygon.</br>
#### Complexity ####
Let n be the total number of edges of all the polygons involved in the Boolean operation and k be the number of intersections of all the polygon edges. The whole algorithm runs in time **O(n+k)log(n)**.</br>

After raw testing this new algorithm on several cases, I am now adding few tests in the m-script. Other than that, a demo has also been added in the m-script. The demo can be seen by the command `demo clipPolygon`. 





