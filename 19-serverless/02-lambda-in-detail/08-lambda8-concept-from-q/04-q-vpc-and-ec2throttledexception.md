# Q- VPC & EC2ThrottledException

A company is building an internal application that allows users to upload images. Each upload request must be sent to Amazon Kinesis Data Streams for processing before the pictures are stored in an Amazon S3 bucket.

The application should immediately return a success message to the user after the upload, while the downstream processing is handled asynchronously. The processing typically takes about 5 minutes to complete.

Which solution will enable asynchronous processing from Kinesis to S3 in the most cost-effective way?

- Use Kinesis Data Streams with AWS Lambda consumers to asynchronously process records and write them to S3.
- Use a combination of Amazon SQS to queue the requests and then asynchronously process them using On-Demand Amazon EC2 Instances.
- Send data from Kinesis Data Streams to Amazon Data Firehose and configure it to deliver directly to S3.
- Use a combination of AWS Lambda and Step Functions to orchestrate service components and asynchronously process the requests.
