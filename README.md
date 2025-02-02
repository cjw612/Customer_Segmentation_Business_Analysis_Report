# Customer Segmentation Business Analysis Report
https://archive.ics.uci.edu/dataset/352/online+retail

- ### Project Objective and Overview
    This project aims to compile a business analysis report through segmentation analysis of an online retail dataset. This project first deploys classical EDA methods to provide an overview of the dataset. Subsequently, RFM (Recency, Frequency, Monetary) indexes are created, which perform as the features used for segmentation. In addition, cancelation rate and customer activity indexes are also created to provide additional information on consumer characteristics. A  business analysis report aimed at providing next-step suggestions is derived from the results of segmentation analysis.

- ### Data Source
  The dataset used for this analysis is the **"Oline Retail" Dataset (retail.xlsx)** dataset, containing transaction data from 01/12/2010 to 09/12/2011 of a UK-based online retail. This data set is created by Daqing Chen and downloaded from the [UC Irvine Machine Learning Repository](https://archive.ics.uci.edu/dataset/352/online+retail).

- ### Data Structure
  This dataset contains 541,909 entries, with a grain of one transaction per item per customer, and eight columns, with each column representing features related to that particular news entry. A snapshot of the dataset is depicted in the following table.
  | InvoiceNo | StockCode | Description                             | Quantity | InvoiceDate          | UnitPrice | CustomerID | Country         |
  |-----------|----------|-----------------------------------------|----------|----------------------|-----------|------------|----------------|
  | 536365    | 85123A   | WHITE HANGING HEART T-LIGHT HOLDER     | 6        | 2010-12-01 08:26:00  | 2.55      | 17850.0    | United Kingdom |
  | 536365    | 71053    | WHITE METAL LANTERN                    | 6        | 2010-12-01 08:26:00  | 3.39      | 17850.0    | United Kingdom |
  | 536365    | 84406B   | CREAM CUPID HEARTS COAT HANGER         | 8        | 2010-12-01 08:26:00  | 2.75      | 17850.0    | United Kingdom |
  
  *Sample snapshot of dataset*

- ### Data Cleaning and Preprocessing
  The purpose of this phase is to preprocess the data for clustering. Since there are no missing values in this dataset, the main goal is to identify 1) duplicate values and 2) remove canceled transactions from clustering analysis. The reason to remove canceled transactions is that since these transactions are not finished, they should be analyzed separately from other transactions in order to discover which items are most often canceled. 

- ### Exploratory Data Analysis
  After preprocessing, EDA is thus conducted in order to provide an overview of the current dataset. The following EDAs are performed:
  - #### Summary Statistics
    The following tables are the summary statistics of the qualitative and quantitative variables, respectively. Note that key and date columns (*InvoiceNo*, *StockCode*, *InvoiceDate*, *CustomerID*) are removed in this step of the analysis.
    | Attribute   | Count  | Unique | Top                                      | Freq  |
    |------------|--------|--------|------------------------------------------|-------|
    | Description | 401604 | 3896   | WHITE HANGING HEART T-LIGHT HOLDER      | 2058  |
    | Country    | 401604 | 37     | United Kingdom         | 356728 |
    
    *Summary statistics of qualitative variables*

    | Attribute   | Count    | Mean     | Std Dev     | Min       | 25%  | 50%  | 75%  | Max      |
    |------------|---------|---------|------------|----------|------|------|------|---------|
    | Quantity   | 401604.0 | 12.1833  | 250.2830   | -80995.0  | 2.00 | 5.00 | 12.00 | 80995.0  |
    | UnitPrice  | 401604.0 | 3.4741   | 69.7640    | 0.0       | 1.25 | 1.95 | 3.75  | 38970.0  |

    *Summary statistics of quantitative variables*

    Furthermore, we would also want to know the unique values corresponding to each feature:
    - Number of entries: 401604
    - Number of unique Customer IDs: 4372
    - Number of unique Countries: 37
    - Number of unique Products: 3684
    - Number of unique Transactions: 22190 
      
    As a result, we can identify that the majority of the products of this retailer are low-priced items.

  - #### Country Statistics
    In addition to basic summary statistics, we would also like to know where the customers and transactions have been made. Therefore, we can plot frequency histograms by transaction and customer, respectively. As a result, we can identify that the majority of our customers and transactions are based in the United Kingdom, with other Western European countries such as Germany and France following. 
    ![country](assets/country.png)
    *Frequency histogram by transaction (left) and by customer (right)*

  - #### Product Statistics
    To analyze the top products sold, we can identify the top products sold by quantity and by total value. We can identify that the top products sold either by quantity or by value are similar, such as *WHITE HANGING HEART T_LIGHT HOLDER*, *REGENCY CAKESTAND 3 TIER*, and *JUMBO BAG RED RETROSPOT* are all in the top 5 products by quantity and by value.
    ![product](assets/product.png)

  - #### Canceled Product Statistics
    In addition to successful transactions, identifying products that are frequently canceled could also provide insights into lowering cancellation rates in the future. Similarly, we can also analyze most canceled products by both quantity and value. We can observe that the top product by value *PAPER CRAFT, LITTLE BIRDIE* is also the top canceled product by value. Further analysis into reasons why customers return these products could potentially improve both customer experience and reduce costs derived from transaction cancellations. 
    ![canceled](assets/canceled.png)

- ### Index Creation
  - #### RFM Indexes
    RFM indexes are commonly used in customer segmentation, serving as a basis of segmentation based on consumer purchasing behavior. In particular, the indexes are operationalized with the following methods:

    - **Recency Index:**
    The Recency Index represents how recently a customer has completed a purchase. In this analysis, it is operationalized by computing the difference in days between the current date and the most recent purchase. However, due to the limitation of the timeframe of this dataset, the current date is set to be the date of the most recent transaction that occurred in the dataset.

        $$R_i = (\text{LatestDate} - \text{LastPurchaseDate}_{i}), \quad \forall i \in \text{Customers}$$
    
    - **Frequency Index:**
      The Frequency Index represents how frequently a customer has been purchasing. In this analysis, it is operationalized by counting the number of unique invoice numbers associated with a particular customer. The reason that the number of unique invoice numbers is used is that even if an invoice number is related to multiple products, each invoice number only represents one transaction. Therefore, to accurately reflect the purchasing frequency of a customer, unique invoice numbers should be counted since they represent unique transactions. 

        $$F_i = \sum_{j} x_{ij}, \quad \forall i \in \text{Customers}, \forall j \in \text{Transactions}$$

      In which $X_{ij}$ denotes a binary variable that is equal to one if that transaction's invoice number is unique.

    - **Monetary Index:**
      The Monetary Index represents the total monetary value spent by a customer. In this analysis, it is operationalized by summing the total revenue generated from all transactions for a specific customer. The variable $Monetary_i$ denotes the total value of a particular customer.

      $$M_i = \sum_{j} \text{Monetary}_{i}, \quad \forall i \in \text{Customers}, \forall j \in \text{Transactions}$$

    - **Results:** After creating RFM Indexes, we can observe the distributions of the RFM values using histograms. However, all three indexes are heavily skewed as a result. Prior to log transformation. we first removed customers with a negative monetary value (n=42). The reason for removing such customers is that, from the retailer's perspective, these customers generate negative value for the retailer. Therefore, if these customers are placed in the same pool as other customers, the clustering algorithm may group these customers into the same cluster. In response, by removing these customers prior to clustering, we could reduce the noise created by these customers and focus on segmenting customers who generate revenue for the retailer. 
      ![rfm_raw](assets/rfm_raw.png)
      *Histograms of raw RFM Indexes*
      
      In response, we can perform log transformations on all three indexes to mitigate the skewness of their distributions. The following graph depicts the results after performing log transformation:
      ![rfm_transformed](assets/rfm_transformed.png)
      *Histograms of log-transformed RFM Indexes*
      
  - #### Cancelation Rate
    In order to analyze the cancelation rate of each customer, a cancelation rate is calculated for each customer. The cancelation rate for the $ith$ customer is calculated by dividing transactions canceled by the $ith$ customer over all transactions made by that customer.
  - #### Customer Activity Index
    The Customer Activity Index (CAI) represents the activity level of a customer, with a higher numeric value indicating a higher activity level. CAI is operationalized with the following procedures:
    
    First, we denote the $ith$ customer as $C_i$ and its corresponding $j$ transactions $T_{ij}$, we first compute the intervals between each purchases:
    
    $$T_{i}^{\text{Interval}} = T_{ij}^{\text{InvoiceDate}} - T_{i(j-1)}^{\text{InvoiceDate}}$$
    
    We then assign weights in ascending order from earliest purchase to newest, 

    $$T_{ij}^{\text{weight}} = j$$

    After we obtained the weights and intervals, we then compute the product of customer $i$ by summing up the product of all pairs of intervals and weights:

    $$C_{i}^{\text{Product}} = \sum_{j} T_{ij}^{\text{weight}} \cdot T_{ij}^{\text{Interval}}$$

    Then, we yield the WMLE (Weighted Mean Logarithmic Expectation) by deviding the product by the sum of weights:

    $$C_{i}^{\text{WMLE}} = \frac{C_{i}^{\text{Product}}}{\sum_{j} T_{ij}^{\text{weight}}}$$

    To yield the final CAI, we compute the difference between the average interval and the WMLE. Since the WMLE represents the expected average purchasing interval from consumers' historical transactions, a CAI greater than 0 indicates that a consumer is purchasing more often than its expected purchasing interval. 

    $$C_{i}^{\text{AvgInterval}} = \frac{\sum_{j} T_{ij}^{\text{Interval}}}{N}$$
    
    $$C_{i}^{\text{CAI}} = \frac{C_{i}^{\text{AvgInterval}} - C_{i}^{\text{WMLE}}}{C_{i}^{\text{WMLE}}}$$    
    
- ### Clustering Model
  - #### Model Selection
    Due to the small number of features in this dataset, K-Means clustering is deployed in this analysis. K-Means is a state-of-the-art clustering method often used in business contexts, providing a straightforward method for initial clustering. To determine the hyperparameter $K$, we first use the elbow method to find the $K$ associated with the most significant drop in the Within-Cluster Sum of Squares (WCSS). From the results of the following plot, we can observe that a $K$ value larger than 3 would only yield incremental improvements with respect to the (WCSS). Therefore, we set the hyperparameter $K = 3$ in this analysis.
    ![elbow](assets/elbow.png)
    *Graphical representation of the Elbow Method*
  - #### Model Results
    After we applied K-Means clustering on our dataset with $K=3$, we can first investigate how the data is distributed across the clusters as long as there is no cluster with an unreasonably small number of data points. As a result, we can observe that the data points are spread somewhat evenly between the clusters, with the smallest cluster still having nearly 20% of the total data points.
    ![cluster](assets/cluster.png)
    *Distribution of data points within clusters*
    
- ### Results
    - #### Clustering Results
      According to the clustering results based on RFM Indexes, we can plot the three clusters into a radar plot to have an overview of the different features of the clusters:   
        ![radar](assets/radar.png)\
        *Radar plot of clusters against features*\
      We can thus identify the following clusters of customers:\
      - **Cluster 1 - Loyal Customers:** The first cluster consists of customers with a well-rounded performance across all indexes. These customers have moderate RFM and CAI values when compared with other clusters, in addition to low cancellation rates. We can, therefore, infer that customers in cluster 1 are loyal customers with a certain amount of brand loyalty to the retail. 
      - **Cluster 2 - High-Value Customers:** The second cluster consists of customers with a high level of engagement (CAI), as well as high frequency and monetary values, suggesting that these customers may be newly acquired and active. However, these customers are currently displaying a lower recency value and a higher cancellation rate when compared with the other two clusters. These customers may only make a large number of transactions in specific periods, which reflects the relatively low recency value. 
      - **Cluster 3 - Occasional Customers:** The third cluster consists of customers with a high recency value yet a low monetary and frequency value. This suggests that these customers shop more infrequently and spend less than other customers, in addition to a lower CAI value, further suggesting that these customers may have a lower engagement when compared with other clusters of customers. 
    - #### Clustering Behavior Analysis
      - **Transaction Time** We can observe that the majority of transactions are made from noon to late afternoon, regardless of which cluster the customer is in. Following the assumption that transactions are recorded at the moment that they are made by consumers, this may indicate that the customers are mainly purchasing during standard working hours or lunch breaks, as apposed to making purchases after work.
        ![time](assets/time.png)
        *Transaction Frequency by Hour of all clusters*
      - **Top Products by Cluster:** We can observe that in terms of both frequency and total value, cluster 1 and cluster 3 display similar purchasing behavior with a high overlap on top products. However, we can also observe that cluster 2 displayed a different purchasing behavior with the other two clusters. For example, products *RABBIT NIGHT LIGHT*, *CHILLI LIGHTS*, and *BLACK RECORD COVER FRAME* did not appear in the top 10 products of either cluster 1 and cluster 3.
        ![product_cluster](assets/product_cluster.png)
        *Top products by frequency and value purchased of all clusters*
        
- ### Implications

- ### Limitations
  - The current category reduction process is based on domain knowledge with sampled data from each category. However, there may be some other set of categories that can further reduce inter-class similarity, therefore potentially further optimizing the performance of the classification models.
  - Hyperparameter tuning is limited due to constraints on computational resources. Implementing Bayesian Optimization on a more extensive range of hyperparameters may also further optimize the models.
  - Implementing single-label classification may lower the performance of our model, as some of the news entries have a more "ambiguous" class. For instance, President Trump's interactions with SNL hosts are often categorized as $COMEDY$ in the original labels. However, the keyword "Trump" may lead the model to misclassify that news entry into $General$ (which encompasses political news). Therefore, implementing multi-label classification may potentially address this issue by reflecting the nature of news having more than one category in real-world scenarios.  

- ### References
  [1] Simon Newman. "rtr1pc8i_0.jpg" Reuters Institute for the Study of Journalism, 31 October 2019, https://reutersinstitute.politics.ox.ac.uk/news/if-your-newsroom-not-diverse-you-will-get-news-wrong/ \
  [2] Rishabh Misra, News Category Dataset (Kaggle, 2022), https://www.kaggle.com/datasets/rmisra/news-category-dataset/data
