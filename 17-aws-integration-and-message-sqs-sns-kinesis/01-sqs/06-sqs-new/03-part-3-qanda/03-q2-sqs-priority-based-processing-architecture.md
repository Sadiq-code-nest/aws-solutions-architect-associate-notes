# Q2 - SQS Priority-Based Processing Architecture

A company launched a website that accepts high-quality photos and turns the photos into a downloadable video montage. The website offers both a free and a premium account, with the premium account guaranteeing faster processing. All requests by both free and premium members go through a single Amazon SQS queue and are then processed by a group of Amazon EC2 instances that generate the videos. The company needs to ensure that premium users, who paid for the service, have higher priority than free members.

How should the company re-design its architecture to address this requirement?

- **Create an SQS queue for free members and another one for premium members. Configure your EC2 instances to consume messages from the premium queue first and if it is empty, poll from the free members' SQS queue.**
- Use Amazon Kinesis to process the photos and generate the video montage in real-time.
- Use Amazon S3 to store and process the photos and then generate the video montage afterward.
- For the requests made by premium members, set a higher priority in the SQS queue so it will be processed first compared to the requests made by free members.

explain me topic like order dispatcher , priority order ques and topic like this explain in this question and attached picture.. in a note with short def, example, snenaio, where to use.. make thgat note
