Yelp-Review-Sentiment-Analysis
==============================
Computing word positivity/negativity scores
To measure how much a word is associated with positive reviews (say),following quantity for each word w is computed.
Positivity(w) = log P(w in PositiveReviews) – log P(w in AllReviews) 
where P(•) denotes probability and log(x) is the natural logarithm of x.  The term P(w in PositiveReviews) means the probability that a word occurs, given we are looking at the set of positive reviews.  The term P(w in AllReviews) means the probability of a word occurring, given that we are looking at all reviews.Words with a high Positivity score are words that occur with much higher probability in positive reviews than all reviews in general.  The “all reviews” probability distribution over words is sometimes called the “background model”, and “positive reviews” probability distribution over words is called the “foreground model”. The same formula applies for negative reviews. Words that are more neutral, like ‘the’, that have similar probability given a positive review compared to any review,has a positivity score close to zero. 
The Positivity score is computed based on the set of reviews with >= 5 stars.
The Negativity score is computed based on the set of reviews with <= 2 stars.

Step 1.
I have used the LOAD command in pig to read in the reviews file using the provided JsonLoader syntax
R = LOAD 'yelp_review.json' USING JsonLoader('votes:map[], user_id:chararray, review_id:chararray, stars:int, date:chararray, text:chararray, type:chararray, business_id:chararray');
Text field from each review is then extracted along with the number of stars for the review, using the flatten and tokenize commands in pig in preparation for step 2.

Step 2.
Stream processing for the text created in step 1 is done by dividing it into three new streams:
1.     Text from all reviews
2.     Text from reviews with >= 5 stars  (the positive set)
3.     Text from reviews with <= 2 stars  (the negative set)
Output.  For each of these three streams, I computed the counts of all words appearing in those streams, and output three intermediate files called ‘output-step-1a’, ‘output-step-1b’ and ‘output-step-1c’ corresponding to the streams 1-3 above.

Step 3. 
Filter out (i.e. do not consider) any words occurring less than or equal to 1000 times in the “all reviews” stream were not considered and filtered out.The word counts of the remaining (high frequency) words in stream 1 (all reviews) were joined with the word counts stream 2 (positive) on the ‘word’ field to produce an output file :

Step 4.
Using the output from Step 3, positivity and negativity scores for all remaining words using the formula given above were computed after computing the total word count for each stream.
Output. Positive and negative word lists were written using STORE commands to create two output folders: one called ‘output-positive’ that holds words associated with the ‘positive’ reviews, and ‘output-negative’ for words associated with the ‘negative’ reviews.  These files are tab-delimited and have these columns:
Column 0: word
Column 1: Positivity(word)  for output-positive folder,  and Negativity(word) for the output-negative folder.
 
