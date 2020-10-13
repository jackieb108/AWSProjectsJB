This project loads CSV file from S3 bucket to Redshift table using AWS Glue tool. See AWS page for details. Below diagram is picked from aws pattern page (https://docs.aws.amazon.com/prescriptive-guidance/latest/patterns/load-data-from-amazon-s3-to-amazon-redshift-using-aws-glue.html)


![image](/AWSTransferS3toRedshiftUsingGlue/assets/images/awstransferfroms3toredshiftarchi.jpg) 

Step1:
1. Clone the repo

    `$git clone https://github.com/jackieb108/AWSProjectsJB.git`
  
2. Transfer 'Customer.csv' to S3 bucket using aws console. Note the name of the bucket. We will call it 'CUSTOMERS3BUCKET' for reference

3. Create VPC endpoint to let services within that VPC access 'CUSTOMERS3BUCKET' S3 bucket. Reference: https://docs.aws.amazon.com/glue/latest/dg/vpc-endpoints-s3.html

4. Create Redshift Cluster. Will have update security settings to let local SQL workbench access redshift instance. Check the setup here - https://www.sqlshack.com/access-aws-redshift-from-a-locally-installed-ide/

5. Install SQL Workbench on local laptop to be able to connect to Redshift instance and check data. Need to download SQL workbench and Redshift driver. Ref: (https://docs.aws.amazon.com/redshift/latest/mgmt/connecting-using-workbench.html)

6. Glue setup (followed tutorial at: https://majestic.cloud/importing-csv-files-from-s3-to-redshift-with-aws-glue/)
    > Create `AWS Glue -> Crawlers -> Classifiers`. Add CSV classifier
    > Create 'AWS Glue -> Crawler` to read csv file(Customer.csv)  from S3 'CUSTOMERS3BUCKET' bucket. Use the classifier above. Create a new or use existing IAM role. Add Database (`CSVDataFromS3`). Run the crawler. This creates the 'csvdaatafroms3' DB and the associated meta data
    > Create redshift connection 'AWS Glue -> Databases -> Connections`. Call is 'RedshiftConn'. Select teh previously created cluster. I twill chose teh DB and user for you. Test the connection
    > Connect to redshift using SQL workbench and create your table that matches the CSV data manually.
    > Create another crawler for redshift database. Create 'AWS Glue -> Crawler` . Name is 'RedshiftTableCrawler'. Choose JDBC type data store and 'RedshiftConn'. Add table path '<database>/<schema>/customer_csv'. Choose IAM role. Run this crawler. Creates schema for redshift db ('customerdevdw_public_customer')
 7. Create 'AWS Glue -> JOB'. 'TransferFromS3toRedshift'. Set JOb bookmanrk to 'Enable'
    > Choose data source as 'customer_csv' and destination as redshift table. See and confirm teh mapping
    

Test S3 connectivity from Glue. Ref:https://docs.aws.amazon.com/glue/latest/dg/connection-S3-VPC.html (`Troubleshooting`)

See the results in redshift DB/table
![image](/AWSTransferS3toRedshiftUsingGlue/assets/images/dwdatainredshift.jpg) 


Automated scala code generated for AWS Glue job that can be modified further for transforming data before loading in warehouse
![image](/AWSTransferS3toRedshiftUsingGlue/assets/images/automatedscalacodeonawsgluejob.jpg) 
