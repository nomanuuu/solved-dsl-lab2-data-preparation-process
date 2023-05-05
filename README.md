Download Link: https://assignmentchef.com/product/solved-dsl-lab2-data-preparation-process
<br>
The purpose of this laboratory is to make you practice with the data preparation process. More specifically, you will tackle the task with tabular and textual input data, learning how to handle anomalies in the data, missing values and more.

1          Preliminary steps

1.1         Matplotlib availability

In order to complete some of the optional exercises you will need the matplotlib plotting library. You can read more about it on its <a href="https://matplotlib.org/">official documentation</a><a href="https://matplotlib.org/">.</a> You can check if it is already installed by running import matplotlib either in Jupyter or iPython.

Among its numerous functionalities, you will use the histogram plotting function in this laboratories. An histogram is a simple representation of the distribution of numerical data. It presents the binned range of values on x-axis (i.e. a series of intervals in which data is divided) and the frequency of each bin on the y-axis. You can read more about it on <a href="https://en.wikipedia.org/wiki/Histogram">Wikipedia</a><a href="https://en.wikipedia.org/wiki/Histogram">.</a>

Here it is a short python script on how to use matplotlib to plot the histogram of values contained in a list. Figure 1 shows an example of the resulting chart.

from random import gauss import matplotlib.pyplot as plt

l = [gauss(0, 1) for _ in range(500)] plt.hist(l)

plt.title(‘Gaussian distribution (mu=0, sigma=1)’) plt.show()

Note that, if you are using Jupyter Notebook, you should add the following line, after importing matplotlib:

%matplotlib inline

<h2>1.2         Datasets download</h2>

For this lab, two different datasets will be used. Here, you will learn more about them and how to retrieve them.

Gaussian distribution (mu=0, sigma=1)

Figure 1: A gaussian distribution plotted with Matplotlib

<strong>Global Land Temperature</strong>

The Global Land Temperature (GLT) dataset is a large collection of measurements actively maintained by Berkeley Earth. It contains the raw source data measured with stations all around the globe, plus an intermediate format and several formatted output files. Data span from ∼1750 up to recent days with monthly and daily availability. Measurements are provided by hemispheres, states, countries, cities and more. You can read more about the dataset at <a href="http://berkeleyearth.org/data/">the Berkeley Earth website.</a>

For the purpose of this laboratory you will work on a modified, smaller but dirtier, version of the original GLT dataset, to stress the importance of data preprocessing. More specifically, this didactic version contains the formatted output files of the major cities of the globe with monthly granularity. For the sake of simplicity, the analysis will range between almost two centuries (i.e. between the years 1817 and 2012).

The dataset is composed of ∼200k rows corresponding to the measurements taken the first day of the month in a given city. Each measurement is then described by 7 values:

<ul>

 <li>Date, when the measurement was taken</li>

 <li>AverageTemperature</li>

 <li>AverageTemperatureUncertainty</li>

 <li>City, from which the measurement was taken</li>

 <li>Country</li>

 <li>Latitude</li>

 <li>Longitude</li>

</ul>

The dataset is available in CSV format. You can find it at the following URL https://raw.githubusercontent.com/dbdmg/data-science-lab/master/datasets/GLT_filtered.csv

<strong>Info: </strong>even though laboratories do not have a strict order, we consider what you have accomplished during the previous ones to be part of your experience. At this point you should be able to download the file and parse it with the csv python module.

<strong>IMDb reviews</strong>

Internet Movie Database (IMDb) is a popular online platform that gathers many information related to multimed like movies, tv shows, video games and many more. You can navigate to its <a href="https://www.imdb.com/">website</a> to explore the huge amount of updated content it offers. The only difference with the previously used CSV datasets is that, in this one, the first row of the file contains a “header” (i.e. the name of each column). You should skip the first row and start reading from the second one on.

IMDb has soon become a rich data source for the scientific community. Among the others, people’s reviews are one of the most important types of data that can be retrieved from the platform. The collection of reviews on a specific movie, for example, contains intrinsic information about its approval rating. Therefore, a considerable amount of recent science works has addressed the identification of the sentiment within textual reviews and surveys. In the context of movie reviews, the sentiment analysis would seek to discover if the reviewer liked the movie or not based on the content of the text, with the sentiment being represented as numerical value (e.g. a score between 1 and 10) or a binary one (e.g <em>Positive </em>or <em>Negative</em>). <a href="https://en.wikipedia.org/wiki/Sentiment_analysis">Wikipedia</a> provides a general overview on the topic of sentiment analysis.

During this laboratory you will work on the dataset collected and used by Maas et al. 2011. We will focus on a smaller portion of the whole dataset, known as the training data (you will learn more on training and test datasets). It contains 25<em>,</em>000 user reviews collected from IMDb for different movies. Since each review has also a numerical score between 1 and 10, the authors considered the ones with a score lower than 4 to have a negative sentiment, while the ones with a score higher than 6 to have a positive sentiment. The dataset includes 12<em>,</em>500 positive and 12<em>,</em>500 negative reviews. For the purpose of this laboratory, reviews, which originally came in different files, have been organized in a single CSV file. Each line of the file refers to a single review and has two fields:

<ol>

 <li>the textual comment;</li>

</ol>

<strong>Info: </strong>you do not have to handle commas inside the text. Each comment comes surrounded by the <em>¨ </em>character which helps the csv module to properly split the fields for you.

<ol start="2">

 <li>a binary value indicating either the positive or negative sentiment, represented respectively by a ’1’ and a ’0’.</li>

</ol>

Here it is a pseudo-representation of the first lines of your dataset:

review,label

&lt;review_text&gt;,1

&lt;review_text&gt;,1

&lt;review_text&gt;,0

<strong>Warning: </strong>the first line of the file contains the header, which describes the schema of your dataset. It is commonplace to store data in this format.

You can download the dataset from the following link https://raw.githubusercontent.com/dbdmg/data-science-lab/master/datasets/aclimdb_reviews_train.txt

<h1>2          Exercises</h1>

Note that exercises marked with a (*) are optional, you should focus on completing the other ones first.

<h2>2.1         Data preprocessing on Global Land Temperature</h2>

The main goal of this exercise is to learn how to clean a real-world dataset searching for anomalies, such as missing values or outliers, in its data.

<strong>Prerequisites. </strong>There are many ways to handle missing values. One can decide to delete a row of the dataset based on whether a missing value is present or not. This strategy can be adopted when the dataset is large and the information loss does not affect the overall distribution. Another common solution is to fill every missing value. If data has not a specific order, they can be replaced with the mean (or the median) of the involved attribute. Temporal data, instead, allow to replace missing values with values of adjacent rows, e.g. by averaging them. Clearly, this technique is possible if the data type allows to compute the mean.

<ol>

 <li>Load the Global Land Temperature dataset as a list of lists. Before starting, take a moment to better inspect the attributes you are going to work on. How many of them are nominal, how many continuous or discrete?</li>

 <li>Analyze the attribute AverageTemperature, which contains missing values. Fill any gap with the arithmetic mean among the closest antecedent and the closest successive measurements in time, taken in the same city. Assume the following rules for edge cases:

  <ul>

   <li>it can happen that a missing value does not have a preceding (or successive) measurement. This happens when the missing value is the first (or last) value of the dataset. If this is the case, consider the missing value to be preceded (or followed) by a 0, then compute the mean accordingly.</li>

  </ul></li>

</ol>

original_list = [ ”, 5, 6, ” ]

step_1               = [ 2.5, 5, 6, ” ] # (0 + 5) / 2 step_2   = [ 2.5, 5, 6, 3 ] # (6 + 0) / 2

<ul>

 <li>if there are consecutive missing values, just compute them in temporal order and use the newly inserted values to evaluate the following ones. Here it is an example with a simple list where both (a) and (b) rules have been applied:</li>

</ul>

original_list = [ ”, ”, 24, 28.9 ]

step_1               = [ 12, ”, 24, 28.9 ] # (0 + 24) / 2 step_2          = [ 12, 18, 24, 28.9 ] # (12 + 24) / 2

<ol start="3">

 <li>Define a function that, given the name of a city and an integer <em>N &gt; </em>0, prints:

  <ul>

   <li>the top N hottest measurements; (b) the top N coldest measurements.</li>

  </ul></li>

 <li>(*) Let’s search for other anomalies in data distribution with the help of matplotlib. Plot the distribution of the average land temperatures for Rome and Bangkok using the aforementioned histogram plotting function.</li>

</ol>

<strong>Info: </strong>calling the plt.hist() method twice will draw the second histogram onto the canvas generated by the first call.

As you can see, Rome and Bangkok have very different temperature distributions, but this seems plausible. What it looks strange is the large difference in their temperatures’ magnitude. Is it possible that <em>all </em>sensors from Bangkok stations, along the <em>entire </em>time-span, were faulty? Could they were configured to use another representation of the temperature measurement? Can you figure out a data cleaning step to solve it?

<em>Before continuing, try to answer to these questions.</em>

<ol start="5">

 <li>(*) One might think that Bangkok sensor provide temperature samples in degrees Fahrenheit while the ones located in Rome use the Celsius notation, which is the common representation in the whole dataset. Write a function to transform Fahrenheit measurements back to Celsius, apply it to your data and plot the two distribution again.</li>

</ol>

<strong>Info: </strong>remember that the mapping function from Celsius to Fahrenheit is the following

<em>T<sub>F </sub></em>= 1<em>.</em>8 · <em>T<sub>C </sub></em>+ 32

<h2>2.2         Textual data preparation on IMDB reviews</h2>

This exercise is meant to get you acquainted with the preprocessing of textual data. You can find useful information about Information Retrieval theory in the book “Introduction to Information Retrieval” (Manning, Raghavan, and Schütze 2008) also available at <a href="https://nlp.stanford.edu/IR-book/information-retrieval-book.html">Introduction to Information Retrieval</a> Stanford web page.

<strong>Prerequisites. </strong>Textual data (like that from IMDb reviews) comes without a specific structure. Even when the text itself is correctly formatted, with no syntax error, misspelled words, misplaced punctuation or any other possible flaw generated by a human typing on a keyboard (which is, in practice, never the case), many data analytics and machine learning algorithms cannot operate on the bare textual representation. The metrics you have already encountered (e.g. arithmetic mean and standard deviation) do not make sense in this context. Nonetheless, many practical tasks would benefit from mathematical computations on textual documents. In all these cases, the data preprocessing step becomes crucial.

Along this exercise you will focus on one of them. Let’s imagine the IMDb platform wants an automatic procedure to assign a numerical value to a user review, based of the content of the text, to identify the rate he or she would have given to the movie. Simplifying the task, you might want to assign a label, Positive or Negative, to identify the overall sentiment, i.e. whether the user liked the movie or not.

To do so, you will implement the <em>TF-IDF </em>weighting scheme on IMDb reviews. Then, you will compute the distance between a review whose sentiment is unknown and the two sets of positive and negative comments, assigning the sentiment of the closest one among the two.

<ol>

 <li>Load the IMDb dataset as a list of lists.</li>

 <li>Apply the tokenization function listed below to your reviews. Please refer to the function’s docstring <a href="#_ftn1" name="_ftnref1"><sup>[1]</sup></a>for the input and output parameters. The tokenization procedure splits each comment in tokens (i.e. separate words). import string def tokenize(docs):</li>

</ol>

<em>“””Compute the tokens for each document.</em>

<em>Input: a list of strings. Each item is a document to tokenize.</em>

<em>Output: a list of lists. Each item is a list containing the tokens of the relative document.</em>

<em>“”” </em>tokens = []

for doc in docs:

for punct in string.punctuation: doc = doc.replace(punct, ” “)

split_doc = [ token.lower() for token in doc.split(” “) if token ] tokens.append(split_doc) return tokens

<ol start="3">

 <li>The next step requires the computation of the <em>term frequency </em>(TF) of each token within its respective document. Although there exist different techniques to evaluate the frequency, we will now assume that the TF of a token <em>t </em>in a document <em>d </em>is equal to the number of occurrences of <em>t </em>in <em>d</em>. Compute the TF for all your reviews.</li>

</ol>

<strong>Info: </strong>if you decide to count the frequency of every term in the collection of reviews for each document (i.e. not only the terms contained in that document), you will build what is known in literature as <a href="https://en.wikipedia.org/wiki/Bag-of-words_model"><em>Bag-of-Words</em></a><a href="https://en.wikipedia.org/wiki/Bag-of-words_model">.</a> However, since many terms are not present in all documents, you will end up with many values equal to 0. This representation is a <a href="https://en.wikipedia.org/wiki/Sparse_matrix">sparse matrix</a><a href="https://en.wikipedia.org/wiki/Sparse_matrix">.</a> Storing many 0-valued cells like in sparse matrices is quite inefficient. As a consequence, you should pay attention to the choice of your data structure.

Here it is an example of TF on a toy sentence:

tokens = [‘with’, ‘great’, ‘power’, ‘comes’, ‘great’, ‘responsibility’] TF(tokens) = {‘with’: 1, ‘great’: 2, ‘power’: 1, ‘comes’: 1, ‘responsibility’: 1}

<ol start="4">

 <li>We will now compute the <em>inverse document frequency </em>(IDF). While the TF gives an idea of the weight of a token within a document, the IDF is used to find its significance among the entire collection of documents (i.e. your reviews). One possible way of computing it is:</li>

</ol>

where <em>N </em>is the number of documents and <em>DF<sub>t </sub></em>is the <em>document frequency </em>of a token, i.e. the number of documents in which <em>t </em>appears at least once. As you can see, <em>IDF<sub>t </sub></em>∈ [0<em>,</em>log<em>N</em>]. Furthermore, a low value means that the token appears in the majority of the documents, hence its presence is not relevant to characterize any subset of them, whereas an high value indicates relevance for a few documents.

<ul>

 <li>Compute the DF for all of your tokens;</li>

 <li>Compute the IDF for all of your tokens;</li>

 <li>Try to sort the IDF values in ascending order. Which tokens (i.e. words) came to the top? Can you figure out why?</li>

</ul>

<ol start="5">

 <li>Compute the <em>TF</em>–<em>IDF</em>. Combine the definitions of term frequency (TF) and inverse document frequency (IDF), to produce a composite weight for each term in each document. The <em>TF</em>–<em>IDF </em>weighting scheme assigns to a term <em>t </em>a weight in the document <em>d </em>given by:</li>

</ol>

<em>TF</em>–<em>IDF<sub>t,d </sub></em>= <em>TF<sub>t,d </sub></em>× <em>IDF<sub>t                                                                                                               </sub></em>(1)

In other words, <em>TF</em>–<em>IDF<sub>t,d </sub></em>assigns to term <em>t </em>a weight in document <em>d </em>that is

<ul>

 <li>high when <em>t </em>occurs many times within a small number of documents;</li>

 <li>low when the term occurs fewer times in a document, or occurs in many documents (thus offering a less pronounced relevance signal);</li>

 <li>lowest when the term occurs in virtually all documents.</li>

</ul>

For dictionary terms that do not occur in a document, the weight is zero.

The suggested output structure is a list of dictionaries. Each dictionary represents a document and contains its tokens as key and weights as values:

TF_IDF = [{ “token_1”: weight_1, … “token_N”: weight_N}, { … }, … ]

<ol start="6">

 <li>(*) Sentiment analysis. Given a document, identify if it belongs to positive or negative comments calculating the similarity between the comments in the two groups.</li>

</ol>

How do we quantify the similarity between two documents exploiting <em>TF</em>–<em>IDF </em>vector representation?

A first attempt might consider the magnitude of the vector difference between two document vectors. This measure suffers from a drawback: two documents with very similar content can have a significant vector difference simply because one is much longer than the other. Thus the relative distributions of terms may be identical in the two documents, but the absolute term frequencies of one may be far larger. To compensate for the effect of document length, the standard way of quantifying the similarity between two documents d1 and d2 is to compute the <a href="https://en.wikipedia.org/wiki/Cosine_similarity">cosine similarity</a> of their vector representations <em>V</em><em><sup>~ </sup></em>(<em>d</em><sub>1</sub>) and <em>V</em><em><sup>~ </sup></em>(<em>d</em><sub>2</sub>):

(2)

To solve this simple task of sentiment analysis you have to:

<ul>

 <li>Take the vector representation of the first document in the collection. We will call it the <em>test document</em>.</li>

 <li>Identify the positive and negative comments and separate them into two groups.</li>

 <li>Compute the cosine similarity between the test document and all the other positive and negative comments separately.</li>

</ul>

Given that the vector representation for a document <em>V</em><em><sup>~ </sup></em>(<em>d<sub>n</sub></em>) is the dictionary of tokens with their weights, use the following functions to compute the cosine similarity: def norm(d):

<em>“””Compute the L2-norm of a vector representation.””” </em>return sum([ tf_idf**2 for t, tf_idf in d.items() ])**.5 def dot_product(d1, d2):

<em>“””Compute the dot product between two vector representations.””” </em>word_set = set(list(d1.keys()) + list(d2.keys()))

return sum([( d1.get(d, 0.0) * d2.get(d, 0.0)) for d in word_set ]) def cosine_similarity(d1, d2):

<em>“””</em>

<em>Compute the cosine similarity between documents d1 and d2.</em>

<em>Input: two dictionaries representing the TF-IDF vectors for documents d1 and d2.</em>

<em>Output: the cosine similarity.</em>

<em>“”” </em>return dot_product(d1, d2) / (norm(d1) * norm(d2))

<ul>

 <li>Assign the label (positive or negative) to the selected document analysing the mean similarity with respect to the group of positive comments and the mean similarity with respect to the group of negative comments.</li>

 <li>Are you able to identify the correct group? Why?</li>

 <li>Repeat the previous steps with other documents. Do you think the <em>TF</em>–<em>IDF </em>pre-processing is enough to perform a simple task of sentiment analysis?</li>

</ul>

<a href="#_ftnref1" name="_ftn1">[1]</a> Python docstrings are string literals that are not processed by the interpreter. Their main purpose is to describe how a piece of the project works (e.g. a module, a function, a class, etc.) in a concise way. Like many other Python stuff, you can find a further description on the <a href="https://www.python.org/dev/peps/pep-0257/">official documentation</a><a href="https://www.python.org/dev/peps/pep-0257/">.</a>