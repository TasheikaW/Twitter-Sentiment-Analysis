# Sentiment Analysis - Donald Trump Before and After Assassination Attempt
## Project Overview
This project aims to explore the Change in sentiments of the Past president of the United States following the recent assassination attempt. With this project as well
I am exploring and building on my Python knowledge and skills.

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
  - Tableau: used for creating a report
  - Powerpoint

## Data Mining
The API was used to pull the data from Tweets from X (Twitter). A YAML file was created to store the credentials. To connect to X, the API  was initialized followed by the query. The query 
contains the parameters for the tweet that needed to be mined from Twitter. Tweets from before and after the attempt were pulled. A snippet of the code is below.

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

### Word Cloud Generation
Word clouds are a straightforward tool for visualizing the most frequently used words in a collection of texts. In a word cloud, words are displayed in varying sizes, 
with larger words appearing more frequently in the text. In this section, The intention was to create a Word cloud with an Image of Donald Trump. However, due to the limited 
size of the data pulled A well-formed Image word cloud was not generated. (see notebook for the word cloud). 
The word cloud was generated in two different ways to broaden my learning.





