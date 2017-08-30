---
layout: post
title: Geometry Package (Octave)
---

# Geometry package: Implement boolean operations on polygons ##
As part of GSoC 2017 , this project is intended to implement a set of boolean operations and supporting function for acting on polygons. These include the standard set of potential operations such as union/OR, intersection/AND, difference/subtraction, and exclusiveor/XOR. Other things to be implemented are the following functions: polybool, ispolycw, poly2ccw, poly2cw, poly2fv, polyjoin, and polysplit.<br/>

This [Repository](https://github.com/piyush-jain1/GSoC17OctaveGeometry) is a clone(fork) of the Geometry Package which is a part of the [Octave Forge Project](http://octave.sourceforge.net/).</br>

This fork adds new functions to the official Geometry Package as part of GSoC (Google Summer of Code) 2016.

The official Geometry Package can be found [here](https://sourceforge.net/p/octave/geometry/ci/default/tree/)

Link to commits on official repo :
- [19a35e](https://sourceforge.net/p/octave/geometry/ci/19a35efc16dbe645e0bbfbffe9cfaa14e5ec9c96/)
- [fc3710](https://sourceforge.net/p/octave/geometry/ci/fc3710b6cce55502e6eff4dc4251d507bc5b4ff1/)

## Added files and functions
1. /inst/polygons2d/clipPolygon_mrf.m
2. /inst/polygons2d/private/\__poly2struct\__.m
3. /src/martinez.cpp
4. /src/polygon.cpp
5. /src/utilities.cpp
6. /src/polybool_mrf.cc
7. /inst/polygons2d/funcAliases

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

After raw testing this new algorithm on several cases, I am now adding few tests in the m-script. Other than that, a demo has also been added in the m-script. The demo can be seen by the command `demo clipPolygon_mrf`. The tests have also been added. The test can be seen by `test clipPolygon_mrf`.

## Second Coding Phase
After implementing the polybool function and checking it, we are planning to include it in the next release of geometry package. 
Now , to move forward, I am first importing some functions from last year GSoC's repo and ensuring their MATLAB compatibility.
Functions like poly2ccw, poly2cw, joinpolygons, splitPolygons have been created as aliases while ensuring their compatibility with their mathworks counterparts. 
Then after, there was some time invested on understanding the CGAL library and its implementation.

The further plan is to sync the matgeom package with the geometry package.

## Third Coding Phase
Proceeding towards the next goal, the idea is to devise some way to automate the process somewhat, of syncing the matgeom and geometry package. The issue is that when a new release of geometry package is planned, there are some things which ahve been updated in matgeom but not in their geometry counterparts (if it exists). So, every time before releasing, so much time has to be invested in manually checking each edit and syncing it into the geometry. 

To achieve this, first a workaround is implemented on a dummy repository - [dummyMatGeom](https://github.com/piyush-jain1/dummyMatGeom/). Its master branch is matGeom (dummy) and there is another branch (named geometry) is created which contains geometry package (dummy). To test the entire procedure, go to the dummy repository dummyMatGeom , pull both branches in different folders, say "dummyMatGeom" for master branch and "dummyGeom" for geometry branch. Then follow the given steps as explained on the [wiki page](http://wiki.octave.org/Geometry_package:GSoC17).

#### Challenges ####

- Clearly, the above procedure will only sync the script of the function, not it's tests and demo, which are in separate folders in a Matlab package structure. Even if we try to concatenate their corresponding test/demo scripts with the function scripts (as it is in an octave package structure), there will be discrepancies because the notion or writing tests for octave and matlab packages are quite different. The way octave allows tests to work is unique to octave as explained here. SO, we can't simply concatenate the Matlab test scripts with the functions. 

- Git doesn't preserves the original version of geometry scripts and overwrites the whole file completely. 
For example :

1. Original file at matGeom (upstream)

~~~~octave

% Bla bla
% bla bla bla

function z = blabla (x,y)
% Help of function
for i=1:length(x)
   z(i) = x(i)*y(i);
end

~~~~

2. Ported to geometry

~~~~octave

# Copyright - Somebody
# Bla bla
# bla bla bla

# texinfo
# Help of function

function z = blabla (x,y)
   z = x .* y;
endfunction

~~~~

3. Updated in matGeom

~~~~octave

% Bla bla
% bla bla bla

function z = blabla (x,y)
% Help of function
% updated to be more clear
z = zeros (size(x));
for i=1:length(x)
   z(i) = x(i)*y(i);
end

~~~~

4. After syncing , the expected result is something like this :

~~~~octave

# Copyright - Somebody
# Bla bla
# bla bla bla

# texinfo
# Help of function
# updated to be more clear

function z = blabla (x,y)
   z = zeros (size(x));
   z = x .* y;
endfunction

~~~~

But, this doesn't happen as expected. Git just finds the files which have been modified and overwrites those files completely.
Considering the possibilities of the solutions, there are ways like `git patch` or `git interactive` which allows us to select the lines specifically which we want to be committed, but that would not serve our purpose as it would not be better than syncing it manually, file by file. Looking for a better solution to handle this !

Now, the further idea is to release geometry and I am getting involved into it to get a feel of how things are done.

Thus, as the time concludes, it's time to say Goodbye to GSoC'17. It was overall a great learning experience.



