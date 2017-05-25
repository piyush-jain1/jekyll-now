---
layout: post
title: Geometry Package (Octave)
---

## Geometry package: Implement boolean operations on polygons ##
As part of GSoC 2017 , this project is intended to implementing a set of boolean operations and supporting function for acting on polygons. These include the standard set of potential operations such as union/OR, intersection/AND, difference/subtraction, and exclusiveor/XOR. Other things to be implemented are the following functions: polybool, ispolycw, poly2ccw, poly2cw, poly2fv, polyjoin, and polysplit.<br/>
There is an already implemented [Geometry3.0 package](https://github.com/piyush-jain1/GSoC17OctaveGeometry). This has mex intrface for its functions. We tried to compare its performance with the oct interface version. For that, we used a **polyUnion** function implemented using oct interface and compared the same function with the one in the geometry package and observed that oct interface had a much better performance than the mex interface version. This result guides us to our first task as implementing the oct interface for the [ClibberLib](http://www.angusj.com/delphi/clipper.php) library.<br/>


