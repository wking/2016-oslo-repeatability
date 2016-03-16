==========================================================
Putting it all together: make, git, and a Jupyter notebook
==========================================================

Discussion topic: why are we using 'make' for the word counting exercise?

.. @@pretend wordlcoud takes a long time.

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

When you run this all, you should see a word cloud!  (It's not very inspiring,
sorry.)

Now add and commit this, & push to github.  In the terminal window, do::

   git add wordcloud.ipynb
   git commit -m "wordcloud notebook"
   git push

Recap
-----

foo!

