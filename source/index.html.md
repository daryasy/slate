---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - http

toc_footers:
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true

code_clipboard: true
---

# Introduction

With the MessageWhiz API, you can create message campaigns, send your own single SMS or the entire message broadcasts, 
as well as manage recipients, senders, unsubscribers or any other possible entities.

This document will guide you through the core endpoints and functionality of the Message Whiz API, 
so you can quickly and efficiently start your messaging.

## Getting started

In this section, we describe all the necessary steps to send your first broadcast via the MW API from scratch.

But first thing first. MessageWhiz uses API keys to allow access to the API. You can get an API key here. 
(You can find your API key in your [MW account](https://sms.messagewhiz.com/app/account))

MessageWhiz expects for the API key to be included in all API requests to the server in a header that looks like the following:
`
POST /api/3/ HTTP/1.1
Host: <url>
apikey: <apikey>
Content-Type: application/json
Authorization: Basic <credentials>`

MessageWhiz uses basic access authentication, that is why you also need to provide a username and password of your 
account to make some(or all?) requests.

<aside class="notice"> You must replace <code> apikey </code> with your personal API key. </aside>

### Sending First Broadcast

In order to send the basic message broadcast, you need to perform the following steps first:
[Create a Campaign](##create-a-campaign) - a set of SMS messages grouped under one campaign name.
[Create Recipient List] - phone numbers to which the messages will be sent.
[Create Sender] -  a number, name, or ID that recipients see on their devices when they receive a message from you.
Only after creating these entities, you can [create and send a broadcast]. 
Or you might use already existing entities for broadcasting as well.

### Generic replaceable

The following variables should be replaced with your actual information in requests:

Key | Description 
-------------- | -------------- 
`<apikey>` | Personal API key 
`<url>` | URL from which API is accessed - http://sms.mmdsmart.com/
`<credentials>` | the Base64 encoding of your username and password joined by a single colon ‘:’


# Campaigns

Campaign represents a concept that unifies a set of SMS messages grouped under one campaign name. 
The campaign exists in a specified time period, therefore start and end dates should be necessarily indicated during 
campaign creation.

## Create a Campaign 

> Example Request

```http
POST /api/3/campaign HTTP/1.1
Host: sms.mmdsmart.com
apikey: <apikey>
Content-Type: application/json
Authorization: Basic <credentials>
{
    "name":"CampaignName",
	"start_date":"2020-07-22T21:00:00.000Z",
	"end_date":"2020-09-25T21:00:00.000Z"
}
```

> Example Response (200 OK)

```json
{
   "errorCode": 0,
   "errorMessage": "",
   "errorType": "",
   "executionTime": 0.044941952,
   "result": {
       "id": 46279,
       "is_default": 0,
       "name": "CampaignName",
       "start_date": "2020-07-22T21:00:00.000Z",
       "end_date": "2020-09-25T21:00:00.000Z",
       "enabled": true
   }
}
```

Create a Campaign from your MessageWhiz account:
`POST http://sms.mmdsmart.com/api/3/campaign`

### Request Body

Campaign to be created: 
`name(string)` - campaign name,
`start_date(string)` - campaign start date,
`end_date(string)` - campaign end date.
Date and time are expressed according to ISO 8601(?). Note, the campaign end_date cannot be earlier than start_date.

### Response Messages

HTTP Status Code | Reason
-------------- | -------------- 
400 | Invalid parameters specified
401 | Not authorized - API key is missing or invalid
403 | Forbidden - user/company is either disabled or doesn't have enough permissions
409 | Duplicate entry

## Get Campaigns 

> Example Request

```http
   GET /api/3/campaign?start=0 HTTP/1.1
   Host: sms.mmdsmart.com
   apikey: <apikey>
   Content-Type: application/json
   Authorization: Basic <credentials>
```

> Example Response (200 OK)

```json
{
   "errorCode": 0,
   "errorMessage": "",
   "errorType": "",
   "executionTime": 0.012664016,
   "result": [
       {
           "enabled": true,
           "id": 46279,
           "is_default": 0,
           "name": "CampaignName",
           "start_date": "2020-07-22T21:00:00.000Z",
           "end_date": "2020-09-25T21:00:00.000Z",
           "userList": [
               {
                   "campaign_id": 46279,
                   "user_id": 3191,
                   "company_id": 3182
               }
           ]
       },
       {
           "enabled": true,
           "id": 34641,
           "is_default": 1,
           "name": "Default campaign",
           "start_date": null,
           "end_date": null,
           "userList": [
               {
                   "campaign_id": 34641,
                   "user_id": 3191,
                   "company_id": null
               }
           ]
       }
   ]
}
```

Get Campaigns from your MessageWhiz account:
`GET http://sms.mmdsmart.com/api/3/campaign HTTP/1.1`

### Query Parameters 
You might pass several parameters to your GET query in order to receive only the data you need.

Parameter | Default | Description
-------------- | -------------- | -------------- 
limit | 25 | the number of items to fetch
enabled | true | fetch only active campaigns
filter | all | filtering by the specific campaign name?
start | 0 | the number of items to skip

For example, to get first two inactive campaigns, the query URL will be the following:
`http://sms.mmdsmart.com/api/3/campaign?limit=2&enabled=false`

### Response Messages

HTTP Status Code | Reason
-------------- | -------------- 
401 | Not authorized - API key is missing or invalid
403 | Forbidden - user/company is either disabled or doesn't have enough permissions


## Modify a Campaign

> Example Request

```http
   PUT /api/3/campaign/46279 HTTP/1.1
   Host: sms.mmdsmart.com
   apikey:  <apikey>
   Content-Type: application/json
   ? Authorization: Basic <credentials>

   {
"end_date":"2021-03-25T21:00:00.000Z"
   }

```

> Example Response (200 OK)

```json
{
   "errorCode": 0,
   "errorMessage": "",
   "errorType": "",
   "executionTime": 0.026245764,
   "result": {
       "id": 46279,
       "is_default": 0,
       "name": "CampaignName",
       "start_date": "2020-07-22T21:00:00.000Z",
       "end_date": "2021-03-25T21:00:00.000Z",
       "enabled": true
   }
}
```

To modify a specific Campaign from your MessageWhiz account you should add its ID as parameter to the query:

`PUT http://sms.mmdsmart.com/api/3/campaign/{id}`

For instance,
`PUT http://sms.mmdsmart.com/api/3/campaign/46279`,
where `46279` is the ID of the campaign that will be retrieved and modified.

### Request Body

In the request body specify the value you want to modify, for instance:

`{
    "end_date":"2021-03-25T21:00:00.000Z"
 }`

### Response Messages

HTTP Status Code | Reason
-------------- | -------------- 
400 | Invalid parameters specified
401 | Not authorized - API key is missing or invalid
403 | Forbidden - user/company is either disabled or doesn't have enough permissions
409 | Duplicate entry

## Disable a Campaign

> Example Request

```http
   DELETE /api/3/campaign/46317 HTTP/1.1
   Host: sms.mmdsmart.com
   apikey: <apikey>
   Authorization: Basic <credentials>
```

> Example Response (200 OK)

```json
{
   "errorCode": 0,
   "errorMessage": "",
   "errorType": "",
   "executionTime": 0.034133648,
   "result": true
}
```

To remove a specific Campaign from your MessageWhiz account you should add its ID as parameter to the query:

`DELETE http://sms.mmdsmart.com/api/3/campaign/{id}`

### Response Messages

HTTP Status Code | Reason
-------------- | -------------- 
400 | Invalid parameters specified
401 | Not authorized - API key is missing or invalid
403 | Forbidden - user/company is either disabled or doesn't have enough permissions
409 | Duplicate entry


# Broadcasts

# Senders
# Recipients 
# Unsubscribers 
# Triggers
# Templates
# Spinners
# Tokens
# Links

# Tutorials
## How to’s
