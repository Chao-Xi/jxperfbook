# **L8 Data Analyst Interview**

### **1、What are the important responsibilities of a data analyst?**

* Collect and interpret data from multiple sources and analyze results.
* Filter and “clean” data gathered from multiple sources.
* Offer support to every aspect of data analysis.
* Analyze complex datasets and identify the hidden patterns in them.
* Keep databases secured.

### **2、What does “Data Cleansing” mean? What are the best ways to practice this?**

The best ways to clean data are:

* **Segregating data**, according to their respective attributes.
* Breaking large chunks of data into small datasets and then cleaning them.
* Analyzing the statistics of each data column.
* Creating a set of utility functions or scripts for dealing with common cleaning tasks.
* Keeping track of all the data cleansing operations to facilitate easy addition or removal from the datasets, if required.


### **3、 Name the best tools used for data analysis.**

* Tableau
* Google Fusion Tables
* Google Search Operators
* KNIME
* RapidMiner
* Solver
* OpenRefine
* NodeXL
* io

### **4、What is the difference between data profiling and data mining?**

Data Profiling focuses on **analyzing individual attributes of data**, thereby **providing valuable information on data attributes such as data type, frequency, length, along with their discrete values and value ranges**. 

On the contrary, **data mining aims to identify unusual records, analyze data clusters, and sequence discovery, to name a few.**

### **5、What is KNN imputation method?**

KNN imputation method seeks to **impute the values of the missing attributes using those attribute values that are nearest to the missing attribute values.**

The similarity **between two attribute values is determined using the distance function**.


### **6、What should a data analyst do with missing or suspected data?**

* Use data analysis strategies like **deletion method, single imputation methods, and model-based methods to detect missing data**.
* Prepare a validation report containing all information about the suspected or missing data.
* **Scrutinize the suspicious data to assess their validity**.
* Replace all the invalid data (if any) with a proper validation code.

### **7. Name the different data validation methods used by data analysts.**


* **Field Level Validation** – In this method, data validation is done in each field as and when a user enters the data. It helps to correct the errors as you go.
* **Form Level Validation** – In this method, the data is validated after the user completes the form and submits it. It checks the entire data entry form at once, validates all the fields in it, and highlights the errors (if any) so that the user can correct it. 
* **Data Saving Validation** – This data validation technique is used during the process of saving an actual file or database record. Usually, it is done when multiple data entry forms must be validated. 
* **Search Criteria Validation** – This validation technique is used to offer the user accurate and related matches for their searched keywords or phrases. The main purpose of this validation method is to ensure that the user’s search queries can return the most relevant results.


### **8. Define Outlier**

There are two kinds of outliers – **Univariate and Multivariate**.

The two methods used for detecting outliers are:


* **Box plot method** – According to this method, if the value is higher or lesser than `1.5*IQR` (interquartile range), such that it lies above the upper quartile (Q3) or below the lower quartile (Q1), the value is an outlier.
* **Standard deviation method** – This method states that if a value is higher or lower than mean `± (3*standard deviation)`, it is an outlier.


### **9. What is “Clustering?” Name the properties of clustering algorithms.**

Clustering is a method in which data is classified into clusters and groups. A clustering algorithm has the following properties:


* Hierarchical or flat
* Hard and soft
* Iterative
* Disjunctive

### **10. What is K-mean Algorithm?**


**K-mean is a partitioning technique in which objects are categorized into K groups**. 

In this algorithm, the clusters are spherical with the data points are aligned around that cluster, and the variance of the clusters is similar to one another.

### **11. Define “Collaborative Filtering”.**

Collaborative filtering is an algorithm that creates a recommendation system based on the behavioral data of a user. 


For instance, online shopping sites usually compile a list of items under “recommended for you” based on your browsing history and previous purchases. The crucial components of this algorithm include users, objects, and their interest.


### **12. Name the statistical methods that are highly beneficial for data analysts?**

The statistical methods that are mostly used by data analysts are:

* Bayesian method
* Markov process
* Simplex algorithm
* Imputation
* Spatial and cluster processes
* Rank statistics, percentile, outliers detection
* Mathematical optimization

### **13. What is an N-gram?**

An n-gram is a connected sequence of n items in a given text or speech. 

Precisely, an N-gram is a probabilistic language model used to predict the next item in a particular sequence, as in `(n-1)`.

### **14. What is a hash table collision? How can it be prevented?**


When two separate keys hash to a common value, a hash table collision occurs. This means that two different data cannot be stored in the same slot.

Hash collisions can be avoided by:

* **Separate chaining – In this method, a data structure is used to store multiple items hashing to a common slot**.
* **Open addressing – This method seeks out empty slots and stores the item in the first empty slot available**.

### **15. Define “Time Series Analysis”.**

Series analysis can usually be performed in two domains – **time domain and frequency domain**.


Time series analysis is the method where the output forecast of a process is done by analyzing the data collected in the past using techniques like exponential smoothening, log-linear regression method, etc.

### **16. How should you tackle multi-source problems?**


* **Identify similar data records and combine them into one record that will contain all the useful attributes, minus the redundancy.**
* Facilitate schema integration through schema restructuring.


### **17. Mention the steps of a Data Analysis project.**

* The foremost requirement of a Data Analysis project is an in-depth understanding of the business requirements. 
* The second step is to identify the most relevant data sources that best fit the business requirements and obtain the data from reliable and verified sources. 
* The third step involves exploring the datasets, cleaning the data, and organizing the same to gain a better understanding of the data at hand. 
* In the fourth step, Data Analysts must validate the data.
* The fifth step involves implementing and tracking the datasets.
* The final step is to create a list of the most probable outcomes and iterate until the desired results are accomplished.

### **18. What are the problems that a Data Analyst can encounter while performing data analysis?**

* Presence of duplicate entries and spelling mistakes. These errors can hamper data quality.
* Poor quality data acquired from unreliable sources. In such a case, a Data Analyst will have to spend a significant amount of time in cleansing the data. 
* Data extracted from multiple sources may vary in representation. Once the collected data is combined after being cleansed and organized, the variations in data representation may cause a delay in the analysis process.
* Incomplete data is another major challenge in the data analysis process. It would inevitably lead to erroneous or faulty results. 

### **19. What are the characteristics of a good data model?**

* It should have predictable performance so that the outcomes can be estimated accurately, or at least, with near accuracy.
* It should be adaptive and responsive to changes so that it can accommodate the growing business needs from time to time. 
* It should be capable of scaling in proportion to the changes in data. 
* It should be consumable to allow clients/customers to reap tangible and profitable results.

### **20. Differentiate between variance and covariance.**

Variance and covariance are both statistical terms. 

Variance depicts how distant two numbers (quantities) are in relation to the mean value. **So, you will only know the magnitude of the relationship between the two quantities (how much the data is spread around the mean)**. 

On the contrary, **covariance depicts how two random variables will change together.** 

Thus, covariance gives both the direction and magnitude of how two quantities vary with respect to each other.

### **21. Explain “Normal Distribution.”**

Normal distribution, better known as the Bell Curve or Gaussian curve, refers to a probability function that describes and measures how the values of a variable are distributed, that is, how they differ in their means and their standard deviations. 

In the curve, the distribution is symmetric. While most of the observations cluster around the central peak, probabilities for the values steer further away from the mean, tapering off equally in both directions.


### **22. Explain univariate, bivariate, and multivariate analysis.**

Univariate analysis refers to a descriptive statistical technique that is applied to datasets containing a single variable. The univariate analysis considers the range of values and also the central tendency of the values. 

Bivariate analysis simultaneously analyzes two variables to explore the possibilities of an empirical relationship between them. It tries to determine if there is an association between the two variables and the strength of the association, or if there are any differences between the variables and what is the importance of these differences.  

Multivariate analysis is an extension of bivariate analysis. Based on the principles of multivariate statistics, the multivariate analysis observes and analyzes multiple variables (two or more independent variables) simultaneously to predict the value of a dependent variable for the individual subjects.

### **23. Explain the difference between R-Squared and Adjusted R-Squared.**

The R-Squared technique is a statistical measure of the proportion of variation in the dependent variables, as explained by the independent variables. 


The Adjusted R-Squared is essentially a modified version of R-squared, **adjusted for the number of predictors in a model.** It provides the percentage of variation explained by the specific independent variables that have a direct impact on the dependent variables.

### **24. What are the advantages of version control?**

* It allows you to compare files, identify differences, and consolidate the changes seamlessly. 
* It helps to keep track of application builds by identifying which version is under which category – development, testing, QA, and production.
* It maintains a complete history of project files that comes in handy if ever there’s a central server breakdown.
* It is excellent for storing and maintaining multiple versions and variants of code files securely.
* It allows you to see the changes made in the content of different files.

### **25. How can a Data Analyst highlight cells containing negative values in an Excel sheet?**

* First, select the cells that have negative values.
* Now, go to the Home tab and choose the Conditional Formatting option.
* Then, go to the Highlight Cell Rules and select the Less Than option.
* In the final step, you must go to the dialog box of the Less Than option and enter “0” as the value.