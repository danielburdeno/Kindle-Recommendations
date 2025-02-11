![books](https://github.com/danielburdeno/Kindle-Recommendations/blob/main/Images/ebooks.jpg)

# Kindle eBook Recommendations: A Collaborative and Content Based Approach
Authors: Daniel Burdeno


## Overview
This project aims to build a two system approach to recommending Kindle eBook's to both existing reviewers and new users looking to find similar books. For existing reviewers a collaborative approach is taken by comparing similar reviewer profiles based on exisitng ratings. A content-based approach is taken in order to recommend books based on similar review text data and can be used by anyone.

Please see my [presentation](https://github.com/danielburdeno/Kindle-eBook-Recommendations/blob/main/Presentation.pdf) for a high level overview of the project.

## Business Problem
Currently eBooks are outsold by print books at about a 4 to 1 ratio. In 2020 there was 191 million eBooks sold. While Amazon holds over 70% of the market in eBooks via their kindle platform there is a large untapped potential for increasing eBook sales and promoting the use of eReaders compared to print. By utilzing quality recommendation systems Amazon can boost the interest and useablity of eBooks thus improving upon this market. The kindle platform and eBooks in general are incredidly accesibile for anyone with a tablet, smartphone, computer, or eReader. These eBooks can be immediatley purchased from a multitude of platforms and are able to read within minutes of purchase, which is far superior to obtaining a print book. This notion of real time purchase and useablily plays greater into Amazon's one click purchase philsophy. 

The kindle store is also full of cheap reads, with some eBooks even being free with certain subsripctions like prime and unlimited. A broad span of genres are available ranging from things like self-help books, cookbooks, and photography books to more traditional literature genres like Science Fiction & Fantasy and Romance novels. A final huge plus for the advocacy of eBooks is the ease in which readers can rate and reviews books they have either just read or already read. This can all be done via the same platform used to access and read the eBook (aka kindle). Ultimately this plays into the collection of more review and rating data wich in turn can attribute to better performing recommendations for each indiviudal user. A quality recommendation system can thus create a positive feedback loop that not only enhances itself but promotoes the increase in eBook sales across the board.

Sources: [1](https://www.tonerbuzz.com/blog/paper-books-vs-ebooks-statistics/) [2](https://www.statista.com/topics/1474/e-books/#:~:text=E%2Dbook%20sales%20in%20the,consistent%20annual%20increases%20since%202018.)

## Data Understanding
Data for this project was pulled from a compiled dataset of Amazon kindle store reviews and meta data in two seperate JSON files. The datasets can be found [here](https://nijianmo.github.io/amazon/index.html). I utlized the smaller dataset known as 5-core which contained data for products and reviewers with at least 5 entries. Data from the Kindle Store sets were used, both the 5-core review data and the full metadata file. Due to the large size of these datasets I downloaded them locally and saved to an external repository outside of github.

Data Instructions: The images below highlight the two compressed JSON files that I downloaded. Naviatged through the linked page requires an entry form of basic information (name, email) in order to begin downloads. Given the size of the two datasets allow several minutes for the downloads to occur. Once saved to your local drive (I placed the data one repository above the linked github repository) the JSON files can be loaded into jupyter notebooks via pandas (pd.read_json) using the compression='gz' and lines=True. Due to the size of the review text dataset be prepared for a large memory usage when loading it in.

Review Data:

![rev](https://github.com/danielburdeno/Kindle-eBook-Recommendations/blob/main/Images/InkedRevimg_LI.jpg) 

Meta Data:

 ![meta](https://github.com/danielburdeno/Kindle-eBook-Recommendations/blob/main/Images/InkedMetaimgv2_LI.jpg)

Once the data was loaded in, cleaned, and processed I saved seperate csv files locally as well, again due to size constraints. Several data sets including a reduced user dataframe and two meta data files were saved and pushed to github as seen in the repository structure. These files are needed for heroku to run the app that was developed. 

Citation: 

Justifying recommendations using distantly-labeled reviews and fined-grained aspects

Jianmo Ni, Jiacheng Li, Julian McAuley

Empirical Methods in Natural Language Processing (EMNLP), 2019 [pdf](https://cseweb.ucsd.edu//~jmcauley/pdfs/emnlp19a.pdf)

## Data Preparation
For data preparation and cleaning I primarily used built-in pandas methods and functions. The meta data was heavily cleaned to extract relevant information for this project including author, genre, page lengths, and kindle features. Meta data and review data was matched using 'asin' field, a unique product ID number generated by Amazon. As this project was focused soley on eBooks, I removed any products that did not match this. Review data was truncate to only included verified reviews, removing any potential paid sponsor reviews which could be skewed positively. 

For the collaborative filtering approach I removed any reviewer who had less than 5 book ratings in order to achieve quality results. A main limitation of collaborative filtering is the need for existing fleshed out user profiles. These reviews were left in for the content-based approach to provide more text data. In order to extract relevant information from text reviews I compiled all the reviews for each book into a new dataframe that could be used with nature language processing tools.

For a more in depth look please see my Data Preparation [notebook](https://github.com/danielburdeno/Kindle-Recommendations/blob/main/DataPrepFinal.ipynb).

## Methods and Models
I utilized two seperate approachs in order to produce a range of eBook recommendations: a collaborative filtering (CF) system and a content-based (CB) system. I choose to attempt a dual approach to providing recommendations so I could address a potential cold start problem. The CF system can only be used by existing book reviewers who have previously rated books, while the CB system can be used by anyone looking to find book recommendations, based on similar books. Once users have read and rated a set of books, this new reviewer data can be entered into the data pipeline and the CF model retrained to provide recommendations for these users. For each of these approachs a function was written that can return n-recommendations for eBooks. These functions were then used to produce a heroku app which can be deployed to provide quality recommendations.

### Collaborative Filtering:
The CF system was based on reviewer rating data to create 'user profiles' which could then be compared with one another. Similar users, based on prior eBook ratings, were then used to return the top recommended books by predicting an estimated rating. This approach is known as user to user. I iterated through several model algorithms and grid searchs before settling on my final model (SVD GS3). This model achieved my lowest Root Mean Squared Error, coming in at 0.781 rating (RMSE). 

![Surprise](https://github.com/danielburdeno/Kindle-Recommendations/blob/main/Images/Model_bar.png)

The CF system function takes in a reviewers unique Amazon ID and returns n-recommendations based on their profile in comparison to other users. This is demonstrated below. As one can see the CF system returns a variety of genres and eBook content and is based on prior reviewed books from the reviewer.

![collabex](https://github.com/danielburdeno/Kindle-eBook-Recommendations/blob/main/Images/collabexv3.png)

For a more in depth look at this process please see my Collaborative Filtering [notebook](https://github.com/danielburdeno/Kindle-Recommendations/blob/main/CollaborativeFiltering.ipynb).

### Content-Based:
The CB system was based on review text data which was compiled together for every book in my data set. Each book's review text acted as a document within the larger text corpus (all books). The Texthero package was utlized to clean and prepare review text for feature extraction. Texthero is a relatively new package that helps to streamline many natural language processing tools, and can be used to create custom cleaning pipelines for text data. Please see the [documentation](https://texthero.org/docs/getting-started) and [github](https://github.com/jbesomi/texthero) for more information and other uses. 

Content features for the corpus were then extracted from the cleaned review text data using the standard sklearn Tfidfvectorizor. Tf-idf scores were then combined with genre and print length data mined from the meta dataset. I purposely exlcuded using author as a feature to make sure the model woudln't just return book series or same authors as I wanted a larger variety of recommendations. The created function takes in a book title and n number of recommendations, provided by the user, and returns book recommendations. As expected from a CB system returned recommendations follow genre lines. 

I want to highlight below, using two eBook examples, how this recommendation system is able to distinguish between content within genres. Both of the inputted eBooks are within the Literature and Fiction Genre however contain vastly different content, one is a thriller and the other a romance. Recommendations for these books follow the same convention.

Thriller:

![ex1](https://github.com/danielburdeno/Kindle-eBook-Recommendations/blob/main/Images/content1.png) 

Romance:

![ex2](https://github.com/danielburdeno/Kindle-eBook-Recommendations/blob/main/Images/content2.png)

For a more in depth look at this process please see my Content-Based [notebook](https://github.com/danielburdeno/Kindle-eBook-Recommendations/blob/main/ContentBased.ipynb).

### App Development
In order to achieve real world access to the two recommendation sytsmes developed above I created an application intended for deployment utilizng streamlit. This app incorporated both functions created for the collaborative filtering and content-based system. Relevant data needed for the app was saved and pushed to github and can be found in the Data folder.

Deployment of the app was inhibited by the maximum memory that is allocated to free streamlit accounts. Given the inclusion of both recommender systems the developed app needs over 2gb of virtual memory to run. It can however be run locally by anyone who would like to fork and download this github repository. Once downloaded the appropriate environment can be activated using the requirements.txt file and then the app can be run locally by utilzing streamlit. In a local terminal type 'streamlit run app.py', which will open the application within a local host. A demonstration video of the app can be see below.

https://user-images.githubusercontent.com/92398209/151427313-bcfac107-b04b-4fe1-a882-9da8a99c17fe.mp4

The code for the app can found [here](https://github.com/danielburdeno/Kindle-eBook-Recommendations/blob/main/app.py).

## Conclusions
Collaborative Filtering:

In conclusion the collaborative filtering model produced a recommendation system that can predict estimated ratings and thus return top recommendations. It works by comparing individual reviewer profiles and determining similar users. Estimate ratings are then predicted based on latent factors between these profiles. The system performs very well for reviewers who have a wide range of ratings, spanning the scale of 1-5) but under performs with reviewers who only rated books at the top end of the scale. This inherently makes sense as these reviewer profiles do not adequately capture specific preferences across different eBooks.

One major limitation of this recommendation system in known as the cold start problem. The trained SVD model cannot predict ratings for new users who have not reviewed any eBooks within the dataset. The model needs an adequate number of prior review/rating history in order to form accurate reviewer profiles. New user information can however be entered into the pipeline and merged with existing data on which the model can be retrained (using the same hyperparameters). This is one of the major reasons I choose the SVD model over the SVD++ model given the large difference in training/fit times.

Users not in the amazon kindle review system can however utilize the content-based recommendation sytem developed in the adjacent notebook. This can recommend similar books based a single eBook entry which can then be used to start the formation of a reviewer profile, to ultimatley be fed into the collaborative system. These systems in tandem can help Amazon promote the use and sale of eBooks over print books by encouraging reviewers to rate and review books as the read and finish them. The kindle platform allows for seamless product recommendation, selection, purchase, and rating. New user data can be easily collected and the model retrained in order to capture these reviewers.

Content-Based:

In conclusion the content-based recommendation system works by taking in review text and vectorizing this into individual word features that can then be used to desrcribe each book as a large mutli-dimensional vector. A comparison of these vectors (using cosine similarity) is then used to return the top closest eBooks as recommendations for the user. It takes in a book title as the input. Currently this system is limited to eBooks within my dataset but it can easily be expanded to many other books as long as review text exists for them. Sites like goodreads contain a wealth of reviews and could be mined for further book entries.

A unique benefit of this content-based approach as compared to the collaborative approach seen here is its ability to be used by anyone looking to find similar books. It does not require any prior review or rating from any specifc user. These approachs can be used in tandem to provide robust and varied eBook recommendations for kindle users and would help to promote the sale of further eBooks. Implementation within the kindle system can be achieved with ease and this would also allow for continued data collection to help further refine the system. The more reviews and text that each book has the better the term vector will be able to distinquish and make accurate recommendations.

As expected this content-based system recommends books along convential genre lines, which can be seen as a beneift and also a slight limitation. An investigation into the exlcusion of genre might be warranted as well as an depth look at re-classifing genres within the dataset using unsupervised clustering. Further eBook review data is also needed to expand the range of possible eBook recommendations. New books are continually being published and their review data can be easily entered through the cleaning pipeline that utlizes texthero. This information can then be merged into the exisiting dataset thus expanding the potential recommendation pool.

Final thoughts: Utlizing a dual recommendation approach for eBooks, Amazon can play into their existing one-click purchase philosophy to help tap into the large potential market for eBooks on their kinlde platform.

### Next Steps
- More data (reviewers, ratings, books)
- Genre expansion & potential re-classify
- Amazon as host for app deployment

## Repository Structure
```
├── [Data]
│    ├── df_dtm.parquet
│    ├── df_user.csv
│    ├── meta5.csv
│    ├── meta_all.csv
├── [Images]
├── [Model]
├── .gitignore
├── CollaborativeFiltering.ipynb
├── ContentBased.ipynb
├── DataPrepFinal.ipynb
├── LICENSE
├── Presentation.pdf
├── Procfile
├── README.md
├── app.py
├── environment.yml
├── requirements.txt
└── setup.sh
```
