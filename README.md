# Sentiment Analysis
## Sentiment sorrounding Donald Trump Before and After Assassination Attempt

## Project Overview
The aim of this project is to build on my data analysis skills by exploring pyhton. To do this, I decided to focus on a news worthy event to perform a sentiment analysis. 
This project aims to explore the Change in sentiments of the past president of the United States following the recent assassination attempt. I aim to see what the sentiment around him was like before the event, and if there are any changes since. 

### Data Sources
Twitter: Data was pulled from X (Twitter) using the Twitter API. A YAML file with the credentials needed to connect to the Twitter API was created. 
The format of the YAML file was:
 - search_tweets_v2:
    - endpoint:  https://api.twitter.com/2/tweets/search/all
    - consumer_key: <CONSUMER_KEY>
    - consumer_secret: <CONSUMER_SECRET>
    - bearer_token: <BEARER_TOKEN>

  ## Tools:
  - Jyputer notebook: used for Data mining, Data cleaning, and Analysis
  - 
## Data Mining
The API method was used to pull the data from X (Twitter). A YAML file was created to store the X credentials. To connect to X, the API and the query was initialized after which is was implemented. The query contains the parameters for the tweet that needed to be mined from Twitter. Tweets from before and after the attempt were pulled. A snippet of the code is below.

``` python
search_args = tw.load_credentials("search_tweets_v2.yaml",
                                     yaml_key="search_tweets_v2",
                                     env_overwrite=False
query_before = tw.gen_request_parameters(
    query ='("Donald Trump" OR #DonaldTrump) -is: retweet -is: reply lang: en',
    results_per_call=100,
    start_time = "2024-07-12", 
    end_time = "2024-07-13",
    tweet_fields="id,created_at,text,author_id,public_metrics",
    granularity=None
)
```
To implement the query the following code was used:
``` python
tweets_before = tw.collect_results(
    query_before,
    max_tweets=1951,
    #passing the credentials
    result_stream_args=search_args
)
```
Once the tweets were pulled from X, They were saved in 2 data frames. One for the tweets before the incident, and one for after the incident. 
With the data frame, The stats for the public metrics had to be their columns. To save the subset of data with public metrics, a function was defined 
and used to pull out the parameters from the public metrics, then save it into a new data frame.

``` python

data_frames = []
for tweet in tweets_before:
    for data in tweet['data']:
        # Extract public metrics
        metrics = data['public_metrics']
        # Create a dictionary with the desired fields
        tweet_data = {
            'id': data['id'],
            'created_at': data['created_at'],
            'text': data['text'],
            'retweet_count': metrics['retweet_count'],
            'reply_count': metrics['reply_count'],
            'like_count': metrics['like_count'],
            'quote_count': metrics['quote_count'],
            'bookmark_count': metrics.get('bookmark_count', 0),  # Use get to handle missing keys
            'impression_count': metrics.get('impression_count', 0)
        }
        # Append the tweet data dictionary to the list
        data_frames.append(tweet_data)

# Create a DataFrame from the list of dictionaries
df_tweets_before = pd.DataFrame(data_frames)
```
# Data Cleaning
Data cleaning is a crucial step in data analysis. It involves preparing the raw data for analysis by handling missing values, removing duplicates, and correcting data types. Clean data ensures accurate and reliable analysis results.
Here are the different processes used to clean the tweets :

- Removing unwanted characters using Regex expressions
- Removing Stop Words
- Lemmatization

# Data Analysis
In the data analysis section, multiple exploration was done. 

- Word Cloud generation
- Sentiment Analysis
- Visualization

### Word Cloud Generation
Word clouds are a straightforward tool for visualizing the most frequently used words in a collection of texts. In a word cloud, words are displayed in varying sizes, 
with larger words appearing more frequently in the text. In this section, The intention was to create a Word cloud with an Image of Donald Trump. However, due to the limited 
size of the data pulled A well-formed Image word cloud was not generated. 

![download](https://github.com/user-attachments/assets/a8279e65-03af-42b8-9002-2989314f86fb)

The word cloud was generated in two different ways to broaden my learning.(see notebook for the well-formed word cloud)

### Sentiment Analysis
Sentiment analysis involves classifying the sentiment of text data.  VADER (Valence Aware Dictionary and sEntiment Reasoner) sentiment analysis tool was used, which is well-suited for analyzing social media text. The Nrclex libray was also used to calculate more complex emotions such as Joy, sadness etc.
``` python
#import the library
from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer

#calculate the negative, positive, neutral and compound scores, plus verbal evaluation
def sentiment_vader(tweets):

    # Create a SentimentIntensityAnalyzer object.
    sid = SentimentIntensityAnalyzer()

    sentiment_dict = sid.polarity_scores(tweets)
    negative = sentiment_dict['neg']
    neutral = sentiment_dict['neu']
    positive = sentiment_dict['pos']
    compound = sentiment_dict['compound']

    if sentiment_dict['compound'] >= 0.05 :
        overall_sentiment = "Positive"

    elif sentiment_dict['compound'] <= - 0.05 :
        overall_sentiment = "Negative"

    else :
        overall_sentiment = "Neutral"

    return negative, neutral, positive, compound, overall_sentiment

#Implement it on each tweet individually

df_tweets["sentiment_vader"] = df_tweets["text_stemmed_porter"].apply(lambda tweet: sentiment_vader(tweet))

df_vader_sentiments = pd.DataFrame()
df_vader_sentiments[["negative_sentiment", "neutral_sentiment", "positive sentiment", "compound_sentiment", "overall_sentiment"]] = \
    pd.DataFrame(df_tweets['sentiment_vader'].tolist())

#Plot the distribution of positive, negative and neutral graphs
df_vader_sentiments["overall_sentiment"].value_counts().plot.bar()
```

# Result
 ## Overall
The results show that from the data set pulled, overall more negative sentiments were being expressed.
![newplot](https://github.com/user-attachments/assets/e9424223-177b-40ee-bed8-de23d06ae2d2)

A breakdown of sentiments by more complex emotion was also done
Outside of negative and positive emotions, we see that surprise was the next highest. I think this was high as no one saw this coming and they were taken aback by what the witness

![newplot (1)](https://github.com/user-attachments/assets/1211a598-e906-4d58-b856-7644ebbb15c4)

## Comparison of Before and After

To assess the sentiment before and after, a bar plot was generated. Here we can see 2 dates ( This is due to the limited amount of tweets that I had access to). One date was before the attempt and the other was after. We can see that before the attempt there was a balance between negative, positive, and neutral sentiment. However, after the attempt, there was a jump in negative sentiment around Donald Trump.

![download (1)](https://github.com/user-attachments/assets/370b9f13-9373-4a2d-9842-f1e2e7fee4ee)

With the more complex  emotion classification, we see how the emotions change before and after the assassination attempt. We see increases in emotions such as Fear and Anticipation.
### Before
![newplot (2)](https://github.com/user-attachments/assets/8cbe104f-35e3-480b-95c4-18f1c90e5c14)
### After
![newplot (3)](https://github.com/user-attachments/assets/8289f9f7-eb2c-4e30-9f55-39bb74014dbb)

I also wanted to look at the public metrics on the tweets classified by sentiments. These metrics included:
- likes
- replies
- quotes
- retweet
Based on the data It was determined that tweets that convey positive sentiments receive higher metrics than those that portray negative sentiments. 
![download (2)](https://github.com/user-attachments/assets/1fbcdb4c-de2c-4e37-9be4-6e3df9dc0567)


# Limitation
The biggest limitation of this project was the size of the dataset.
Due to changes made to X (Twitter) developer, The number of tweets accessible  was limited. 

# Reference
- [Emotion classification using NRC Lexicon in Python](https://www.geeksforgeeks.org/emotion-classification-using-nrc-lexicon-in-python/)
- [Getting Started with Twitter's Academic Track API](https://muhark.github.io/python/scraping/tutorial/2021/03/25/getting-started-with-academic-twitter.html)


