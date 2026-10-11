# fifa18-player-clustering
K-means clustering in PySpark to find cheaper substitutes for FIFA 18 star players

FIFA 18 – Finding Cheaper Substitutes for Star Players
Star players are expensive. This project uses K-means clustering in PySpark on the FIFA 18 dataset to find players who play like each star but cost far less.

**Approach**
    1. Data: FIFA 18 player dataset from Kaggle (17,981 players, 74 attributes). 
    2. Features: 10 playing-style attributes, chosen from FIFA's own attribute categories: Acceleration, Ball control, Composure, Dribbling, Finishing, Long passing, Short passing, Reactions, Stamina, Volleys. 
    3. Cleaning: The attributes are stored as text, sometimes with an in-game boost (e.g. 81+3). Only the base value is kept. Value and Wage (e.g. €95.5M, €565K) are converted to euros. Players with a value or wage of €0 are left out when selecting stars and substitutes. 
    4. Scaling: The features are standardized (mean 0, std 1) so that all attributes weigh equally. 
    5. Choosing k: Silhouette scores are computed for k = 5–15 on a fixed 25% sample (seed 42). 
    6. Clustering: K-means with k = 11. 
    7. Stars and substitutes: The highest-valued player in each cluster is its star. For each star, the 20 nearest players in the same cluster are found by Euclidean distance. From those 20, the 2 closest and the 2 lowest-paid are reported. 
    
**Choice of k**
The best silhouette score came at k = 6 (0.330). But  k = 11 was chosen instead (0.294, a local maximum) because it gives finer player groups and more stars to find substitutes for. This trades some cluster separation for practical usefulness.
Example results
Star (wage)	Closest substitute (wage)	Cheapest of the 20 nearest (wage)
Neymar (€280K)	E. Hazard (€295K)	S. Giovinco (€15K)
De Gea (€215K)	H. Lloris (€165K)	K. Vermeer (€14K)
M. Neuer (€230K)	C. Bravo (€110K)	A. Correa (€1K)
R. Varane (€175K)	A. Laporte (€36K)	L. Ponzio (€16K)

The closest match is not always cheaper: Hazard earns more than Neymar. That is why both the closest and the cheapest substitutes are reported.
Every reported substitute is much closer to its star than the cluster average. In Neymar's cluster, for example, the mean distance to the star is 3.55, while his substitutes are at 0.85–1.57.

**Limitations**
    • Overlapping clusters: Silhouette scores are modest (0.25–0.33). 
    • Equal feature weights: The attributes that make a particular star valuable do not necessarily drive the similarity. A football expert should review the results before they are used. 
    • Goalkeepers: No goalkeeper attributes are included, so goalkeepers are grouped only by their outfield stats. 
    • Cluster shape: K-means assumes roughly spherical clusters. 
    • Future work: Try other feature sets and feature weights, possibly tailored to each star. 
    
**Running the project**
The notebook was built for Google Colab, which provides PySpark and matplotlib. A Google account is required.
    1. Download CompleteDataset.csv from https://www.kaggle.com/datasets/thec03u5/fifa-18-demo-player-dataset and upload it to Google Drive. 
    2. Open FIFA18.ipynb in Colab and set data_path in the first cell to the file's location in Drive. 
    3. Run all cells from top to bottom. If PySpark is not available, run !pip install pyspark first. 

**Context**
Individual assignment in the course Machine Learning with Big Data 7.5 ECTS at Mälardalen University.
