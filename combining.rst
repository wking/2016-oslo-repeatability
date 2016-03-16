==========================================================
Putting it all together: make, git, and a Jupyter notebook
==========================================================

@@pretend wordlcoud takes a long time.

Create a Python2 notebook (or download it@@) in the make-lesson directory;
create (at least) three cells::

::

   !pip install wordcloud

and

and::
   
   def load_frequencies(filename):
        for line in open(filename):
             word, count, freq = line.split()
             if len(word) < 5:
                  continue
             freq = float(freq)
             yield word, freq

and::
  
   import wordcloud
   wc = wordcloud.WordCloud(stopwords=wordcloud.STOPWORDS)
   wc.generate_from_frequencies(load_frequencies('abyss.dat'))

   %matplotlib inline
   from pylab import imshow, axis

   imshow(wc)
   axis('off')
