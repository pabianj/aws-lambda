# Why I forked this
I tried several others "starter" tutorials to get this going.  Node is not (yet) one of my
strong points, but I did get this to work with a ton of searching, the AWS Lambda forum, and
StackOverflow.

I found the original README didn't have enough info for a n00b to get started.  

# cloudwatch-alarm-to-slack
[Amazon CloudWatch](https://aws.amazon.com/cloudwatch/) alarm notifications to
[Slack](https://slack.com/) streaming function for
[AWS Lambda](https://aws.amazon.com/lambda/).

## Setup

### Function configuration
Add the function code to AWS Lambda with the following configuration options:  

Key     | Value
--------|--------------
Runtime | Node.js 6.10
Handler | index.handler
Role    | AWSLambdaBasicExecutionRole
Memory  | 128 (MB)
Timeout | 3 sec
KMS key | aws/lambda

### Environment variables
Set the following required environment variable for the Lambda function:

Key     | Value
--------|--------------
webhook | [AWS KMS](https://aws.amazon.com/kms/) encrypted Slack WebHook URL.

Set the following optional environment variables for the Lambda function:

Key        | Value
-----------|--------------
channel    | Slack channel to send the notifications to.
username   | Bot username used for the slack messages.
icon_emoji | Bot icon emoji used for the slack messages.
icon_url   | Bot icon url used for the slack messages.

#### How do I encrypt an URL?
I tried many ways to figure out how to encrypt the URL.  Some of the other "SNS to Slack" examples
tell you to remove the domain name and just use the token.  Here you need to ecrypt the whole thing
like this, (the --plaintext option tries to download from the given URL so you have to trick it)

```
aws kms encrypt --key-id $KEY --plaintext fileb://<(echo 'https://atesturl.google.com')
```

### Trigger configuration
Add the desired [Amazon SNS](https://aws.amazon.com/sns/) topic as trigger for
the Lambda function.

### Other Items of Interest
I wanted to see more information in the Slack message when it comes threw.  I added some
addition items.

I found that adding `console.log` was really helpful. I didn't realize you can get a console in the
inline editor within Lambda.  It was nice for quick 'live' debugging.

Mess around with Lambda.  If this is your first foray into Lambda, it's going to take a minute
to get your sea legs.  It's fun.  You'll figure it out; don't give up.  This is a great way to get your
feet wet, have some fun working on something new.  My guess if you are already proficient with
node or golang, you aren't Googling for a way to do this. 

### Logging
I found for some reason that CloudWach Logs were not being generated and I could not create them.

I had to attach the following policy to the IAM role that was being used for this Lambda Function:
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:PutLogEvents",
                "logs:DescribeLogStreams"
            ],
            "Resource": [
                "arn:aws:logs:*:*:*"
            ]
        }
    ]
}
```
I'll include it in the repo.  You may need it, you may not.

I found this referenced a few times in the AWS Lambda forum.


### Testing
Use the `test-event.json` as your test template:
1. In your Lambda Function detail page, look for the "testing" dropdown box before the "Test"
button.
2. In that bo, select "Configure Test Event"
3. Create new test event
4. Give it a name and copy and paste the code into the editor
5. Click "Create"
6. Just make sure you what ever you named it is selected in the dropbox
7. Profit!

## License
Released under the [MIT license](https://opensource.org/licenses/MIT).

## Original Author
[Sebastian Tschan](https://blueimp.net/)

## Forked Author
[Jeff  Pabian](http://jeffpabian.com)
