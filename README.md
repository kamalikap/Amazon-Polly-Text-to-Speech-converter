
# Amazon Polly: Text to Speech Converter

Using:

* Lambda
* S3
* DynamoDB
* IAM (Identity Access Management)
* API Gateway
* SNS (Simple Notification Service)

Download the Repository. Open your AWS Console and follow the below steps to make Amazon Polly Speak.


1. First check your region and make sure it's Northern-Virginia.

2. Go to DynamoDB under Databases. Create Table
  * Table name - posts
  * Primary key- id
  * Hit Create

3. Go to S3. Create two buckets poly.mp3 and polly.website. Make both public by going to "Edit public access settings".

4. Go to "Simple Notification Service" in Application Integration. 
  *Create topic
    * Topic name- new_posts
    * Display name- New Posts

5. Go to "IAM" in Security and compliance. Go to Roles
    * Create Role
        * Select Lambda then Next
        * Create Policy
             * click Json and paste the lambdapolicy.json.
             * Name- Lambda-policy-polly
             *Description -Lambda-policy-polly and Create Policy
    * Go  back to create role and then lambda, then search for "Lambda-policy-polly", next.
      * Role name- Lambda-policy-polly
      * Description- Lambda-policy-polly
      * Create Role

6. Open Lambda and create a function
    * Author from Scratch, then 
        *Name- PostReader_New-posts
        *Runtime- Python 2.7
        *Role-Choose existing role
        *Existing role - Lambda-policy-polly then create function
    * Copy the code from newposts.py to function code. 
    * Copy "DB_TABLE_NAME" then paste it to environmental variables as key then write value as "posts". Copy whole "arn" of newposts in SNS and paste it.
        * DB_TABLE_NAME - posts
        * SNS_TOPIC - arn:aws:sns:us-east-1:307328585937:new_posts
    * Basic description - This post creates dynamodb.
    * Click Save.
    * Click Test
       * Event name- HelloJoanna
       * Get sample.json from directory and paste in the json file.
       * Click Create
       * Click Test
       * Click on Details

7. Go to DynamoDB under Databases.
    * Click Tables-> posts->items-> The post is created with unique ID.

8. Creating second Lambda function-
    * Call it "PostReader_ConvertToAudio" and do the same specifications and create the function.
    * In Designer click SNS 
    * In Configure trigger select new_posts and click Add then Save.
    * Go to Function code- and paste the converttoaudio.py code.
    * In the Environmental variables, we have :
       * DB_TABLE_NAME - posts
       * BUCKET_NAME -poly.mp3
    * Basic settings- This lambda function converts my text to audio and saves in S3. Click Save.
       * Timeout-5 min.


 9. Third lambda function is to "Get Posts"
    * Create a function & Call it "PostReader_GetPosts" and do the same specifications and create the function.
    * Go to Function code- and paste the getposts.py code.
    * In the Environmental variables, we have :
       * DB_TABLE_NAME - posts
    * Basic settings- This gets all posts from our DynamoDB Table. Click Save.
    * Click Test
       * Event name- TestEvent
       * {
  			"postId": "*"
		 }
       * Click Create
       * Click Test
       * Click on Details


10. Go to API Gateway in Networking and Content Delivery
    * Create API
    * API name - PostReaderAPI
    * Description - My API for polly
    * Create API
    * Actions and "Create Method"
    * Select "GET" and click on the tick
    * In Lambda function select PostReader_GetPOsts and click Save.
    * Select "POST" and click on the tick
    * In Lambda function select PostReader_NewPOsts and click Save.
    * Click Actions and "Enable CORS" and click on Enable CORS and YES. This will allow the API to interact with the S3 posts.
    * Click on "GET", then Method Request, then " URL Query string parameters"
       * Add query string - postId and click tick.
    * Click on "GET", then Integration Request, then " Mapping Templates". 
       * Select- "When there are no templates defined (recommended)"
       * Add mapping - application/json, click tick.
       * get mappings.json and paste it on the box.
    * Click on "/", then Actions- Deploy API, click on deployment stage- New stage,  name, description - "prod". Click Deploy.
    * Copy the Invoke URL and paste it in "var END_Point" in script.js.



11. Go to S3
   * Click on "polly.website".
      * Permissions- Bucket Policy
      * Copy paste the existing bucket policy code- "bucketpolicypermissions.json" and paste it.
      * Change the Resource to "arn:aws:s3:::polly.website/*", click Save.
      * Click the bucket then add- "index.html", "scripts.js", "style.css". Click on the link in index.html to get the page.

12. Type in the text, then in the Search text box type "*". Click on Search, then play button.

13. Go to DynamoDB-> tables->posts-> items-> actions-> Delete.

14. Go to the page then refresh- Type in the text, click in Speak your voice. Copy the Post ID and paste the box for "Search". Click on the Play button and Enjoy!
