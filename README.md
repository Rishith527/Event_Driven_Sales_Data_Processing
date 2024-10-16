# Event_Driven_Sales_Data_Processing



## Project Overview

This project implements an event-driven sales data processing pipeline using AWS services. When a new sales data JSON file is uploaded to an S3 bucket, an EventBridge rule triggers a Step Function workflow. The Step Function invokes a Lambda function to validate the data. If the sales data contains both `contact-info` and `order-info`, it is sent to a DynamoDB table. If the data is invalid, it is sent to an SQS Dead Letter Queue (DLQ) for further inspection.

### Architecture



- **S3**: Stores incoming sales data in JSON format.
- **EventBridge**: Monitors the S3 bucket for new files and triggers the Step Function.
- **Step Functions**: Orchestrates the data validation and routing process.
- **Lambda**: Validates the JSON data for required fields (`contact-info` and `order-info`).
- **DynamoDB**: Stores valid sales data.
- **SQS (DLQ)**: Stores invalid sales data for future review.

## Technologies Used

- **AWS S3**: Object storage for uploaded sales data.
- **AWS Lambda**: Serverless compute for validating the data.
- **AWS Step Functions**: Workflow orchestration for managing the data pipeline.
- **AWS EventBridge**: Event-driven service to detect new S3 objects.
- **AWS DynamoDB**: NoSQL database to store valid sales data.
- **AWS SQS**: Queue to store invalid sales data (Dead Letter Queue).

## How It Works

1. A JSON file containing sales order data is uploaded to an S3 bucket.
2. EventBridge triggers a Step Function that passes the file to a Lambda function.
3. Lambda validates the sales data:
   - If valid, it is stored in DynamoDB.
   - If invalid, it is sent to the SQS DLQ for further review.
   
### Data Validation Logic

The Lambda function checks if the uploaded JSON data includes both `contact-info` and `order-info`. Records missing these fields are treated as invalid and routed to the DLQ.

### Sample JSON Structure of a record

```json
{
  "order-info": {
    "order_id": "1234",
    "item": "Laptop",
    "quantity": 1
  },
  "contact-info": {
    "name": "John Doe",
    "email": "johndoe@example.com"
  }
}
