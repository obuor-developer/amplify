# amplify
Serverless Web App Development Made Easy: A Complete Guide with AWS Amplify, DynamoDB, Lambda and API Gateway
Image description

Get ready to dive into the world of serverless web application development on AWS. In this series, we’ll guide you through the process of creating a dynamic web app that calculates the area of a rectangle based on user-provided length and width values. We’ll leverage the power of AWS Amplify for web hosting, AWS Lambda functions for real-time calculations, DynamoDB for storing and retrieving results, and API Gateway for seamless communication. By the end of this journey, you’ll have the skills to build a responsive and scalable solution that showcases the true potential of serverless architecture. Let’s embark on this development adventure together!

Click on the url link for a step-by-step guide approach: https://dev.to/isaac_obuor_4ec2278316110/serverless-web-app-development-made-easy-a-complete-guide-with-aws-amplify-dynamodb-lambda-and-353o 

Creating Frontend

Use the index.html file from the project files. Or simply open a text editor and copy the following code into an index.html file. Note the part with “YOUR API URL” as we will be filling this part with the API URL later.

<
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Rectangle</title>
    <!-- Styling for the client UI -->
    <style>
    h1 {
        color: #FFFFFF;
        font-family: system-ui;
  margin-left: 20px;
        }
 body {
        background-color: #222629;
        }
    label {
        color: #86C232;
        font-family: system-ui;
        font-size: 20px;
        margin-left: 20px;
  margin-top: 20px;
        }
     button {
        background-color: #86C232;
  border-color: #86C232;
  color: #FFFFFF;
        font-family: system-ui;
        font-size: 20px;
  font-weight: bold;
        margin-left: 30px;
  margin-top: 20px;
  width: 140px;
        }
  input {
        color: #222629;
        font-family: system-ui;
        font-size: 20px;
        margin-left: 10px;
  margin-top: 20px;
  width: 100px;
        }
    </style>
    <script>
        // callAPI function that takes the length and width numbers as parameters
        var callAPI = (length,width)=>{
            // instantiate a headers object
            var myHeaders = new Headers();
            // add content type header to object
            myHeaders.append("Content-Type", "application/json");
            // using built in JSON utility package turn object to string and store in a variable
            var raw = JSON.stringify({"length":length,"width":width});
            // create a JSON object with parameters for API call and store in a variable
            var requestOptions = {
                method: 'POST',
                headers: myHeaders,
                body: raw,
                redirect: 'follow'
            };
            // make API call with parameters and use promises to get response
            fetch("YOUR API URL", requestOptions)
            .then(response => response.text())
            .then(result => alert(JSON.parse(result).body))
            .catch(error => console.log('error', error));
        }
    </script>
</head>
<body>
    <h1>AREA OF A RECTANGLE!</h1>
 <form>
        <label>Length:</label>
        <input type="text" id="length">
        <label>Width:</label>
        <input type="text" id="width">
        <!-- set button onClick method to call function we defined passing input values as parameters -->
        <button type="button" onclick="callAPI(document.getElementById('length').value,document.getElementById('width').value)">CALCULATE</button>
    </form>
</body>
</html>
>

The file should look like this when opened on a browser. It gives spaces to input the length and width of a rectangle and a ‘Calculate’ button
Image description

Hosting the App on AWS Amplify

On your AWS console search box, search for ‘Amplify’ and click on the first option that appears
Image description

Click on "Create new app"
Image description

select the source for your app files. They can be in a remote repository or local. We will use ‘GitHub’ since our files are in GitHub. We also need to use a compressed folder with our files. Click on ‘Next’
Image description

We have to make sure the Amplify GitHub App has permission to the repository and select your file. Click on 'Next'
Image description

Name your App and edit YML file. Copy this code and paste it into the YML file. Click on 'Next'

version: 1
frontend:
phases:
preBuild:
commands: []
build:
commands: []
artifacts:
baseDirectory: .
files:
- '*/'
cache:
paths: []

Image description

Click on 'Save and Deploy'.
Image description

Once the deployment is complete, click on the Domain to access your app.
Image description

The app opens on the browser. (You might need to refresh the deployment page on Amplify.
Image description

*Creating a Lambda Function to do the Math
*

On the AWS console search bar, type ‘Lambda’ and select the Lambda service
Image description

Click on 'Create a function'
Image description

Give the Function name, The Runtime(Latest Python), then scroll down and click on ‘Create function’
Image description

Image description

Copy the following Lambda function onto your lambda_function.py file. Please note the DynamoDB name. We will be use this name later as we create the DB.

``
# import the JSON utility package
import json

# import the AWS SDK (for Python the package name is boto3)
import boto3

# import two packages to help us with dates and date formatting
from time import gmtime, strftime

# create a DynamoDB object using the AWS SDK
dynamodb = boto3.resource('dynamodb')

# use the DynamoDB object to select our table
table = dynamodb.Table('AreaDatabase')

# store the current time in a human readable format in a variable
now = strftime("%a, %d %b %Y %H:%M:%S +0000", gmtime())

# define the handler function that the Lambda service will use an entry point
def lambda_handler(event, context):

# extract the two numbers from the Lambda service's event object
    Area = int(event['length']) * int(event['width'])

# write result and time to the DynamoDB table using the object we instantiated and save response in a variable
    response = table.put_item(
        Item={
            'ID': str(Area),
            'LatestGreetingTime':now
            })

# return a properly formatted JSON object
    return {
    'statusCode': 200,
    'body': json.dumps('Your result is ' + str(Area))
    }
    ``
Select 'code' and paste your lambda_function.py code. Click on 'Deploy'
Image description

Create an API Gateway_

On the AWS services search box, enter ‘API’ and select ‘API Gateway’ that appears
Image description

In the list for ‘Choose an API type’, select ‘Create REST API’
Image description

Image description

Select ‘New API’ under ‘Create new API’ and give the API a name, then click on ‘Create API’
Image description

On the page that appears, select ‘Resources’ on the Left Panel, select ‘Create method’. Select ‘POST’ on the drop-down that appears, then click on the ✔. Select ‘Lambda Function’ as the Integration type and type or choose the name of the lambda function in the ‘Lambda Function’ box. Click on ‘Create method’
Image description

Image description

Image description

Once all the checks are complete, click on ‘Deploy API’
Image description

Give the ‘Stage name’, then click ‘Deploy’
Image description

The Invoke URL is what you replace “YOUR API URL” with on the index.html file. Insert the URL, regenerate the index.html file and reupload to Amplify
Image description

``Invoke URL: https://j2hiyb31be.execute-api.us-east-1.amazonaws.com/mycloud ``

Setting up a Database on DynamoDB to store results

On the services search box, search for ‘DynamoDB’ and select the DynamoDB service
Image description

Click on ‘Create table’
Image description

Give the table a name, for ‘Partition key’ input ‘ID’. Leave the rest as default, scroll to the bottom and click on ‘Create table’
Image description

Select the table name. Under the settings tab, expand ‘General information’, then take note of the ARN
Image description

``arn:aws:dynamodb:us-east-1:266735826605:table/web-table``

Let’s add permissions to our Lambda function to access DynamoDB. On the Lambda function window, select the ‘Configuration’ tab, then ‘Permissions’ on the left side panel, and select the Role name.
Image description

A new tab opens in IAM and we can add permissions to the role. Click on ‘Add permissions’, then ‘Create inline policy’
Image description

Select the JSON Tab and copy the following policy. Replace “YOUR-TABLE-ARN” with the ARN of your table that we copied in step 4, then click ‘Next’ at the bottom
Image description

``
{
"Version": "2012-10-17",
"Statement": [
{
"Sid": "VisualEditor0",
"Effect": "Allow",
"Action": [
"dynamodb:PutItem",
"dynamodb:DeleteItem",
"dynamodb:GetItem",
"dynamodb:Scan",
"dynamodb:Query",
"dynamodb:UpdateItem"
],
"Resource": "YOUR-TABLE-ARN"
}
]
}
``

On the ‘Review and create’ page, give the policy a name then click on ‘Create policy’ at the bottom of the page
Image description

Now the Lambda function has permissions to write to the DB
Testing

Now that we are done, let’s see what we have. Open the AWS Amplify domain. It should open our app.

Input values for the Length and Width and click on “Calculate”. The solution should pop up on the screen. (Returned in the browser through API Gateway)
Image description
