[[AWS]]

# boto3
##### build automated reports
Boto3 lets us harness the power of AWS as an extension of our laptops!
- perform sentiment analysis
- send alerts
- etc...

- built on top of `botocore` library
	- shared by the AWS CLI
	- `botocore` provides the low-level:
			- clients,
			- session
			- configuration data
- **`boto3` builds on top of botocore by providing it's own:
  ### session
  - manages state about a particular configuration
  - by default, created for you whenever needed
  - *however*, recommended to maintain your own sessions in some scenarios
  - store things like credentials, AWS region, and other configurations
  - can also configure **non-credential values**:

- upon connecting, `boto3` looks for configuration instructions in the following order:
	1. explicitly passed configuration parameters when adding a client
	2. environment variables
	3. `.aws/config` file
### resources
- object-oriented interface into AWS services
	- higher level of abstraction than raw, low-level calls made by service clients
```python
sqs = boto3. resource('sqs')
s3 = boto3.resources('s3')
```
### clients
- map one-to-one with cli service APIs - all service operations are supported by `client`s
```python
import boto3
s3 = boto3.client('s3', region_name = 'us-east-1', aws_access_key_id=AWS_KEY_ID, aws_secret_access_key=AWS_SECRET)
response = s3.list_buckets()
---------------------------------
{'ResponseMetadata':
  {'RequestId': 'FUMSLESZA70Q9OT7Q3S2BQ6F2KBMKDY56NRU16TJ5YSCND13GOW5', 'HTTPStatusCode': 200, 'HTTPHeaders': {'x-amzn-requestid': 'FUMSLESZA70Q9OT7Q3S2BQ6F2KBMKDY56NRU16TJ5YSCND13GOW5'}, 'RetryAttempts': 0}, 'Buckets': [{'Name': 'datacamp-hello', 'CreationDate': datetime.datetime(2022, 3, 14, 21, 28, 28, tzinfo=tzutc())}, {'Name': 'datacamp-uploads', 'CreationDate': datetime.datetime(2022, 3, 14, 21, 28, 28, tzinfo=tzutc())}, {'Name': 'datacamp-transfer', 'CreationDate': datetime.datetime(2022, 3, 14, 21, 28, 28, tzinfo=tzutc())}], 'Owner': {'DisplayName': 'webfile', 'ID': 'bcaf1ffd86f41161ca5fb16fd081034f'}}
```
- key & secret are your username & password
- anything you can do in the console, you can do with boto3
- use IAM to create key/secret
other available client methods:
- `abort_multipart_upload()`
- `can_paginate()`
- `complete_multiport_upload()`
- `copy()`

##  S3
- Lets us put any file in the cloud & make it accessible anywhere in the world through a URL
### Buckets (directories)
- have their own permission polity
- website storage
- generate logs about their own activity & write them to other buckets
- contain many objects
##### Create a bucket
`bucket = s3.create_bucket(Bucket='git-requests')`
- **bucket names must be unique across ALL AWS**
`dict_of_bucket_names = s3.list_buckets()['Buckets']`
##### Delete a bucket
`response = s3.delete_bucket('gid-requests')`
- combine loops & client calls to perform operations on many buckets at once 
  - **Be careful doing this - especially deleting!**

### Objects (files)
- can be anything - image, video file, csv, or log file
- plenty of operations
#### Uploading & retrieving files
- bucket's name is a string
- Objects have **keys**, which are the full path of the object
- Key must be unique across the bucket
- can belong to a one bucket only
```python
s3.upload_file(
  Filename='example_file.csv'
  ,Bucket='example_bucket'
  ,Key='gid_requests_2019_01_01.csv'
)

response = s3.list_objects(
  Bucket='gid-requests'
  ,MaxKeys=2 # s3 will limit to 1000 if this param is omitted
  ,Prefix='gid_requests_2019_' # essentially a filter on object key
)

what_you_probably_want = response['Content']
```
![[Pasted image 20220315100819.png]]

#### Get object metadata
```python
response = s3.head_object(
  Bucket='gid-requests'
  ,Key='gid_requests_2018_12_30.csv'
)
```

#### Download files
```python
s3.download_file(
  Filename='gid_requests_downed.csv'
  ,Bucket='gid-requests'
  ,Key='gid_requests_2018_12_30.csv'
)
```

#### Delete object
```python
s3.delete_object(
  Bucket='gid-requests'
  ,Key='gid_requests_2019_01_01.csv'
)
```

# Share files securely
- AWS defaults to denying permission
- when we upload a file, by default only our key & secret can access the file

**4 ways to control S3 permissions:**
#### IAM
#### Bucket policies: control over specific buckets & objects within it
### Access Control Lists
#### public read
`s3.put_object_acl(Bucket='gid-requests', Key='potholes.csv', ACL='public-read')`
- now, anyone in the world can download this file

alternatively,
```python
s3.upload_file(
  Bucket='gid-requests'
  , Filename='potholes.csv'
  , Key='potholes.csv'
  , ExtraArgs={'ACL':'public-read'})
```

##### S3 object URL template
`https://{bucket}.{key}`
p
- use f-strings!
- load directly into pandas df using url!
![[Pasted image 20220318170323.png]]

#### private read
- presigned URLs: share temporary links with 'guests'
`obj = s3.get_object(Bucket='gid-requests', Key='2019/potholes.csv)`
- returns metadata about the file
- `obj['Body']` = `StreamingBody`
  - **StreamingBody** doesn't download the whole object immediately
  - pandas knows how to handle this response!

### Presigned URLs
- expire after a certain timeframe
- great for temporary acces
```python
share_url = s3.generate_presigned_url(
  ClientMethod='get_object'
  ,ExpiresIn=3600
  ,Params={'Bucket': 'gid-requests', 'Key':'potholes.csv}
)
```
- can open in pandas

### Lead multiple files into one pd DF
```python
df_list = []
response = s3.list_objects(
  Bucket='gid-requests'
  ,Prefix='2019/1
)

request_files = response['Contents']

for file in request_files:
  obj = s3.get_object(Bucket='gid-requests', Key=file['Key'])
  obj_df = pd.read_csv(obj['Body'])
  df_list.append(obj_df)
  
final_df = pd.concat(df_list)
```


## Share files through a website
- S3 can serve as HTML pages
  - useful when we want to share results of an analysis with management & update results via pipeline
```python
df.to_html('table_agg.html'
  , render_links=True # makes links in the df clickable
  ,columns['service_name', 'request_count', 'info_link']
  , borders=0 # or 1 to show a border
)
```
### upload HTML file to S3
```python
s3.upload_file(
  Filename='./table_aggregation.html'
  , Bucket='datacamp-website'
  , Key='table.html'
  , ExtraArgs = {
    'ContentType': 'text/html'
    , 'ACL': 'public-read'
  }
)
```
- access via `https://my_website.table.html`
- HTML files are treated just like regular files
  - we can make this page private & provide a pre-signed URL for temporary access
#### Upload other types of content (e.g. images)
```python
s3.upload_file(
  Filename='./plot_image.png'
  , Bucket='datacamp-website'
  , Key='plot_image.png'
  , ExtraArgs = {
    'ContentType': 'image/png'
    ,'ACL': 'public-read'}
)
```
### other media types
- JSON: `application/json`
- PNG: `image/png`
- PDF: `application/pdf`
- CSV: `text/csv`
other formats available via IANA 

### Generating index page
```python
r = s3.list_objects(Bucket='gid-reports', Prefix='2019/')

objects_df = pd.DataFrame(r['Contents'])

base_url = 'http://example_website.'
objects_df['Link'] = base_url + objects_df['Key']
objects_df.to_html('report_listing.html'
                    , columns=['Link', 'LastModified', 'Size']
                    , render_links=True
)
# then upload the same way you would any file 
```
Your data is now available via that public URL!

# Reports & notifications
## SNS Topics (simple notification service)
- send texts, push notifications & email alerts
![[Pasted image 20220319171248.png]]
- publishers post **messages** to topics
- subscribers receive them
![[Pasted image 20220319171443.png]]
- every topic has an ARN: Amazon Resource Name
  - unique id for this topic
  - each subscription has a unique ID
```python
sns = boto3.client('sns'
                    region_name='us-east-1'
                    ,aws_access_key_id=AWS_KEY_ID
                    ,aws_secret_access_key=AWS_SECRET)

response = sns.create_topic(Name='example_alert') # ['TopicArn] (shortcut :))

topic_arn = response['TopicArn']
```
- creating topics is idempotent - `create_topic` with a name that already exists will just return the pre-existing ARN
### list topics
- `list_of_topics = sns.list_topics()['Topics']`
### delete a topic:
`sns.delete_topic(TopicArn='arn:aws:sns:us-east-1-0203940293034:example_alert'`\

Create 2 topics per city department: general & critical
```python
# Create list of departments
departments = ['trash', 'streets', 'water']

for dept in departments:
  	# For every department, create a general topic
    sns.create_topic(Name="{}_general".format(dept))
    
    # For every department, create a critical topic
    sns.create_topic(Name="{}_critical".format(dept))

# Print all the topics in SNS
response = sns.list_topics()
print(response['Topics'])
----------------------------
[{'TopicArn': 'arn:aws:sns:us-east-1:123456789012:trash_general'}, {'TopicArn': 'arn:aws:sns:us-east-1:123456789012:trash_critical'}, {'TopicArn': 'arn:aws:sns:us-east-1:123456789012:streets_general'}, {'TopicArn': 'arn:aws:sns:us-east-1:123456789012:streets_critical'}, {'TopicArn': 'arn:aws:sns:us-east-1:123456789012:water_general'}, {'TopicArn': 'arn:aws:sns:us-east-1:123456789012:water_critical'}]
```

too many notifications are going out now! delete the general topic for all departments
```python
# Get the current list of topics
topics = sns.list_topics()['Topics']

for topic in topics:
  # For each topic, if it is not marked critical, delete it
  if "critical" not in topic['TopicArn']:
    sns.delete_topic(TopicArn=topic['TopicArn'])
    
# Print the list of remaining critical topics
print(sns.list_topics()['Topics'])
```

## SNS Subscriptions
- choose who gets the notifications & how they get them
- every subscription has a:
    - unique id
    - protocol: email, text, other
    - endpoint: specific phone # or email address where the message should be sent
    - status: confirmed or pending confirmation
      - email subscribers have to click the unique link to authorize the subscription
```python
response = sns.subscribe(
  TopicArn = 'arn:aws:sns:us-east-1:320333787931:example_alerts'
  ,Protocol = 'SMS'
  ,Endpoint = '+14125982940')
```
![[Pasted image 20220319180455.png]]

### Listing subscriptions by Topic
`subs = sns.list_subscriptions_by_topic(TopicArn='arn:aws:sns:us-east-1:2340293502:example_alerts')['Subscriptions']`

### Deleting subscriptions
`sns.unsubscribe(SubscriptionArn='arn:aws:sns:us-east-1:3205g234523456:example_alerts:104u0h-u093hu-0hu3h')`
##### delete all text subscriptions:
```python
for sub in subs:
  if sub['Protocol'] == 'sms':
    sns.unsubscribe(sub['SubscriptionArn'])
```

### Sending messages
```python
response = sns.publish(
  TopicArn = 'arn:aws:sns:us-east-1:123049508234:example_alerts'
  ,Message = 'Body of SMS text or email'
  ,Subject = 'Subject Line for Email'
)
```

##### Sending custom messages
```python
num_of_reports = 137

response = client.publish(
  TopicArn = 'arn:aws:sns:us-east-1:123094808:examle_alerts'
  , Message = f'There are {num_of_reports} reports outstanding'
  , Subject = 'Number of outstanding reports'
```

#### Send a single SMS
 ```python
 response = sns.publish(
  PhoneNumber = '+12345288'
  , Message = 'This is the text that will be sent'
)
```
- one-off texts are ok for getting stuff out quickly, but not good long-term practice
- topics and subscribers = maintainable long-term

| publish to a topic                         | send a single SMS                             |
| ------------------------------------------ | --------------------------------------------- |
| have to have a topic                       | don't need a topic                            |
| topic has to have up-to-date subscriptions | don't need subscriptions                      |
| better for multipile recievers             | just sends a message to a single phone number |
| easier list mgmt                           | email options not available                                              |
