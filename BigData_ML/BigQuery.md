# Introduction to Big Query
Bigquery provides two fundamental resources:
1. SQL Query Enginer (Query Service)
2. Managed storage for datasets (Storage Service)

Big Query is serverless, meaning it is fully managed. This means it can auto-scale to a given storage and query. 
The datasets in Big Query is contained within projects. You can navigate this with your project ID. 
most efficient working ondata contained within these projects and datasets.
This can be connected to other services with dataproc/dataflow to build pipelines. 

Controlling resource consumption and costs can be facilitated by optimising what data is contained in the query. 
1tb query, 10gbs storage per month free. 


### Data Quality
Dataprep - visualizes data, similar to a df.describe() at a more sohpistacted view with interactive options.


### IAM Project Rules
Project Rules and inherited BigQuery Permiossions
When a project is created, BigQuery grants the owner role to the user who created the project.
- Viewer
  - Can start a job in the project, list and get all jobs, update jobs that they started for the project
  - BigQuery grants exisitng viewer defined users predefined viewer roles for any new datasets. 
- Editor
  - Same as viewer, plus being able to create a new dataset in the project
  - BigQuert grants existing users that are editors the editor role for any new datasets. 
- Owner
  - Same as viewer, plus can list all datasets in the project
  - can delete any dataset in the project
  - can list and get all jobs run on the project, including jobs run by other project users
  - BigQuery grants existing users with Owner permissions owner roles in any new datasets. 
 Recommended to use different projects or datasets for different environments (eg. DEV, QA, PRD)


Transformations might favour cloud dataflow when ingesting data into BigQuery, but it is possible to ingest data straight into BigQuery

