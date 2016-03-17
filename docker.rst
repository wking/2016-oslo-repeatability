##################################
Running in a container environment
##################################

Learning objectives: learn about Docker containers, and how they can help deal
with dependencies.

.. note::

   To go through the following, you'll need to have Docker installed.
   For most of it, you'll also need to have an Amazon Web Services
   account.

Introduction: dependencies, and containers
------------------------------------------

If you think back to our original overview, we've got three of the four
workflow components in place - we've got a workflow that takes our raw
data and converts it into a summary, we've got an analysis notebook that
generates results from that data, and we've put it in git.
           
.. thumbnail:: images/overview-2-highlighted.png
   :width: 20%

But, in order to run it, we still need a lot of things installed.  In
this case, we don't have that weird a stack of software: we need
Python, make, Jupyter Notebook, and wordcloud - which depends on numpy
and matplotlib.  Not terribly.

But, hypothetically, we could have *many* dependencies - for example,
bioinformatic workflows often involve dozens of dependencies, with
things written in Java and Ruby and Perl and ...

Installing all of these dependencies on your own laptop may be
difficult enough, but it becomes nearly impossible in shared
environments like lab workstations and HPCs, where you generally don't
have admin/install privileges, and where you have to be concerned
about conflicts between dependencies because it's a shared environment.

This is where isolated environments come in.  These allow you to install
a collection of software in such away that it only interacts within that
collection.

There's a bunch of ways to do this, ranging from language-specific
(virtualenv in Python) to whole-machine (virtual machines and cloud).

Docker containers are a new-ish way to do this.  Docker basically
provides a lightweight virtual machine that is quick and easy to start
up, and the Docker ecosystem includes ways of specifying
configurations and also shipping these fully configured "containers"
around.

A super quick introduction to Docker
------------------------------------

foo!

Using docker-machine to run Docker on AWS
-----------------------------------------

bar!

Building your own Docker image (by writing a Dockerfile)
--------------------------------------------------------

baz!

.. python-numpy python-matplotlib
   unzip?

.. Dockerfile
.. FROM jupyter/notebook
.. RUN

Return to index: :doc:`index`
