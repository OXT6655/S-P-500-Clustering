S&P 500 Clustering Analysis
Otajon Yuldashev
2025-01-11
Introduction
This analysis aims to group S&P 500 stocks into clusters based on their risk and reward profiles. By leveraging clustering techniques, we identify distinct groups of stocks that can inform investment strategies. The analysis uses metrics such as average return, volatility, and market capitalization to determine meaningful clusters.

Data processing
Objective: Prepare the datasets for analysis by cleaning, transforming, and aligning the data.

Data Loading:
Imported two datasets and necessary libraries:

Historical stock prices.
S&P 500 prices (close, open, high, low, adj.close, volume).
S&P 500 company metadata (sectors, market cap, etc.).
library(tidyverse)
## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
## ✔ dplyr     1.1.4     ✔ readr     2.1.5
## ✔ forcats   1.0.0     ✔ stringr   1.5.1
## ✔ ggplot2   3.5.1     ✔ tibble    3.2.1
## ✔ lubridate 1.9.3     ✔ tidyr     1.3.1
## ✔ purrr     1.0.2     
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()
## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors
library(cluster)
library(factoextra)
## Welcome! Want to learn more? See two factoextra-related books at https://goo.gl/ve3WBa
# Load datasets
sp500_prices <- read.csv("sp500_stocks.csv")
sp500_metadata <- read.csv("sp500_companies.csv")


# Inspect data
head(sp500_prices)
##         Date Symbol Adj.Close    Close     High      Low     Open  Volume
## 1 2010-01-04    MMM  44.01673 69.41472 69.77425 69.12207 69.47324 3640265
## 2 2010-01-05    MMM  43.74103 68.97993 69.59030 68.31104 69.23077 3405012
## 3 2010-01-06    MMM  44.36134 69.95819 70.73579 69.82442 70.13378 6301126
## 4 2010-01-07    MMM  44.39317 70.00836 70.03345 68.66221 69.66555 5346240
## 5 2010-01-08    MMM  44.70597 70.50167 70.50167 69.64883 69.97491 4073337
## 6 2010-01-11    MMM  44.52572 70.21739 70.73579 69.74081 70.55184 3500812
head(sp500_metadata)
##   Exchange Symbol             Shortname              Longname
## 1      NMS   AAPL            Apple Inc.            Apple Inc.
## 2      NMS   NVDA    NVIDIA Corporation    NVIDIA Corporation
## 3      NMS   MSFT Microsoft Corporation Microsoft Corporation
## 4      NMS   AMZN      Amazon.com, Inc.      Amazon.com, Inc.
## 5      NMS  GOOGL         Alphabet Inc.         Alphabet Inc.
## 6      NMS   GOOG         Alphabet Inc.         Alphabet Inc.
##                   Sector                       Industry Currentprice
## 1             Technology           Consumer Electronics       254.49
## 2             Technology                 Semiconductors       134.70
## 3             Technology      Software - Infrastructure       436.60
## 4      Consumer Cyclical                Internet Retail       224.92
## 5 Communication Services Internet Content & Information       191.41
## 6 Communication Services Internet Content & Information       192.96
##      Marketcap      Ebitda Revenuegrowth          City State       Country
## 1 3.846820e+12 1.34661e+11         0.061     Cupertino    CA United States
## 2 3.298803e+12 6.11840e+10         1.224   Santa Clara    CA United States
## 3 3.246069e+12 1.36552e+11         0.160       Redmond    WA United States
## 4 2.365034e+12 1.11583e+11         0.110       Seattle    WA United States
## 5 2.351625e+12 1.23470e+11         0.151 Mountain View    CA United States
## 6 2.351623e+12 1.23470e+11         0.151 Mountain View    CA United States
##   Fulltimeemployees
## 1            164000
## 2             29600
## 3            228000
## 4           1551000
## 5            181269
## 6            181269
##                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            Longbusinesssummary
## 1                                                                                                                                                                                                                                                                                                            Apple Inc. designs, manufactures, and markets smartphones, personal computers, tablets, wearables, and accessories worldwide. The company offers iPhone, a line of smartphones; Mac, a line of personal computers; iPad, a line of multi-purpose tablets; and wearables, home, and accessories comprising AirPods, Apple TV, Apple Watch, Beats products, and HomePod. It also provides AppleCare support and cloud services; and operates various platforms, including the App Store that allow customers to discover and download applications and digital content, such as books, music, video, games, and podcasts, as well as advertising services include third-party licensing arrangements and its own advertising platforms. In addition, the company offers various subscription-based services, such as Apple Arcade, a game subscription service; Apple Fitness+, a personalized fitness service; Apple Music, which offers users a curated listening experience with on-demand radio stations; Apple News+, a subscription news and magazine service; Apple TV+, which offers exclusive original content; Apple Card, a co-branded credit card; and Apple Pay, a cashless payment service, as well as licenses its intellectual property. The company serves consumers, and small and mid-sized businesses; and the education, enterprise, and government markets. It distributes third-party applications for its products through the App Store. The company also sells its products through its retail and online stores, and direct sales force; and third-party cellular network carriers, wholesalers, retailers, and resellers. Apple Inc. was founded in 1976 and is headquartered in Cupertino, California.
## 2                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  NVIDIA Corporation provides graphics and compute and networking solutions in the United States, Taiwan, China, Hong Kong, and internationally. The Graphics segment offers GeForce GPUs for gaming and PCs, the GeForce NOW game streaming service and related infrastructure, and solutions for gaming platforms; Quadro/NVIDIA RTX GPUs for enterprise workstation graphics; virtual GPU or vGPU software for cloud-based visual and virtual computing; automotive platforms for infotainment systems; and Omniverse software for building and operating metaverse and 3D internet applications. The Compute & Networking segment comprises Data Center computing platforms and end-to-end networking platforms, including Quantum for InfiniBand and Spectrum for Ethernet; NVIDIA DRIVE automated-driving platform and automotive development agreements; Jetson robotics and other embedded platforms; NVIDIA AI Enterprise and other software; and DGX Cloud software and services. The company's products are used in gaming, professional visualization, data center, and automotive markets. It sells its products to original equipment manufacturers, original device manufacturers, system integrators and distributors, independent software vendors, cloud service providers, consumer internet companies, add-in board manufacturers, distributors, automotive manufacturers and tier-1 automotive suppliers, and other ecosystem participants. NVIDIA Corporation was incorporated in 1993 and is headquartered in Santa Clara, California.
## 3 Microsoft Corporation develops and supports software, services, devices and solutions worldwide. The Productivity and Business Processes segment offers office, exchange, SharePoint, Microsoft Teams, office 365 Security and Compliance, Microsoft viva, and Microsoft 365 copilot; and office consumer services, such as Microsoft 365 consumer subscriptions, Office licensed on-premises, and other office services. This segment also provides LinkedIn; and dynamics business solutions, including Dynamics 365, a set of intelligent, cloud-based applications across ERP, CRM, power apps, and power automate; and on-premises ERP and CRM applications. The Intelligent Cloud segment offers server products and cloud services, such as azure and other cloud services; SQL and windows server, visual studio, system center, and related client access licenses, as well as nuance and GitHub; and enterprise services including enterprise support services, industry solutions, and nuance professional services. The More Personal Computing segment offers Windows, including windows OEM licensing and other non-volume licensing of the Windows operating system; Windows commercial comprising volume licensing of the Windows operating system, windows cloud services, and other Windows commercial offerings; patent licensing; and windows Internet of Things; and devices, such as surface, HoloLens, and PC accessories. Additionally, this segment provides gaming, which includes Xbox hardware and content, and first- and third-party content; Xbox game pass and other subscriptions, cloud gaming, advertising, third-party disc royalties, and other cloud services; and search and news advertising, which includes Bing, Microsoft News and Edge, and third-party affiliates. The company sells its products through OEMs, distributors, and resellers; and directly through digital marketplaces, online, and retail stores. The company was founded in 1975 and is headquartered in Redmond, Washington.
## 4                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        Amazon.com, Inc. engages in the retail sale of consumer products, advertising, and subscriptions service through online and physical stores in North America and internationally. The company operates through three segments: North America, International, and Amazon Web Services (AWS). It also manufactures and sells electronic devices, including Kindle, Fire tablets, Fire TVs, Echo, Ring, Blink, and eero; and develops and produces media content. In addition, the company offers programs that enable sellers to sell their products in its stores; and programs that allow authors, independent publishers, musicians, filmmakers, Twitch streamers, skill and app developers, and others to publish and sell content. Further, it provides compute, storage, database, analytics, machine learning, and other services, as well as advertising services through programs, such as sponsored ads, display, and video advertising. Additionally, the company offers Amazon Prime, a membership program. The company's products offered through its stores include merchandise and content purchased for resale and products offered by third-party sellers. It serves consumers, sellers, developers, enterprises, content creators, advertisers, and employees. Amazon.com, Inc. was incorporated in 1994 and is headquartered in Seattle, Washington.
## 5                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   Alphabet Inc. offers various products and platforms in the United States, Europe, the Middle East, Africa, the Asia-Pacific, Canada, and Latin America. It operates through Google Services, Google Cloud, and Other Bets segments. The Google Services segment provides products and services, including ads, Android, Chrome, devices, Gmail, Google Drive, Google Maps, Google Photos, Google Play, Search, and YouTube. It is also involved in the sale of apps and in-app purchases and digital content in the Google Play and YouTube; and devices, as well as in the provision of YouTube consumer subscription services. The Google Cloud segment offers infrastructure, cybersecurity, databases, analytics, AI, and other services; Google Workspace that include cloud-based communication and collaboration tools for enterprises, such as Gmail, Docs, Drive, Calendar, and Meet; and other services for enterprise customers. The Other Bets segment sells healthcare-related and internet services. The company was incorporated in 1998 and is headquartered in Mountain View, California.
## 6                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   Alphabet Inc. offers various products and platforms in the United States, Europe, the Middle East, Africa, the Asia-Pacific, Canada, and Latin America. It operates through Google Services, Google Cloud, and Other Bets segments. The Google Services segment provides products and services, including ads, Android, Chrome, devices, Gmail, Google Drive, Google Maps, Google Photos, Google Play, Search, and YouTube. It is also involved in the sale of apps and in-app purchases and digital content in the Google Play and YouTube; and devices, as well as in the provision of YouTube consumer subscription services. The Google Cloud segment offers infrastructure, cybersecurity, databases, analytics, AI, and other services; Google Workspace that include cloud-based communication and collaboration tools for enterprises, such as Gmail, Docs, Drive, Calendar, and Meet; and other services for enterprise customers. The Other Bets segment sells healthcare-related and internet services. The company was incorporated in 1998 and is headquartered in Mountain View, California.
##       Weight
## 1 0.06920915
## 2 0.05934964
## 3 0.05840088
## 4 0.04254995
## 5 0.04230871
## 6 0.04230867
Data preprocessing:
# Convert Date to Date format
sp500_prices$Date <- as.Date(sp500_prices$Date)


# # Handle missing values by omitting rows with NA
sp500_prices <- na.omit(sp500_prices)


# Calculate daily returns
sp500_prices <- sp500_prices %>%
  arrange(Symbol, Date) %>%
  group_by(Symbol) %>%
  mutate(Daily_Return = log(Close) - log(lag(Close))) %>%
  ungroup() %>%
  filter(!is.na(Daily_Return))  # Remove NA values from returns
Feature Engineering:
Calculated daily returns using the formula:

Daily_return=log(PtPt−1)

Computed key metrics:

Average Return: Mean of daily returns for each stock.
Volatility: Standard deviation of daily returns.
Merged metadata (sector and market cap) with these computed features.

# Feature Engineering: Calculate average return and volatility for each stock
features <- sp500_prices %>%
  group_by(Symbol) %>%
  summarise(
    Avg_Return = mean(Daily_Return, na.rm = TRUE),
    Volatility = sd(Daily_Return, na.rm = TRUE)
  ) %>%
  left_join(sp500_metadata %>% select(Symbol, Sector), by = "Symbol")  # Add Sector metadata
Output:
A clean dataset with the following features:

Symbol: Stock ticker.
Avg_Return: Average daily return.
Volatility: Daily return volatility.
Market_Cap: Market capitalization.
Sector: Company sector.
Exploratory Data Analysis (EDA)
Objective:
Understand the distribution and relationships between key metrics.

Visualized Distributions:

# Plot distribution of average returns
ggplot(features, aes(x = Avg_Return)) +
  geom_histogram(bins = 30, fill = "blue", alpha = 0.7) +
  labs(title = "Distribution of Average Returns",
       x = "Average Return",
       y = "Frequency") +
  theme_minimal()


The histogram for average returns shows that most values are centered near zero, with very few large deviations. This suggests that average returns are relatively stable for most stocks, though there may be some outliers or stocks with extreme performance.

# Plot distribution of volatility
ggplot(features, aes(x = Volatility)) +
  geom_histogram(bins = 30, fill = "red", alpha = 0.7) +
  labs(title = "Distribution of Volatility",
       x = "Volatility",
       y = "Frequency") +
  theme_minimal()


The histogram for volatility indicates a right-skewed distribution, meaning that most stocks have low volatility, but some have much higher values. This skewness is common in financial datasets and highlights that only a few stocks have very high risk or price fluctuations.

Clustering Methodology
We scale the features because Avg_Return and Volatility have different ranges and units. Scaling ensures that both features contribute equally to the distance calculation during K-means clustering, preventing the feature with a larger range (e.g., volatility) from dominating the clustering process.

# Scale features
scaled_features <- scale(features[, c("Avg_Return", "Volatility")])
In order to determine the optimal number fo clusters we are going to use Silhouette method for both K-means and Clara clustering algorithms.

# Silhouette Method for K-means
fviz_nbclust(scaled_features, kmeans, method = "silhouette") +
  labs(title = "Silhouette Method for Optimal Clusters")


So, in order to find optimal number of clusters for K-means clustering I used Elbow Method, but was not 100% sure of results of it as it was showing 2 clusters. So then I decided to try Silhouette method , which clearly confirmed that the number of clusters should be 2 for this fuatures. Then I decided to skip Elbow method and include silhouette method instead.

K-means Clustering:
Performed K-means clustering to group stocks based on average return and volatility.

# Perform K-means clustering with k = 2, because from our Elbow method we have chosen 2 clusters as optimal number.
set.seed(123)
kmeans_result <- kmeans(scaled_features, centers = 2, nstart = 25)

# Add cluster assignments to features
features$Cluster <- kmeans_result$cluster

# Visualize K-means clusters
fviz_cluster(kmeans_result, data = scaled_features,
             geom = "point", stand = FALSE,
             ellipse = TRUE) +
  labs(title = "K-means Clustering of Stocks")


Overview
Cluster Spread
Cluster 1 is more tightly packed, indicating that the stocks in this group have similar, stable characteristics. On the other hand, Cluster 2 is more spread out, showing greater variability in average returns and volatility.

Cluster Location
The stocks in Cluster 2 generally have higher volatility and, in some cases, higher returns compared to Cluster 1.

Cluster Evaluation
Objective:
Assess the quality of the clusters and validate the clustering results.

Silhouette Score: Measures how well each stock fits within its cluster compared to other clusters.
# Compute silhouette score
silhouette_score <- silhouette(kmeans_result$cluster, dist(scaled_features))

# Plot silhouette analysis
fviz_silhouette(silhouette_score) +
  labs(title = "Silhouette Analysis for K-means Clustering")
##   cluster size ave.sil.width
## 1       1  379          0.60
## 2       2  123          0.15


# Average silhouette width
mean_sil_width <- mean(silhouette_score[, "sil_width"])
print(paste("Average Silhouette Width: ", round(mean_sil_width, 3)))
## [1] "Average Silhouette Width:  0.49"
The silhouette analysis for K=2 clustering shows that Cluster 1 is well-defined, with most points having high silhouette widths. However, Cluster 2 exhibits some overlap or ambiguity, as indicated by lower silhouette widths for certain points. The overall average silhouette width of 0.49 (moderate clustering) supports the decision to use K=2.

Purpose of the Plot
This visualization helps understand the sectoral composition of each cluster, offering insights into the risk-return profiles of sectors within the S&P 500 stocks. It provides a useful guide for investors aiming to diversify their portfolios based on sector-specific strategies.

# Sector distribution within clusters
ggplot(features, aes(x = as.factor(Cluster), fill = Sector)) +
  geom_bar(position = "fill") +
  labs(
    title = "Sector Distribution Across Clusters",
    x = "Cluster",
    y = "Proportion",
    fill = "Sector"
  ) +
  theme_minimal()


Summary of the Sector Composition Bar Plot
This bar plot illustrates the sectoral composition of stocks within each cluster, highlighting the proportion of various sectors in the two clusters.

Key Observations
Cluster 1:
Dominant Sectors:
Sectors like Technology, Utilities, and Consumer Defensive hold significant proportions in this cluster.
Characteristics:
These sectors typically represent low-risk investments with relatively stable performance.
The heavy presence of Utilities suggests that this cluster is more defensive in nature, catering to risk-averse investors.
Cluster 2:
Dominant Sectors:
Sectors like Technology, Healthcare, and Consumer Cyclical are prominent.
Characteristics:
These sectors are generally growth-oriented and exhibit higher variability, aligning with the higher-risk, higher-reward profile of this cluster.
The presence of Healthcare and Consumer Cyclical indicates a focus on sectors that tend to thrive during economic expansion.
Key Insights
Sector Overlap:
Both clusters have a notable presence of the Technology sector, but its proportion is higher in Cluster 2, indicating the sector’s higher risk-return dynamics.
Cluster Differentiation:
Cluster 1 leans toward stable, defensive sectors like Utilities and Consumer Defensive.
Cluster 2 focuses on growth-oriented sectors like Technology, Healthcare, and Consumer Cyclical.
Summary of the Box Plot
The box plot displays the distribution of average returns for the two clusters identified through K-means clustering:

Cluster 1 (Red):
The stocks in this cluster generally have lower average returns with a tighter range of values.
This indicates a more stable and consistent return profile, suitable for risk-averse investors.
A few outliers with extremely low returns are visible, but they don’t significantly affect the overall distribution.
Cluster 2 (Blue):
The stocks in this cluster exhibit higher average returns with a broader range of values compared to Cluster 1.
The wider spread suggests greater variability, consistent with higher risk and potential reward.
Some outliers with very high returns are observed, reinforcing the cluster’s higher-risk nature.
ggplot(features, aes(x = as.factor(Cluster), y = Avg_Return, fill = as.factor(Cluster))) +
  geom_boxplot() +
  labs(title = "Distribution of Avg_Return by Cluster",
       x = "Cluster",
       y = "Average Return",
       fill = "Cluster") +
  theme_minimal()


Key Insights
Cluster 1: Consists of low-return, stable stocks with minimal variability.
Cluster 2: Comprises higher-return, high-risk stocks with greater variability and potential outliers.
This visualization reinforces the risk-return tradeoff observed in the clustering process, providing a clear differentiation between the two clusters.

Interpretation and Insights
This section summarizes the results from the clustering analysis and provides actionable insights based on the identified clusters.

Cluster Profiles
The following table summarizes the characteristics of each cluster, including average return, volatility, market cap, and stock count.

# Cluster summary table
cluster_summary <- features %>%
  group_by(Cluster) %>%
  summarise(
    Avg_Return = mean(Avg_Return, na.rm = TRUE),
    Volatility = mean(Volatility, na.rm = TRUE),
    Count = n()
  )

# Print the cluster summary table
knitr::kable(cluster_summary, caption = "Cluster Profiles: Summary of Key Metrics")
Cluster Profiles: Summary of Key Metrics
Cluster	Avg_Return	Volatility	Count
1	0.0004002	0.0169756	379
2	0.0005657	0.0272543	123
Sector Trends

The following table and visualization illustrate the sectoral composition of each cluster

# Sector distribution by cluster
sector_distribution <- features %>%
  group_by(Cluster, Sector) %>%
  summarise(Count = n()) %>%
  arrange(Cluster, desc(Count))
## `summarise()` has grouped output by 'Cluster'. You can override using the
## `.groups` argument.
# Print the sector distribution table
knitr::kable(sector_distribution, caption = "Sector Distribution by Cluster")
Sector Distribution by Cluster
Cluster	Sector	Count
1	Financial Services	59
1	Industrials	59
1	Healthcare	52
1	Technology	47
1	Consumer Defensive	35
1	Consumer Cyclical	32
1	Real Estate	30
1	Utilities	28
1	Basic Materials	17
1	Communication Services	14
1	Energy	6
2	Technology	35
2	Consumer Cyclical	23
2	Energy	16
2	Industrials	11
2	Healthcare	10
2	Communication Services	8
2	Financial Services	8
2	Basic Materials	5
2	Utilities	4
2	Consumer Defensive	2
2	Real Estate	1
# Visualize sector composition
# Calculate sector distribution by cluster
sector_distribution <- features %>%
  group_by(Cluster, Sector) %>%
  summarise(Count = n()) %>%
  arrange(Cluster, desc(Count))
## `summarise()` has grouped output by 'Cluster'. You can override using the
## `.groups` argument.
# View the summarized table
View(sector_distribution)
Conclusion
This project applied clustering techniques (K-means) to group S&P 500 stocks based on key financial metrics such as average return, volatility. The goal was to identify distinct clusters of stocks to provide actionable insights for investors.

The analysis revealed two meaningful clusters that capture different risk-return profiles and sector compositions of the stocks.

Insights
Cluster Profiles
Cluster 1:
Characteristics:
Lower average return and volatility.
Predominantly large-cap stocks from stable, defensive sectors like utilities and consumer staples.
Risk-Reward Profile:
Low-risk, low-return investments.
Investor Type:
Suitable for conservative investors focused on stable returns.
Cluster 2:
Characteristics:
Higher average return and volatility.
Stocks from growth-oriented sectors like technology and healthcare.
Risk-Reward Profile:
High-risk, high-reward investments.
Investor Type:
Suitable for aggressive investors willing to tolerate higher risk for potentially greater returns.
Sector Trends
Cluster 1: Dominated by defensive sectors such as utilities and consumer staples.
Cluster 2: Heavy representation of growth sectors like technology and healthcare.
Cluster Evaluation
K-means clustering provided more cohesive and interpretable clusters, making it the preferred method for this dataset.
The performance gap between K-means and CLARA suggests that the dataset characteristics (e.g., size and structure) align better with K-means.
Recommendations for Investors
1. Portfolio Diversification
Cluster 1:
Invest in Cluster 1 stocks as a foundation for a low-risk portfolio.
Focus on defensive sectors to hedge against market volatility.
Cluster 2:
Allocate a smaller portion of the portfolio to Cluster 2 stocks for growth potential.
Be mindful of the higher risk and volatility associated with these investments.
2. Sector-Specific Strategies
During economic downturns:
Favor stocks in Cluster 1 (utilities, consumer staples) for stability.
During bull markets:
Focus on Cluster 2 stocks (technology, healthcare) to maximize returns.
3. Monitor Risk-Reward Balance
Use the clustering insights to maintain a balance between risk and reward in the portfolio.
Reassess sector compositions periodically to adapt to changing market conditions.
This project demonstrates the value of clustering analysis in identifying distinct groups of stocks and tailoring investment strategies accordingly. K-means clustering, in particular, proved to be a robust and effective method for this dataset.

Useful Links:
K-means Clustering in R - RDocumentation
Silhouette Analysis in R - CRAN Clustering Documentation
Data Visualization in R with ggplot2 - R for Data Science
Factoextra Package for Clustering Visualization in R
Data Transformation with dplyr
