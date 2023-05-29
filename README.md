## Using Amazon SageMaker Feature Store with streaming feature aggregation

### Overview:

Amazon SageMaker Feature Store is a purpose-built repository where you can store and access features so it’s much easier to name, organize, and reuse them across teams. SageMaker Feature Store provides a unified store for features during training and real-time inference without the need to write additional code or create manual processes to keep features consistent.

This implementation shows you how to do the following:

* Create multiple SageMaker Feature Groups to store aggregate data from a credit card dataset
* Run a SageMaker Processing Spark job to aggregate raw features and derive new features for model training
* Train a SageMaker XGBoost model and deploy it as an endpoint for real time inference
* Generate simulated credit card transactions sending them to Amazon Kinesis 
* Use KDA SQL to aggregate features in near real time, triggering a Lambda function to update feature values in an online-only feature group
* Trigger a Lambda function to invoke the SageMaker endpoint and detect fraudulent transactions

### Prerequisites

Prior to running the steps under Instructions, you will need access to an AWS Account where you have full Admin privileges. The CloudFormation template will deploy multiple AWS Lambda functions, IAM Roles, and a new SageMaker notebook instance with this repo already cloned. In addition, having basic knowledge of the following services will be valuable: Amazon Kinesis streams, Amazon Kinesis Data Analytics, Amazon SageMaker, AWS Lambda functions, Amazon IAM Roles.

### Instructions

1. Create stack with command 'aws cloudformation create-stack --stack-name cc-transactions-stack --template-body file://template.yaml --capabilities CAPABILITY_IAM CAPABILITY_NAMED_IAM --region ap-south-1'

3. Once the stack is complete, browse to Amazon SageMaker in the AWS console and click on the 'Notebook Instances' tab on the left. 
4. Click either 'Jupyter' or 'JupyterLab' to access the SageMaker Notebook instance. The Cloudformation template has cloned this git repository into the notebook instance for you. All of the example code to work through is in the notebooks directory. 


There are a series of notebooks which should be run in order. Follow the step-by-step guide in each notebook:

* [notebooks/0_prepare_transactions_dataset.ipynb](./notebooks/0_prepare_transactions_dataset.ipynb) - generate synthetic dataset
* [notebooks/1_setup.ipynb](./notebooks/1_setup.ipynb) - create feature groups and Kinesis resources (can run this in parallel with notebook 0, no dependencies between them)
* [notebooks/2_batch_ingestion.ipynb](./notebooks/2_batch_ingestion.ipynb) - igest one-week aggregate features, and create training dataset
* [notebooks/3_train_and_deploy_model.ipynb](./notebooks/3_train_and_deploy_model.ipynb) - train and deploy fraud detection model
* [notebooks/4_streaming_predictions.ipynb](./notebooks/4_streaming_predictions.ipynb) - make fraud predictions on streaming transactions

#### Optional steps
- View the Kinesis Stream that is used to ingest records.
- View the Kinesis Data Analytics SQL query that pulls data from the stream.
- View the Lambda function that receives the initial kinesis events and writes to the FeatureStore.
- View the Lambda function that receives the final kinesis events and triggers the model prediction.

### **CLEAN UP**
To destroy the AWS resources created as part of this example, complete the following two steps:
1. Run all cells in [notebooks/5_cleanup.ipynb](./notebooks/5_cleanup.ipynb) 
2. Delete stack with command 'aws cloudformation delete-stack --stack-name cc-transactions-stack'
