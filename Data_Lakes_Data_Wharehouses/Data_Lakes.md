# Data Lakes
Data Lakes securely store various types of data of all scales for processing and analytics.
They accept all types of data, are portable and on-premise or in the cloud. 
Data Lakes are typically used to drive data analytics, data science and ML workloads or batch and streaming data pipelines.


### Data Lakes in a ecosystem
Data Scources -> data sinks (central data lake repository / data warehouse) -> dta pipelines (batch and streaming) -> high-level orchestration workflows.
Data lakes facilitate reliable ways of toring the raw data collected from data sources. It is central and does not onsider the data volume, format or velocity. This factors are considered in data warehouses and pipelines. 
For data lakes, GCP offers a variety of cloud storage solutions. Traditional use case would involve Cloud Storage buckets. 


### Data Storage options
GCP offers the following data storage solutions:
- Cloud storage (catch-all)
- Cloud SQL and Cloud Spanner (relational data
- Firestore and Cloud Bigtable (NoSQL data)

To determine the optimal service, the use case must be considered. This includes:
- understanding how large you data is
- where you data is now, where it has to land
- what traansformations are necessary

### ETL vs. ELT
ETL and ELT represent two options when ingesting data into a data lake or warehouse. The first, ETL, involves the extraction of raw data from sources, transforming this data into the use case and loading into a data lake or warehouse. Alternatively, ELT loads the data before then transforming. The optimal order or processing can facilitate less resouce demand, faster processing and reduced costs.   

ELT is favourable when:
- the amount of transformation necessary is not very high
- the transformation will not greatly reduce the amount of data that you have
It is important the remember that expenses are influenced by data quantity.  

Alternatively, ETL is favourable when:
- transformation is essential 
- transformation greatl;y reduces the data volume
- your data is in some propriety format
