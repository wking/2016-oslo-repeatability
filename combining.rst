==========================================================
Putting it all together: make, git, and a Jupyter notebook
==========================================================

Discussion topic: why are we using 'make' for the word counting exercise?

.. @@pretend wordlcoud takes a long time.

Related discussion topic: how do we decided what to put in 'make' and what
to put in a notebook?
   
Adding a data analysis narrative
--------------------------------

Rather than doing a real data analysis, we're just going to make a
word cloud from the word count information.  (If this were a Real Scientific
Project, this is where you'd be doing your statistics and your graphing.)

Create an empty Python2 notebook named 'wordcloud' in the make-lesson
directory; in it, add three cells.

First, install the wordcloud library::

   !pip2 install wordcloud

then write & run some parsing code to generate the wordcloud object::
   
   def load_frequencies(filename):
        for line in open(filename):
             word, count, freq = line.split()
             if len(word) < 5:
                  continue
             freq = float(freq)
             yield word, freq

   import wordcloud
   wc = wordcloud.WordCloud(stopwords=wordcloud.STOPWORDS)
   wc.generate_from_frequencies(load_frequencies('abyss.dat'))

and finally show the wordcloud object::
  
   %matplotlib inline
   from pylab import imshow, axis

   imshow(wc)
   axis('off')

When you run this all, you should see a word cloud!

Now add and commit this, & push to github.  In the terminal window, do::

   git add wordcloud.ipynb
   git commit -m "wordcloud notebook"
   git push origin master

Recap
-----

You have done the following:

1. encapsulated your analysis in a Makefile that generates results from
   raw data;
2. put your analysis in a git repository, and posted it to github;
3. written an analysis notebook to generate figures from the results of
   running the make command;

At this point, you have a fully articulated analysis workflow with both
a script-based data reduction and a Jupyter Notebook graphing analysis.

Moreover, anyone who has access to your git repository (which is
*everybody*, in this instance) can both get your workflow and (with
the proper software) run it.

At this point we can take advantage of a service called 'mybinder' which
will actually let you *run* your analysis and your notebooks for free
on the Google cloud.

Testing it out with mybinder
----------------------------

Take the URL of your github repository and paste it into the top of
mybinder.org; you don't need to configure any dependencies. Now click
'make my binder'.  Once it builds, click on the black-and-red button
'launch binder'.

This will spin up an execution container, running Jupyter notebook,
that has your analysis repo in it, with everything copied from your
github repo.

The 'launch binder' URL is something you can give to other people so
they can run your analysis, too.

To read more on mybinder, see `my blog post
<http://ivory.idyll.org/blog/2016-mybinder.html>`__.

Next: :doc:`docker`
