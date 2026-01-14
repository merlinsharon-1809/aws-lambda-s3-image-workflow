Serverless Image Workflow using AWS Lambda and S3
Project Title
Serverless Image Processing Workflow with Automated S3 Trigger Using AWS Lambda
Project Objective
The objective of this project is to develop a fully functional, beginner-friendly serverless workflow on AWS. Images uploaded to a designated S3 bucket folder (input/) are automatically detected and processed by an AWS Lambda function. The processed images are then stored in a separate folder (output/) within the same bucket. This project demonstrates the use of serverless computing, event-driven architecture, AWS Lambda functions, and S3 integration, providing a strong foundation for cloud-based applications.
Tools and Technologies Used
•	Amazon S3: Scalable object storage for storing input and output images.
•	AWS Lambda: Serverless compute service used to execute code in response to S3 events.
•	Python 3.10: Chosen runtime environment for Lambda function development.
•	Boto3: AWS SDK for Python to interact with AWS services such as S3.
•	JSON: Data format used for Lambda events and responses.
•	CloudWatch Logs: For monitoring, debugging, and logging Lambda function executions.
Project Workflow
The project follows a structured serverless workflow outlined below:
1. S3 Bucket Setup
1.	Log in to the AWS Management Console and navigate to Amazon S3.
2.	Create a new S3 bucket (example: merlin-image-input-123). Bucket names must be globally unique.
3.	Inside the bucket, create two folders:
o	input/ → All images to be processed are uploaded here.
o	output/ → Processed images are saved here.
4.	Configure the bucket settings:
o	Bucket Versioning: Disabled to simplify management.
o	Default Encryption: Disabled for ease of use.
o	Object Ownership: Set to Bucket owner preferred to manage file access.
2. Lambda Function Setup
1.	Navigate to AWS Lambda → Create Function.
2.	Configure the Lambda function with the following settings:
o	Function Name: image-copy-lambda
o	Runtime: Python 3.10
o	Execution Role: Create a new role with S3 full access and basic Lambda permissions.
3.	Deploy a basic Lambda test function to verify execution.
3. S3 Trigger Configuration
1.	Open the Lambda function’s Configuration → Triggers section.
2.	Add an S3 trigger for the bucket created:
o	Event Type: All object create events
o	Prefix: input/
3.	This ensures that any new image uploaded to the input/ folder triggers the Lambda function automatically.
4. Lambda Function Code
The following Lambda function automatically copies uploaded images from the input/ folder to the output/ folder. It adds a timestamp prefix to ensure uniqueness and prevent overwriting.
import json
import boto3
from datetime import datetime

# Initialize S3 client
s3 = boto3.client('s3')

def lambda_handler(event, context):
    # Retrieve bucket name and uploaded file key from the event
    bucket_name = event['Records'][0]['s3']['bucket']['name']
    input_file = event['Records'][0]['s3']['object']['key']

    # Only process files in the 'input/' folder
    if not input_file.startswith("input/"):
        return {"statusCode": 200, "body": "File not in input folder; skipping processing."}

    # Generate timestamped output file name for uniqueness
    timestamp = datetime.now().strftime("%Y%m%d%H%M%S")
    output_file = input_file.replace("input/", f"output/{timestamp}_")

    # Copy the file from input to output folder
    copy_source = {'Bucket': bucket_name, 'Key': input_file}
    s3.copy_object(Bucket=bucket_name, Key=output_file, CopySource=copy_source)

    # Log the operation for monitoring and debugging
    print(f"Copied {input_file} to {output_file} successfully.")

    return {"statusCode": 200, "body": f"Image copied successfully to {output_file}"}
5. Deployment and Testing
1.	Click Deploy to save changes to the Lambda function.
2.	Verify that the Lambda execution role has S3 read/write permissions.
3.	Upload an image (.jpg or .png) to the S3 input/ folder.
4.	Lambda automatically triggers and processes the image.
5.	Verify that the processed image appears in the output/ folder with a timestamped filename.
6.	Monitor CloudWatch Logs to confirm execution:
Copied input/myimage.jpg to output/20260101123456_myimage.jpg
6. Monitoring and Logging
•	CloudWatch Logs provide detailed insights into Lambda executions.
•	Each upload triggers a log entry showing the bucket name, file name, and timestamp.
•	This facilitates monitoring, debugging, and ensures transparency of the automated process.
7. Project Highlights
•	Demonstrates a complete serverless architecture using AWS Lambda and S3.
•	Implements an event-driven workflow triggered by file uploads.
•	Fully functional without external libraries like Pillow, making it beginner-friendly.
•	Ensures unique output filenames using timestamp prefix.
•	Extensible: Can later include image resizing, watermarking, or compression.
•	Provides detailed logging and monitoring for all automated operations.
8. Conclusion
This project successfully implements a beginner-friendly, serverless workflow that automates image handling in AWS. Images uploaded to the S3 input/ folder are detected and copied to the output/ folder in real-time, demonstrating a practical application of AWS Lambda, S3, and CloudWatch. The workflow is scalable, fully serverless, and can be extended to include advanced image processing functionalities in the future.
