---
title: IAM Policies
---

Jets provides several ways to finely control the IAM policies associated with your Lambda functions. Here are the ways and their precedence:

1. Function specific IAM policy: highest precedence
2. Class-wide IAM policy
3. Application-wide IAM policy: lowest precedence

## Function specific IAM policy

```ruby
class PostsController < ApplicationController
  # ...
  iam_policy("s3:*", "logs:*")
  def show
    render json: {action: "show", id: params[:id]}
  end
end
```

## Class-wide IAM policy

```ruby
class PostsController < ApplicationController
  class_iam_policy(
    "dynamodb:*"
    {
      "Sid" => "MyStmt1",
      "Action" => ["logs:*"],
      "Effect" => "Allow",
      "Resource" => "*",
    }
  )
end
```

## Application-wide IAM policy

```ruby
Jets.application.configure do |config|
  config.iam_policy = ["logs:*"]
end
```

## IAM Policy Definition Styles

You might have noticed that the above `iam_policy` examples used different parameter styles. Jets allows for a variety of different IAM Policy Definiition styles for your convenience. The `iam_policy` takes a single parameter or list of parameters.  Jets expands each parameter in the list to Policy Statements in an IAM Policy Document.

Summary of the different expansion styles:

1. Simple Statement: simplest
2. Statement Hash
3. Full Policy Hash: most complex

It is suggested that you start off with the simplest `iam_policy` definition style and resort to the more complex styles only when you need to. Here are examples of each style with their expansion:

### IAM Policy Simple Statement

```ruby
iam_policy "dynamodb:*"
```

Expands to:

```yaml
Version: '2012-10-17'
Statement:
- Sid: Stmt1
  Action:
  - dynamodb:*
  Effect: Allow
  Resource: "*"
```

### IAM Policy Statement Hash

```ruby
iam_policy(
  sid: "MyStmt1",
  action: ["logs:*"],
  effect: "Allow",
  resource: "*",
)
```

Expands to:

```yaml
Version: '2012-10-17'
Statement:
- Sid: Stmt1
  Action:
  - logs:*
  Effect: Allow
  Resource: "*"
```

### IAM Policy Full Policy Hash

```ruby
iam_policy(
  version: "2012-10-17",
  statement: [{
    sid: "MyStmt1",
    action: ["lambda:*"],
    effect: "Allow",
    resource: "arn:::my-arn"
  }]
)
```

Expands to:

```yaml
Version: '2012-10-17'
Statement:
- Sid: MyStmt1
  Action:
  - lambda:*
  Effect: Allow
  Resource: "arn:::my-arn"
```

## IAM Policy Definition Expansion

What's important to understand is that ultimately, the `iam_policy` definition expands to include an IAM Policy document that looks something like this:

```yaml
PolicyDocument:
  Version: '2012-10-17'
  Statement:
  - Sid: Stmt1
    Action:
    - s3:*
    Effect: Allow
    Resource: "*"
```

The expanded IAM Policy documents gets included into the CloudFormation template and get associated with the desired Lambda functions. More details on what an raw IAM Policy document looks like can be found at:

* [AWS IAM Policies and Permissions docs](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html#access_policies-json)
* [CloudFormation IAM Policy reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-iam-policy.html)

<a id="prev" class="btn btn-basic" href="{% link _docs/function-properties.md %}">Back</a>
<a id="next" class="btn btn-primary" href="{% link _docs/prewarming.md %}">Next Step</a>
<p class="keyboard-tip">Pro tip: Use the <- and -> arrow keys to move back and forward.</p>