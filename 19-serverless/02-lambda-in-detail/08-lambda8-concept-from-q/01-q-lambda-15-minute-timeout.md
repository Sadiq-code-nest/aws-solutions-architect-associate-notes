# Q-Lambda 15-Minute Timeout

An application is using an AWS Lambda function to process complex financial data that runs for 15 minutes on average. The Lambda function has been configured with the maximum memory allocation to handle the computational demands of the financial data processing. Most invocations were successfully processed. However, a few terminated invocations were noticed throughout the day, which caused a data discrepancy in the application.

Which of the following is the most likely cause of this issue?

- The concurrent execution limit has been reached.
- The Lambda function contains a recursive code and has been running for over 15 minutes.
- The failed Lambda functions have been running for over 15 minutes and reached the maximum execution time.
- The failed Lambda Invocations contain a `ServiceException` error which means that the Lambda service encountered an internal error.
