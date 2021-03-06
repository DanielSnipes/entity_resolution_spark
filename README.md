# entity_resolution_spark
Collection of some algorithms for entity resolution on string attribute

Written in python. 

Refer howtoguide for the overview of features. 

Complements the algorithms presents in jellyfish package of python.

Edit distance (s,t): The number of minimum edit operation (Insertion,Deletion,Substitution) to tranform s to t. Each operation are wieghted.<br/>

Affine gap: A+(B⋅L). A is the cost of opening the gap. B is the gap extension penality and L is the length of the gap. <br/>

![Alt text](./Entity_resolution.png "Summary of algorithms")
*Image adopted from Naumann, F. (2013). Similarity measure. Personal collection of F. Naumann, Hasso-Plattner-Institut, Potsdam,Germany


|Algorithm| example|score | scoreApplicability|
|---------|--------|------|---------------|
|Levenshtein|leven("Peter","Pteer")|0.6| Hello world of approx. matching. Many extensions are possible|
|Damareu-Levenshtein|dl("Peter","Pteer")|0.8| Transposition of adjacent character extension of Levenshtein|
|jaro distance| jaro('paul j','palu' )|0.8|Characters at position i at s1 and j at s2 are similar if they are equal and not longer than abs(i-j) < 0.5 min(len(s1), len(s2)) |
|Jaro-winkler|jw('paul j','palu' )| 0.84| Extension to jaro. Works best on census names [1]|
|Monge-Eklan| monge_score("paul johnson","johson paule")|0.94| Best of the character based method [1]|
|cosine similarity| sim(['apocalypse now'],['apoclapse now'])| 0.707| Similar to search engine. Good results for phrase queries. Many extension are possible (Jaccard TFIDF). TFIDF used in topic modelling as well |
|N-gram cosine similarity|sim(['apocalypse now'],['apoclapse now'])|0.78|By generating n-gram of the query and the document, we increase the similarity score as both share lot of tokens in common. Suitable for small documents like database text|
|Soft Tf-idf | sim(['apocalypse now'],['apoclapse now'])|0.97|handles smaller corrections in tokens. Suitable for short records (database text).  |

Refer equations.pdf for the algorithm formulation.

Once the Needleman-Wunch and Smith-Watermann algorithms are implmented , I will refactor the code and release it as a package.

To apply this algorithms on spark is a five simple steps process,

Step1 : Read two input sources containing data

Step2 : Perform pre-processing like lowercase conversion, removing unwanted characters. 

2a. If algorithms requires Tf-idf scores, generate it here and distribute the wieghts
2b. N-gram is not good for large documents because of sparsity.

Step3 : Take the cross product of the data sources. # cartesian product
3a. Instead of cartesian product , you can use sorted neighbourhood and blocking algorithm.

Step4 : Apply the differnt algorithm based on application needs

Step 5 : Filter the results based a threshold. A good threshold value can be determined by ROC curve if you have golden dataset. Else, start with a lower threshold and experiment.

Step6 : Transform the dataset to the form (idsrc1,idscr2,avg.score) and write the results.

Refer spark_template.py for applying the algorithm in distributed system.

Further development:
1. Machine learning algorithms based on score functions (SVM, decision tress), neural net with single preceptron.
2. BM25 algorithm for probabilitic interepresentation of matching algorithm instead of cosine score

Reference
1. Cohen, W., Ravikumar, P., & Fienberg, S. (2003, August). A comparison of string metrics for matching names and records. In Kdd workshop on data cleaning and object consolidation (Vol. 3, pp. 73-78).<br/>
2. Naumann, F., & Herschel, M. (2010). An introduction to duplicate detection. Synthesis Lectures on Data Management, 2(1), 1-87.<br/>
3. A. K. Elmagarmid, P. G. Ipeirotis, and V. S. Verykios, “Duplicate record detection: A survey,”
Knowledge and Data Engineering, IEEE Transactions on, vol. 19, no. 1, pp. 1–16, 2007.

