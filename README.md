# FinalProject

Made with Israel Cohen (another student) and supervised by prof. Ron Unger.

background:
Dr. Itay Koren from Bar-Ilan has conducted a biological reaserch and made data of 22,000 peptides - all 23-anino-acid-long, and a regressive stability score in range (0,6). He published his results here https://www.cell.com/cell/pdf/S0092-8674(18)30521-X.pdf
Based on this data, our project was constructing a machine that would learn to anticipate the stability score of peptides

getting started:
We decided to start with binary classification of 25% most stable and 25% least stable peptides, see how it goes, and later advance to regression.
Our initial intention for the binary classification was to start with MLP, get accuracy of around 60%, than move forward to RNN, becuse we assumed the order of AAs in the sequenced plays a central role.

too good MLP results:
To consider the order of AA within the sequence, we used one-hot-encoding: there are 20 different AA, so each AA in the peptide is ancoded to vector of 20, and overall peptides of 23 AA encoded to vector of 460.
To our great surprise, with the simple MLP we hit 91% accuracy *this is mainMLP.py*!
At first, we suspected the results are to good to be true.
Indeed, we noticed our data contains peptides that are very similar to one another, and some even identical (turned out the reasarch got two different results for the same peptides, it happens). Using cd-hit, we filtered the data from peptides that have 85% or more identical AAs. The results remained the same, so we reduced it to 75%, and even 40%, with no significant change. We also tried using random half of the data (the remaining data after 40% filtering was about half of the original) and got similar results. We concluded the problem we tried to solve - binary classification of 25% most stable and 25% least stable - was too easy, though it's weird that it can be solved with only MLP.
From now on we continued using the 40% filtered data, just to be sure. *This is the pepsData.xlsx. *

simpler models:
At this point, we had a meeting with Dr. Itay who surprised us by agreeing that the order of AA within the sequence is not important. He showed us an article that stated that.
To make sure this is true, we tried ignoring the order by using bag-of-words encoding: each peptide was encoded to vector of 20, where vector[i] is how many times AA[i] exist in the peptide. The results where better than before (94% accuracy)!
After that we wanted to see how simpler models will do. We tried Naive Bayes (80%) and Logistic Regression (95%!!!).
*These are naiveBayes.py and logisticRegression.py*

Regression:
Being satisfied with the classification, we moved on to regression. We tried Linear Regression (Mean Squared Error 0.85) and MLP Regressor (from sklearn. MSE 0.32). The results show that for regression it might be preffered to use more complex model.
the results were similar using either one-hot-encoding or bag-of-words encoding.
*These are linearRegression.py and MPLR.py*

Proteins:
The peptides we used in fact are the tails of different proteins.
We wanted to find out what is the impact of the tail stability, to the stability of the whole protein.
We had a data file of the proteins, which contains the whole sequence, regressive stability score in range (1,6), and gene id, which we used to link each protein to its tail (in the data file of the peptides, they also have gene id).
To our regret, we succeeded to link only small portion of the proteins.
To determine the impact of the tail stability, we Logistic Regression to predict the stability of the protein and used the following encoding: each protein was encoded to vector of 21, the first 20 are regular bag-of-words, and the last component is for the stability of the tail: we made 2 runs. in the first it was always 0, and in the second it was -1 for protein with unstable tail, 1 for stable, and 0 for the rest (preteins with tails of middle 50%, and unknown).
The results of both runs were the same - accuracy of 80%. but in the second run the 21st component was given a large weight.
The conclusion is the tail stability does make impact, to some extent, on the stability of the whole protein, but we couldn't see it in the results because the stability of proteins can be predicted well without it, and most of the proteins were on 0 on both runs, because we did not succeed in finding their tail.
*This is prots_utils.py.  run logisticRegerssion.py, put line 6 in comment, and uncomment line 5*
*The proteins data is protsData.csv*
