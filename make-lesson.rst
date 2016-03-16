=================================================
A brief introduction to 'make', a workflow engine
=================================================

We're going to walk through the first two parts of the
`Software Carpentry 'make' tutorial <https://swcarpentry.github.io/make-novice/index.html>`__.

Create a new terminal by going to the Console (File... Open...) and then
doing New... Terminal.  See the screenshot below --

.. thumbnail:: ./images/console-new-terminal.png
   :width: 20%

Once you have a new terminal, copy and paste the following::

   wget https://swcarpentry.github.io/make-novice/make-lesson.zip
   unzip make-lesson.zip
   mv make-lesson/* .
   rmdir make-lesson

This downloads a bunch of stuff from the make lesson, and puts it in the
current directory.

.. note::

   A tip on organizing your windows: you can arrange your tabs and windows
   to faciliate copy-pasting!  Put your terminal window in a new window,
   and then you can use Alt-TAB (on Windows) or Command-backquote (on Mac)
   to switch quickly between the windows.

You will also need to configure matplotlib to display to a file - see `this stackoverflow issue <https://stackoverflow.com/questions/4930524/how-can-i-set-the-backend-in-matplotlib-in-python>`__::

   echo backend : Agg >> matplotlibrc

----

Now, let's walk through `Automation and Make: Introduction
<https://swcarpentry.github.io/make-novice/01-intro.html>`__ and
`Makefiles
<https://swcarpentry.github.io/make-novice/02-makefiles.html>`__.

.. One final note -- to edit, you'll need to use the 

----

Next: :doc:`stuff`
