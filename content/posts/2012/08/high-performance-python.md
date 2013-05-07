Title: High Performance Python
Author: d.rey
Date: 2012-08-03 18:08:00
Slug: high-performance-python
Tags: performance,python

**High Performance Python I**

A very hands-on tutorial for High Performance Python techniques. This updated tutorial will cover profiling, PyPy, Cython, numpy, NumExpr, ShedSkin, multiprocessing, ParallelPython and pyCUDA. 

<iframe allowfullscreen="allowfullscreen" frameborder="0" height="400" src="http://www.youtube.com/embed/Iw9-GckD-gQ" width="600"></iframe>

**High Performance Python II**

It will cover how to write very fast Python code for data analysis and will be briefly introduce NumPy and illustrate how fast code for Python is written in SciPy using tools like Fwrap / F2py and Cython.

<iframe width="600" height="400" src="http://www.youtube.com/embed/xHqlzuPq_qQ" frameborder="0" allowfullscreen></iframe>

**High-Performance Computing with Python**

Traditionally, compiled languages have been used to write the software for massively parallel high-performance computing. Even though dynamic, interpreted languages, such as Python, have gained popularity due to increased programming efficiency, they cannot compete directly with the raw performance of compiled languages. However, by using an interpreted language together with a compiled language, it is possible to have most of the productivity enhancing features together with a good numerical performance.

External hardware-tuned numerical libraries offer excellent performance and in several cases these can be used directly from Python, e.g. through NumPy or petsc4py. Major improvements can be also achieved by re-writing performance critical routines and operations in a compiled language and accessing them through the Python extension mechanism. This approach has been used successfully to implement the software package GPAW for quantum-mechanical simulations of nanostructures. It uses a combination of Python and C programming languages, Python extensions, and external numerical libraries.

While the chosen approach works well in standard workstations and Unix environments, massively parallel supercomputing systems can present some challenges in porting, debugging and profiling the software. We present some details of the implementation and discuss the advantages and challenges of the combined Python/C approach. We show that despite the challenges it is possible to obtain good numerical performance and good parallel scalability with Python based software.

<iframe width="600" height="400" src="http://www.youtube.com/embed/x1x8H0XVIjM" frameborder="0" allowfullscreen></iframe>

**Hint:** Usually it's not so comfortable to watch presentation from youtube directly. Python can solve this issue - [the project youtube-dl](http://rg3.github.com/youtube-dl/). For most cases there's enough to type in command line: 

    :::text
    # youtube-dl -t <url-to-video>
    
to download video on your PC.

