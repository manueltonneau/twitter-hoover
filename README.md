# twitter-hoover
Retrieve data from Twitter.

This is a general utility to retrieve data from Twiter, meant to be used from the command line. Below are some instructions on how to install it, and how to perform the most common tasks.

## How to install

Clone this repository into your local machine:

`git clone https://github.com/cmb-css/twitter-hoover.git`

You can then `cd` into the created directory and use `pip` to install locally:

`pip install -e .`

## Table of contents

* [Use with Twitter v1 API](#twitter_v1)
  * [Authorize the app (auth)](#v1_auth)
  * [Read a filtered stream into a local file (stream)](#v1_stream)
  * [Retrieve tweets from user timelines (timelines)](#v1_timelines)
  * [Retrieve followers (followers)](#v1_followers)
  * [Retrieve friends (friends)](#v1_friends)
  * [Convert to .csv (csv)](#v1_csv)
  * [Simplify JSON (simplify)](#v1_json)
  * [Extract YouTube videos (youtube)](#v1_youtube)
  * [Timeline anonymization](#v1_anonymization)

<a name="twitter_v1"/>

## Use with Twitter v1 API

<a name="v1_auth"/>

### Authorize the app (auth)

To authorize the app, first you need to create the local file `key-and-secret.txt`. This file should contain your private Twitter APP_KEY and APP_SECRET (one per line, nothing else). You can obrain these at https://developer.twitter.com. Then simply execute the following command:

`hoover auth`

You will be give a URL to perform authorization. After you authorize the app on this page, you will be given a PIN code, that you should then insert in the command line (there will be a prompt asking you for this). Both OAUTH_TOKEN and OAUTH_TOKEN_SECRET will be written to the local file `auth.txt`. This file will then be available for other commands to transparently perform authentication.

<a name="v1_stream"/>

### Read a filtered stream into a local file (stream)

`hoover --infile <keywords_file> --outfile <outfile> stream`

JSON data will be written to `<outfile>`. The stream will be filtered by the keywords or hashtags found in `<keywords_file>` (one keyword/hashtag per line).

Tweets are saved one per line, in the form of the full JSON object reveived from the Twitter API.

<a name="v1_timelines"/>

### Retrieve tweets from user timelines (timelines)

This allows for the retrieval of tweets from the timelines of the specified users. The simplest way to use it is to specify either a screen name or user id:

`hoover --user <screen_name or user_id> --outfile <filename> timelines`

You can also provide a list of users as an input file (one user_id per line):

`hoover --infile <users> --outdir <outdir> timelines`

In this case, the input file is interpreted as a .csv file. It can have a header or not (this is automatically detected), and the first row is assumed to contain the user ids for which data should be collected. There can be an arbitrary number of other rows, they are ignored here. The outputs of the `folowers` and `friends` commands, for example, are valid input files for this commmand. One file will be created for each user, inside `outdir`, and its name will be the respective user id with the extension `.json`.


Tweets are saved one per line, in the form of the full JSON object reveived from the Twitter API.

<a name="v1_followers"/>

### Retrieve followers (followers)

This allows for the retrieval of information on the followers of the specified users. The simplest way to use it is to specify either a screen name or user id:

`hoover --user <screen_name or user_id> --outfile <filename> followers`

You can also provide a list of users as an input file (one user_id per line):

`hoover --infile <users> --outdir <outdir> followers`

In this case, the input file is interpreted as a .csv file. It can have a header or not (this is automatically detected), and the first row is assumed to contain the user ids for which data should be collected. There can be an arbitrary number of other rows, they are ignored here. The outputs of the `folowers` and `friends` commands, for example, are valid input files for this commmand. One file will be created for each user, inside `outdir`, and its name will follow the template: `<user_id>-followers.csv`.


The output file(s) are .csv files, with each line containing information about one user. Its columns are: id, screen_name, name, location, protected, verified, followers_count, friends_count, listed_count, favourites_count, statuses_count, created_at, created_ts.

<a name="v1_friends"/>

### Retrieve friends (friends)

This allows for the retrieval of information on the friends of the specified users. The simplest way to use it is to specify either a screen name or user id:

`hoover --user <screen_name or user_id> --outfile <filename> friends`

You can also provide a list of users as an input file (one user_id per line):

`hoover --infile <users> --outdir <outdir> friends`

In this case, the input file is interpreted as a .csv file. It can have a header or not (this is automatically detected), and the first row is assumed to contain the user ids for which data should be collected. There can be an arbitrary number of other rows, they are ignored here. The outputs of the `folowers` and `friends` commands, for example, are valid input files for this commmand. One file will be created for each user, inside `outdir`, and its name will follow the template: `<user_id>-friends.csv`.


The output file(s) are .csv files, with each line containing information about one user. Its columns are: id, screen_name, name, location, protected, verified, followers_count, friends_count, listed_count, favourites_count, statuses_count, created_at, created_ts.

<a name="v1_csv"/>

### Convert to .csv (csv)

This command converts tweets encoded as raw JSON objets (as received from the Twitter API) to .csv files. The simplest way to use it is to specify an input and an output file:

`hoover --infile <input file with JSON objects> --outfile <output .csv file> [--type <csv_type>] csv`

You can also provide input and output directories:

`hoover --indir <directory with input .json files> --outdir <directory with output .csv files> [--type <csv_type>] csv`

In this case, .json file names are assumed to be the user ids of the author of the tweets they contain. These user ids will be used to automatically generate the names of the output .csv files.

There are several types of .csv files that can be generated. We list them here, by the name that can be specified with the optioanl `--type` parameter:

* *all*: all the tweets (including replies, retweets and quotes)
* *tweets*: only simple tweets (no replies, retweets or quotes)
* *replies*: only replies
* *retweets*: only retweets
* *quotes*: only quotes
* *hashtags*:  all the hastags contained in the tweets, including number of occurrences
* *mentions*: all the mentions contained in the tweets, including number of occurrences

If `--type` is not specified, all of the above outputs are generated. Output files that have automatically generated names (using `--indir` and `--outdir`), will be identified with one of the above csv types as a suffix. To illustrate, if there is a file called `4135510295844.json` on the input directory, then  the following files will be generated on the output directory:

* 4135510295844-all.csv
* 4135510295844-tweets.csv
* 4135510295844-replies.csv
* 4135510295844-retweets.csv
* 4135510295844-quotes.csv
* 4135510295844-hashtags.csv
* 4135510295844-mentions.csv

Or only one of them, if `--type` is specified.

All the .csv files that directly list tweets (the first 5 types above) have the following columns: 'created_at', 'timestamp', 'id', 'text', 'retweet_count', 'favorite_count' and 'lang'. If the user who created the tweet is not speficied in the file name (this is the case when one input and one output file are directly specified, as in the first example of invocation of the command above), then two extra columns are present: 'user_id' and 'user_screen_name'. Files that contain replies include the columns: 'in_reply_to_status_id', 'in_reply_to_user_id' and 'in_reply_to_screen_name'. Files that contin tweets quoting parent tweets (retweets and quotes) include the column 'quoted_text'. Files  that contain retweets include the fileds: 'retweeted_id', 'retweeted_user_id' and 'retweeted_user_screen_name'. Files that contain quotes include the fileds: 'quoted_id', 'quoted_user_id' and 'quoted_user_screen_name'.

<a name="v1_json"/>

### Simplify JSON (simplify)

JSON objects produced by the JSON API to represent Tweets can be a bit complex. This converts them to a flatter, simpler JSON schema that might be simpler to user from many purposes:

`hoover --infile <input file with Tweet JSON objects> --outfile <output .json file> simplify`

<a name="v1_youtube"/>

### Extract YouTube videos (youtube)

This command extracts all the URLs corresponding to YouTube videos from a list of Tweets in API JSON format (e.g. the ones outputted by the `stream` or `timelines` commands).

`hoover --infile <input file with Tweets JSON objects> --outfile <output .csv file> youtube`


The output files is a .csv file with two columns: video URL and number of occurrences.

<a name="v1_anonymization"/>

### Timeline anonymization

With the present tool, there is a possibility to anonymize user timelines when using the Twitter v1 API. This anonymization can be performed either during data collection or ex-post.
During anonymization, some entries are dropped, others are anonymized and the rest is kept as is. For details on treatment for each entry, please refer to the `v1_tweet_object`, `v1_user_object` and `v1_entities_object` sheets of this [Google Sheets](https://docs.google.com/spreadsheets/d/11GGVrfr0OrrBFR5qSUE6Tv97l2ahYNj9flNdXabEcis/edit?usp=sharing). 


To collect anonymized timelines, use the following command:

`hoover --infile <users> --outdir <outdir> --anon 1 timelines`

The default path of the database containing the encryption keys is set to `/home/socsemics/anon`. If the database is located somewhere else, run:

`hoover --infile <users> --outdir <outdir> --anon 1 --anon_db_folder_path <path> timelines` 
where `<path>` is the path where this encryption key database is located.

To anonymize timelines that were previously collected, run:

`python3 ../hoover/anon/anonymize_v1.py --input_path PATH_TO_TIMELINE_FOLDER_TO_ANONYMIZE`

This script gives the possibility to resume an anonymization that may have crashed with the `--resume` parameter. For a detailed description of all parameters, please refer to the script.




