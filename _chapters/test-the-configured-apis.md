---
layout: post
title: Test the configured APIs
date: 2017-05-30 00:00:00
description:
comments_id:
---

Now we have two sets of APIs (prod and dev), let's quickly test them to make sure they are working fine before we plug our frontend into them. Back in the [Test the APIs]({% link _chapters/test-the-apis.md %}) chapter, we used a simple utility called [AWS API Gateway Test CLI](https://github.com/AnomalyInnovations/aws-api-gateway-cli-test). If you don't have it installed take a quick look back at [chapter]({% link _chapters/test-the-apis.md %}).

Befre we do the test let's create a test user for both the environments. We'll be following the exact same steps as the [Create a Cognito test user]({% link _chapters/create-a-cognito-test-user.md %}) chapter.

### Create Test Users

We are going to use the AWS CLI for this.

<img class="code-marker" src="/assets/s.png" />In your terminal, run.

``` bash
$ aws cognito-idp sign-up \
  --region YOUR_COGNITO_REGION \
  --client-id YOUR_COGNITO_APP_CLIENT_ID \
  --username admin@example.com \
  --password Passw0rd!
```

Refer back to the [Deploying through Seed]({% link _chapters/deploying-through-seed.md %}) chapter to look up the **dev** version of your Cognito App Client Id.

<img class="code-marker" src="/assets/s.png" />Next we'll confirm the user through the Cognito Admin CLI.

``` bash
$ aws cognito-idp admin-confirm-sign-up \
  --region YOUR_COGNITO_REGION \
  --user-pool-id YOUR_COGNITO_USER_POOL_ID \
  --username admin@example.com
```

Again, replace `YOUR_COGNITO_USER_POOL_ID` with the **dev** version of your Cognito User Pool Id from the [Deploying through Seed]({% link _chapters/deploying-through-seed.md %}) chapter.

Let's quickly do the same with **prod** versions as well. Now we are ready to test our APIs.

### Testing Dev

First let's test our dev endpoint. Run the following command:

``` bash
$ apig-test \
--username='admin@example.com' \
--password='Passw0rd!' \
--user-pool-id='YOUR_COGNITO_USER_POOL_ID' \
--app-client-id='YOUR_COGNITO_APP_CLIENT_ID' \
--cognito-region='YOUR_COGNITO_REGION' \
--identity-pool-id='YOUR_IDENTITY_POOL_ID' \
--invoke-url='YOUR_API_GATEWAY_URL' \
--api-gateway-region='YOUR_API_GATEWAY_REGION' \
--path-template='/notes' \
--method='POST' \
--body='{"content":"hello world","attachment":"hello.jpg"}'
```

Make sure to refer back to the [Deploying through Seed]({% link _chapters/deploying-through-seed.md %}) for these:

- `YOUR_COGNITO_USER_POOL_ID`, `YOUR_COGNITO_APP_CLIENT_ID`, and `YOUR_COGNITO_REGION` are all related to your Cognito User Pool.
- `YOUR_IDENTITY_POOL_ID` is for your Cognito Identity Pool.
- `YOUR_API_GATEWAY_REGION` is the region your API Gateway is deployed to.
- And `YOUR_API_GATEWAY_URL` is your API Gateway endpoint. It looks something likes this `https://ly55wbovq4.execute-api.us-east-1.amazonaws.com/dev`. But if you have confgiured it with a custom domain use the one from that [chapter]({% link _chapters/custom-domains-for-api-gateway.md %}).

If the command is successful, it's look something like this.

``` bash
Authenticating with User Pool
Getting temporary credentials
Making API request
{ status: 200,
  statusText: 'OK',
  data: 
   { userId: 'us-east-1:9bdc031d-ee9e-4ffa-9a2d-123456789',
     noteId: '8f7da030-650b-11e7-a661-123456789',
     content: 'hello world',
     attachment: 'hello.jpg',
     createdAt: 1499648598452 } }
```

### Testing Prod

Testing prod should be pretty much the same. Run the following command.

``` bash
$ apig-test \
--username='admin@example.com' \
--password='Passw0rd!' \
--user-pool-id='YOUR_COGNITO_USER_POOL_ID' \
--app-client-id='YOUR_COGNITO_APP_CLIENT_ID' \
--cognito-region='YOUR_COGNITO_REGION' \
--identity-pool-id='YOUR_IDENTITY_POOL_ID' \
--invoke-url='YOUR_API_GATEWAY_URL' \
--api-gateway-region='YOUR_API_GATEWAY_REGION' \
--path-template='/notes' \
--method='POST' \
--body='{"content":"hello world","attachment":"hello.jpg"}'
```

Just use the set of values that you have for prod.

Now that our APIs our tested we are ready to plug these into our frontend. But before we do that, let's do a quick test to see what will happen if we make a mistake and push some faulty code to production.