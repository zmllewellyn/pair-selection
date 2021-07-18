# pair-selection
Pair selection algorithm base on Sarmento and Horta (2020). live_version for practical input and test_version for explanation
- Paper at https://www.sciencedirect.com/science/article/pii/S0957417420303146

# Instructions for Implementation
- Necessary inputs are a list of companies to test and their respective prices and returns data (must be over same time period)
- For live_version the only output is a list containing the selected pairs and their spreads up to the current time period
- For test_version the output includes the number of testable pairs at each stage of the program and a graph of each of the selected pairs spreads
- Anecdotally the algorithm tends to work better for companies that have some macroeconomic similarities (e.g. same sector/industry)

# Theory Explanation
This pair selection algorithm works on the three step process below
![Screen Shot 2021-05-07 at 2 16 03 pm](https://user-images.githubusercontent.com/67776635/117397170-df0df880-af3e-11eb-88f6-79f8f4c0d295.png)

For step 1 we want to run the principal component analysis on the returns data for each stock in the format below
![Uploading Screen Shot 2021-05-07 at 2.18.47 pm.png…]()
Notice that we use returns instead of raw prices for PCA. This was done due to the assumption of normality of the input data into PCA.
Also the decision to choose two principal components over three or more was largely done to better replicate the paper but not neccessary for the function of OPTICS (at least for small n)

In step 2 the OPTICS algorithm is run to cluster each stock based on its (p1,p2) coordinates on the principal component 1 and 2 plane like below
![Screen Shot 2021-05-07 at 2 20 52 pm](https://user-images.githubusercontent.com/67776635/117397484-9c98eb80-af3f-11eb-9348-4cf1af570433.png)
As mentioned in the paper OPTICS was chosen of DBSCAN due to OPTICS allowing clusters of various density to be made

We will split step 3 into 3a and 3b for clarity.

For step 3a we aim to find the combinations of pairs of stocks in each identified cluster from step 2. Then we will find the spread of each stock using a simple linear regression method.

Finally in step 3b the Hurst Exponent is calculated for each pairs spread to ensure the anti-persistent/mean-reverting (H<0.5) behaviour is present. If this is true then we complete the final step of testing for the stationarity of the spreads using the Augmented Dickey-Fuller test. Any pairs also passing this are finally returned as successful pairs for trading.
- When testing for stationarity a Bonferroni correction was utilised to avoid p-hacking

# Results from Test Case
Using the companies listed in Energy.csv we got this list of pairs as possible options for pair trading
[('PPL', 'CQP'), ('WMB', 'PPL'), ('EPD', 'PPL'), ('EPD', 'KMI'), ('PEG', 'ETR'), ('SO', 'ETR'), ('WEC', 'EIX'), ('PEG', 'EIX'), ('SO', 'EIX'), ('SO', 'ED'), ('PEG', 'WEC'), ('ES', 'WEC'), ('SO', 'WEC'), ('SO', 'PEG'), ('AEP', 'ES'), ('SO', 'ES'), ('DUK', 'ES'), ('SO', 'AEP'), ('SO', 'D'), ('PSX', 'VLO')]
The graphs of the spreads for each pair can be located in graph_spread

The number of pairs after each stage of testing is
![Screen Shot 2021-05-07 at 2 35 14 pm](https://user-images.githubusercontent.com/67776635/117398354-85f39400-af41-11eb-8969-fe5349c4527f.png)


# Analysis
The first point to notice from the test case is that the program does seem to work. It is able to shrink 435 possible pairs to test into 20 successful pairs after the adf test. And visually using the graphs of the spreads it does seem as if the spreads are mean-reverting which is necessary for pair trading.

However it should be noted that testing for anti-persistent behaviour using the Hurst Exponent didn't have any affect on the total pairs to be tested in step 3. Implying that further testing is necessary to see whether Hurst is useful here or weakly implied by the results of step 1 and 2.

# Future Updates/Improvements 
- Testing for the necessity of the Hurst Exponent
- Additional tests for mean-reversion to reduce the affect of the Bonferroni correction

