# Lab session #2: Doors in the Cloud
In this Lab session, we are going to discuss the overall structure of a tweet and how to pre-process the text before going into a more interesting analysis in the next Lab session. In particular, we are going to see how tokenization, while being a well-understood problem, can get tricky dealing with Twitter data. Before this, we need to install A Python Development Environment which will be very helpful for this and future sessions.

* [Pre-lab howemork 2](#Prelab)
   * [Homework 2.1: Registering Our App on Twitter](#Homework1)
* [Tasks for Lab session #2](#Tasks)
   * [Task 2.1: Geting Started with NLTK](#NLTK)
   * [Task 2.2: Getting Started with `tweepy`](#tweepy)
   * [Task 2.3: Tweet pre-processing](#preproc)

<a name="Prelab"/>

#  Pre-lab homework 2

<a name="Homework1"/>

## Homework 2.1: Registering Our App on Twitter
Cloud applications are characterized by an increased focus on user participation and content creation, but also by a profound interaction and interconnection of applications sharing content from different types of services to integrate multiple systems. This scenario is possible, without a doubt,  thanks to the rise of “Application Programming Interfaces” (API). 

An API, or **Application Programming Interface**, provides a way for computer systems to interact with each other. There are many types of APIs. Every programming language has a built-in API that it is used to write programs. For instance, you have studied in previous courses that operating systems themselves have APIs used by applications to interact with files or display text on the screen.

Since this course focuses on studying Cloud technologies, we are going to concentrate on APIs built on top of web technologies such as HTTP. We will refer to this type of API as `web API`: an interface used by either web servers or web browsers. Such APIs are extensively used for the development of web applications and work at either the server end as well as at the client end. 

Web APIs are a fundamental component of nowadays Cloud era. Many cloud applications provide an API that allows developers to integrate their code to interact with them, taking advantage of the services' functionality for their apps.

Twitter API is one example among the vast number of available APIs. Twitter API offers the possibility of accessing all tweets made by any user, all tweets containing a particular term or combination of terms, all tweets about a given topic during a date range, and many more features.

To set up our Lab session #2 to be able to access Twitter data, there are some preliminary steps. Twitter implements OAuth (called Open Authorization) as its standard authentication mechanism, and to have access to Twitter data programmatically; we need to create an app that interacts with the Twitter API. 

There are four primary identifiers we need to have to start an OAuth workflow: consumer key, consumer secret, access token, and access token secret. Good news, from developer's perspective, is that the Python ecosystem has already well-established libraries for most social media platforms, which come with an implementation of that authentication process.

Follow the instructions at https://developer.twitter.com/en/docs/platform-overview to log-in to Twitter and enroll a new application.

 <p align="center"><img src="./images/Lab02-twitterAPI.png " alt="PyCharm Env" title="PyCharm Env"/></p>

> **Warning**: these are application settings should always be kept private. Do not push the credentials to the git repository. You can use the environment variables, amongst other methods, to provide the configuration data to the applications.

Note that you need a Twitter account to log in, create an app, and get these credentials.

# See some Python sample code here:

https://github.com/twitterdev/Twitter-API-v2-sample-code


<a name="Tasks"/>

#  Tasks for Lab session #2

<a name="NLTK"/>

## Task 2.1: Getting Started with NLTK
One of the most popular packages in Python for NLP (Natural Language Processing) is Natural Language Toolkit ([NLTK](http://www.nltk.org). This toolkit provides a friendly interface for many of the basic NLP tasks, as well as lexical resources and linguistic data.

Tokenization is one of the most basic, yet most important, steps in text analysis required for the following task. The purpose of tokenization is to split a stream of text into smaller units called tokens, usually words or phrases. For this purpose we are going to use the [NLTK](http://www.nltk.org) Python Natural Language Processing Toolkit:

```python
import nltk
```
Please, check [https://www.nltk.org/data.html](https://www.nltk.org/data.html) if you have problems installing nltk packages.

A difference between NLTK and many other packages is that this framework also comes with linguistic data for specific tasks. Such data is not included in the default installation, due to its big size, and requires a separate download. Therefore, after importing NLTK, we need to download NLTK Data which includes a lot of corpora, grammars, models, and more resources. You can find the complete nltk data list [here](http://nltk.org/nltk_data/). You can download all nltk resources using `nltk.download('all')`  beware that it takes ~3.5G. For English text, we could use `nltk.download('punkt')` to download the NLTK data package that includes a pre-trained tokenizer for English.

Let’s see the example using the NLTK to tokenize the book [First Contact with TensorFlow](http://www.jorditorres.org/Tensorflow) [`FirstContactWithTensorFlow.txt`](./FirstContactWithTensorFlow.txt) available for download at this repository and outputs the ten most common words in the book.

```python
import nltk
nltk.download('punkt') 
import re

from collections import Counter

def get_tokens():
   with open('FirstContactWithTensorFlow.txt', 'r') as tf:
    text = tf.read()
    tokens = nltk.word_tokenize(text)
    return tokens

tokens = get_tokens()
count = Counter(tokens)
print (count.most_common(10))
```

### Task 2.1.1: Word Count 1
Create a file named `WordCountTensorFlow_1.py`, that computes and prints the 10 most common words in the book. Add one more line to print the total number of words of the book.

**Q211: Copy and paste the output to `README.md`, a file that you will add to your repository.**

### Task 2.1.2: Remove punctuation
We can remove the punctuation, inside get_tokens(), by applying a regular expression:

```python
    lowers = text.lower()
    no_punctuation = re.sub(r'[^\w\s]','',lowers)
    tokens = nltk.word_tokenize(no_punctuation)
```
Create a new file named `WordCountTensorFlow_2.py` that computes and prints the 10 most common words without punctuation characters as well as the total number of words remaining.

**Q212: Copy and paste the output to `README.md`, a file that you will add to your repository.**
    
### Task 2.1.3: Stop Words

**Q213a: Why "Tensorflow" is not the most frequent word? Which are the Stop Words?** Include your answers in `README.md`.

When we work with text mining applications, we often hear of the term “Stop Word Removal." We can do it using the same `nltk` package: 

```python
from nltk.corpus import stopwords
nltk.download('stopwords') 

tokens = get_tokens()
# the lambda expression below this comment
# stores stopwords in a variable for eficiency: 
# it avoids retrieving them from ntlk for each iteration
sw = stopwords.words('english')
filtered = [w for w in tokens if not w in sw]
count = Counter(filtered)
```

Create a new file named `WordCountTensorFlow_3.py` holding the code (and the comments) that computes and prints the total number of words remaining and the ten most common words after removing the stop words.

**Q213b: Copy and paste the output to `README.md`.**

Now, it makes more sense, right? "TensorFlow" is the most common word!

<a name="tweepy"/>

## Task 2.2: Getting Started with `tweepy`

In this task, we are going to use the `tweepy` package as a tool to access Twitter data in a somewhat straightforward way using Python. There are different types of data that we can collect. However, we are going to focus on the “tweet” object.

### The Twitter API

As [homework](#Homework1) we already registered Our App on Twitter to set up our project and access Twitter data. However, the Twitter API limits access to applications. You can find more details at [the official documentation](https://dev.twitter.com/rest/public/rate-limiting). It's also important to consider that [different APIs have different rate limits](https://dev.twitter.com/rest/public/rate-limiting). The implications of hitting the API limits is that Twitter returns an error message rather than the data we require. Moreover, if we continue performing more requests to the API, the time required to obtain regular access again increases, as Twitter could flag us as potential abusers. If our application needs many API requests we can use the `time` module (`time.sleep()` function). 

Another relevant thing to know, before we begin, is that we have two classes of APIs: **REST APIs** and **Streaming API**. All REST APIs only allow you to go back in time (tweets already published). Often these APIs limit the number of tweets you can retrieve, not just regarding rate limits as we mentioned, but also regarding a period. In fact, it's usually possible to go back in time up to approximately one week. Also, another aspect to consider regarding the REST API is that it does not guarantee to provide all tweets published on Twitter. 

On the other hand, the Streaming API looks into the future: we can retrieve all tweets matching our filtering criteria as they become available. The Streaming API is useful when we want to filter a particular keyword and download a massive amount of tweets about it. On the other hand, REST API is useful when we want to search for tweets authored by a specific user, or we want to access our timeline.

### Task 2.2.1: Accessing your twitter account information

Twitter provides a programming language independent API as a [RESTfull webservice](https://en.wikipedia.org/wiki/Representational_state_transfer). Such API allows anybody to interact with the Main Model classes of Twitter: `Tweets`, `Users`, `Entities` and `Places`. 

Since we are using Python, to interact with the Twitter APIs, we need a Python client that implements the different calls to the RESTfull API itself. There are several options, as we can see at the [official documentation](https://dev.twitter.com/resources/twitter-libraries). We are going to choose  [Tweepy](https://docs.tweepy.org/en/stable/) for this lab.

One easy way to install the latest version is by using pip/easy_install to pull it from [PyPI](https://pypi.python.org/pypi) to your local directory:

```bash
_$ pip install tweepy
```

Tweepy is also available from [conda forge](https://conda-forge.org/feedstocks/):

```bash
_$ conda install -c conda-forge tweepy
```


Create a file named `Twitter_1.py` and include the code to access Twitter on our behalf. We need to use the OAuth interface:

```python
import os
import tweepy

client = tweepy.Client(bearer_token=os.environ['BEARER_TOKEN'])
```

To be sure that everything is correctly installed print the main information of Google account:

```python
user = client.get_user(username='Google')
print('Name: ' + user.data.name)

```
The `client` variable is now our entry point for most of the operations with Twitter. 

Tweepy provides Python access to the well documented [**REST Twitter API**](https://docs.tweepy.org/en/stable/client.html). In the referred documentation there is a list of REST calls and extensive information on all the key areas where developers typically engage with the Twitter platform.

Using tweepy, it's possible to retrieve objects of any type and use any method that the official Twitter API offers. 

Before you run the program, you need to instantiate the process environment variables that contain your credentials. **Remember** that every time you close the session, you will need to set them again. Use your Bash shell `.bashrc` to automate this process. (Create a FILE.bat in MS-Windows or use the equivalent init files for other Unix shells).

```bash
_$ export BEARER_TOKEN="oEeC30R9-YOUR-OWN-DATA-y4YlY94KIZ"
```

If you want to run/debug your program with PyCharm you can setup the environment variables at the run/debug configuration:

 <p align="center"><img src="./images/Lab02-PyCharmEnv.png " alt="PyCharm Env" title="PyCharm Env"/></p>

**Q221: Is the data printed correctly?** Add your answers to `README.md`.

**NOTE:** You are encouraged to explore alternative ways of detaching private configuration details form the application code. Check [configparser](https://docs.python.org/3.4/library/configparser.html) or [decouple](https://pypi.python.org/pypi/python-decouple) as options.

### Task 2.2.2: Accessing Tweets
In order to search Tweets from the last 7 days, you can use the search_recent_tweets function available in Tweepy. You will have to pass it a [search query](https://github.com/twitterdev/getting-started-with-the-twitter-api-v2-for-academic-research/blob/main/modules/5-how-to-write-search-queries.md) to specify the data that you are looking for. In the example below, we are searching for Tweets from the last days from the Twitter handle suhemparack and we are excluding retweets using `-is:retweet`.

By default, in your response, you will only get the Tweet ID and Tweet text for each Tweet. If you need additional Tweet fields such as [context_annotations](https://developer.twitter.com/en/docs/twitter-api/annotations/overview), created_at (the time the tweet was created) etc., you can specifiy those fields using the tweet_fields parameter, as shown in the example below. Learn more about available fields [here](https://developer.twitter.com/en/docs/twitter-api/fields). By default, a request returns 10 Tweets. If you want more than 10 Tweets per request, you can specify that using the max_results parameter. The maximum Tweets per request is 100.

```python
query = 'from:Google -is:retweet'

tweets = client.search_recent_tweets(query=query, tweet_fields=['context_annotations', 'created_at'], max_results=100)

for tweet in tweets.data:
    print(tweet.text)
    if len(tweet.context_annotations) > 0:
        print(tweet.context_annotations)
```

As a conclusion, notice that using `tweepy` we can quickly collect all the tweet information and store it in JSON format, reasonably easy to convert into different data models (many storage systems provide import feature).

Create a file named `Twitter_2.py` and use the previous API presented to obtain information about your tweets.

**Q22: Keep track of your executions and comments at   `README.md`.**

<a name="preproc"/>

## Task 2.3:  Tweet pre-processing
The code used in this Lab session is using part of the work done by [Marco Bonzanini](https://marcobonzanini.com/2015/03/02/mining-twitter-data-with-python-part-1/). As Marco indicates, it is far from perfect, but it’s a good starting point to become aware of the complexity of the problem, and it is reasonably easy to extend.

We are going to focus on looking for the text of a tweet and breaking it down into words. While tokenization is a well-understood problem with several out-of-the-box solutions from popular libraries, Twitter data pose some challenges because of the nature of the language used.

Let’s see an example using the NLTK package previously used to tokenize a fictitious tweet:

```python
from nltk.tokenize import word_tokenize

tweet = 'RT @JordiTorresBCN: just an example! :D http://JordiTorres.Barcelona #masterMEI'

print(word_tokenize(tweet))
```
You might notice some peculiarities of Twitter that a general-purpose English tokenizer such as the one from NLTK can't capture: @-mentions, emoticons, URLs, and #hash-tags stay unrecognized as single tokens. Right?

Using some code borrowed from [Marco Bonzanini](https://marcobonzanini.com/2015/03/02/mining-twitter-data-with-python-part-1/) we could consider these aspects of the language (A former student of this course, [Cédric Bhihe](https://www.linkedin.com/in/cedricbhihe/), suggested [this alternative code](CedricTokenizer.py)).  

```python
import re
 
emoticons_str = r"""
    (?:
        [:=;] # Eyes
        [oO\-]? # Nose (optional)
        [D\)\]\(\]/\\OpP] # Mouth
    )"""
 
regex_str = [
    emoticons_str,
    r'<[^>]+>', # HTML tags
    r'(?:@[\w_]+)', # @-mentions
    r"(?:\#+[\w_]+[\w\'_\-]*[\w_]+)", # hash-tags
    r'http[s]?://(?:[a-z]|[0-9]|[$-_@.&+]|[!*\(\),]|(?:%[0-9a-f][0-9a-f]))+', # URLs
 
    r'(?:(?:\d+,?)+(?:\.?\d+)?)', # numbers
    r"(?:[a-z][a-z'\-_]+[a-z])", # words with - and '
    r'(?:[\w_]+)', # other words
    r'(?:\S)' # anything else
]
    
tokens_re = re.compile(r'('+'|'.join(regex_str)+')', re.VERBOSE | re.IGNORECASE)
emoticon_re = re.compile(r'^'+emoticons_str+'$', re.VERBOSE | re.IGNORECASE)
 
def tokenize(s):
    return tokens_re.findall(s)
 
def preprocess(s, lowercase=False):
    tokens = tokenize(s)
    if lowercase:
        tokens = [token if emoticon_re.search(token) else token.lower() for token in tokens]
    return tokens
 
tweet = 'RT @JordiTorresBCN: just an example! :D http://JordiTorres.Barcelona #masterMEI'
print(preprocess(tweet))
```

As you can see, @-mentions, URLs, and #hash-tags are now individual tokens. This tokenizer gives you a general idea of how you can tokenize twitter text using regular expressions (regexp), which is a common choice for this type of problem. 

With the previous essential tokenizer code, some particular types of tokens are not captured but split into several other tokens. To overcome this problem, you can improve the regular expressions, or apply more sophisticated techniques such as [*Named Entity Recognition*](https://en.wikipedia.org/wiki/Named-entity_recognition).

In this example, regular expressions are compiled with the flags re.VERBOSE, to ignore spaces in the regexp (see the multi-line emoticons regexp), and re.IGNORECASE to match both upper and lowercase text. The tokenize() function catches all the tokens in a string and returns them as a list. preprocess() uses tokenize() to pre-process the string: in this case, we only add a lowercasing feature for all the tokens that are not emoticons (e.g., :D doesn’t become :d).

Keep track of the execution examining ten different tweets extracted using tweepy, as shown above. In this initial exercise using Twitter, if you don't want to have extra problems with *special characters* filter tweets *in the English language*.

**Q23: Add the code to `Twitter_2.py` and your comments to `README.md`.**

We are now ready for next Lab session where we will be mining streaming Twitter data.

**Q24: How long have you been working on this session? What have been the main difficulties you have faced and how have you solved them?** Add your answers to `README.md`.


# How to submit this assignment:

Use the **private** repo named *https://github.com/CCBDA-UPC/2023-2-xx*. It needs to have, at least, two files `README.md` with your responses to the above questions and `authors.json` with both members email addresses:

```json5
{
  "authors": [
    "FIRSTNAME1.LASTNAME1@estudiantat.upc.edu",
    "FIRSTNAME2.LASTNAME2@estudiantat.upc.edu"
  ]
}
```

Make sure that you have updated your local GitHub repository (using the `git`commands `add`, `commit` and `push`) with all the files generated during this session. 

**Before the deadline**, all team members shall push their responses to their private *https://github.com/CCBDA-UPC/2023-2-xx* repository.
