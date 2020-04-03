---
title: Stintmint API Docs

language_tabs:
  - python: Python
  - java: Java
  - shell: cURL

toc_footers:
  - <a href='https://stintmint.com/enterprise/signup'>Sign Up for a Developer Key</a>
  - <a href='http://support.stintmint.com'>Chat with us</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the Stintmint API! You can use our API to access Stintflow API endpoints, which can send data and receive results from all StintFlows.

We have language bindings in Shell, Python, and Java! You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

# POST data to StintFlow

## API Endpoint

`POST https://stintmint.com/api/v1/post`

## Authentication

Stintmint uses Basic Auth to allow access to the API. You need to signup on Stintmint Console to get an authentication key on our [signup page](https://stintmint.com/enterprise/signup).

Requests to your StintFlow will need the Stintmint authentication key to be included in all API calls in a header that looks like the following:

**Username**

*The email address of the user*

**Password**

*The developer key of the user.* This will be available in your account section on the Console.

## POST Parameters

> POST requests to StintFlow

```python
import requests
from requests.auth import HTTPBasicAuth
url = 'https://stintmint.com/api/v1/post'
username = 'enterprise@stintmint.com'		#paste your username (email) here
key = ''									#paste your API key here
def post_(url,params1):
	r = requests.post(url,auth=(username, key),json=params1)
	return r

params = {
	"identifier":"test_value",
	"sfId":"test_value",
	"batchId":"test_value",
	"urgency":"test_value",
	"descriptors":[							#Add upto 10 descriptors
		{
		"name" : "test_value",				#Descriptor (input) names defined for the Stintflow in use on the Console
		"type" : "TEXT",
		"info" : "test_value",
		"caption" : "test_value"
		}
	]
}

r = post_(url,params)
print r
```

```shell
# With shell, you can just pass the correct header with each request
curl -X POST https://stintmint.com/api/v1/post \
 -u username:{API_KEY} \
 -d identifier="test_value" \
 -d sfId="test_value" \
 -d batchId="test_value" \
 -d urgency="test_value" \
 -d descriptors="test_array"
```

```java
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.DefaultHttpClient;
import org.apache.http.message.BasicHeader;
import org.apache.http.protocol.HTTP;
import org.json.JSONArray;
import org.json.JSONObject;

import java.io.BufferedReader;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.nio.charset.StandardCharsets;
import java.util.Base64;

public class Sample {
    public static String post( String url, JSONObject params,String username,String password ) {
        String encoded = Base64.getEncoder().encodeToString((username+":"+password).getBytes(StandardCharsets.UTF_8));  
        String responseString = null;
        try {
            HttpClient c = new DefaultHttpClient();
            HttpPost post = new HttpPost( url );
            StringEntity se = new StringEntity(params.toString());
            se.setContentType(new BasicHeader(HTTP.CONTENT_TYPE, "application/json"));
            post.setEntity(se);
            post.setHeader("Authorization","Basic "+encoded);

            HttpResponse response = c.execute( post );
            System.out.println(response.getStatusLine().getStatusCode());
            HttpEntity entity = response.getEntity();
            if (entity != null) {
                InputStream instream = entity.getContent();
                BufferedReader br = new BufferedReader( new InputStreamReader( instream ) );
                StringBuilder sb = new StringBuilder();
                String line;
                while ( ( line = br.readLine() ) != null ) {
                    sb.append( line );
                }
                responseString = sb.toString();
                instream.close();
            }
            c.getConnectionManager().shutdown();
        } catch ( Exception e ) {
            e.printStackTrace();
        }
        return responseString;
    }

    public static void main(String args[]) {
        String url = "https://stintmint.com/api/v1/post";
        String username = "enterprise@stintmint.com";				#paste your username (email) here
        String API_KEY = "";										#paste your API key here
        JSONObject jsonObject = new JSONObject(){{
            put("identifier","test_value");
            put("sfId","test_value");
            put("batchId","test_value");
			put("urgency","test_value");
            put("descriptors",new JSONArray(){{
                put(new JSONObject(){{								#Add upto 10 descriptors
                    put("name","test_value");						#Descriptor (input) names defined for the Stintflow in use on the Console
					put("type","TEXT");
                    put("info","test_value");
                    put("caption","test_value");
                }});
            }});
        }};

        String response = Sample.post(url,jsonObject,username,API_KEY);
        System.out.println(response);
    }
}
```


### Request

Parameter | Default | Description
--------- | ------- | -----------
identifier |   | Your unique identifier for this product. We will post the responses on the callback url provided by you using this identifier.
sfId |   | Service type. Provide your StintFlow ID here. Mandatory.
batchId |   | Provide an alphanumeric batch ID to track and maintain separate datasets
descriptors |   | An array of JSON objects containing data corresponding to a single request
stateOptions |   | An array of JSON objects each corresponding to a module allowing you to control module specific options/classes
urgency |   | Processing criteria for Request. Eg. U1 (Refer the [Support Base](http://support.stintmint.com) for details)
callbackUrl |   | Processed responses will be posted to this URL

### descriptors
Structured as an array of JSON objects to support multiple data values for each identifier while supporting different data types

Here are the parameters of a single 'descriptors' element:

Parameter | Default | Description
--------- | ------- | -----------
name |   | Descriptor (input) names defined specific to the Stintflow in use. Names are available for reference on the Console in the Use Stintflow section in the Overview tab.
type |   | Can be any one of - TEXT, IMAGE, PDF, HREF, AUDIO, VIDEO
info |   | Value of the descriptor; Can be a URL in case {type:{$nin:[TEXT]}}
caption |   | The display value of the descriptor in case it needs to be different from the actual descriptor

All mandatory descriptor fields for a StintFlow (field requirement mentioned on the Console) must be included in the request.

For each Descriptor (input) field, the 'name' 'type' & 'info' keys need to be provided for a Requestto be valid.

<aside class="notice">
A maximum of 10 descriptors are allowed per request. Log a ticket with us in case that needs to be changed.
</aside>

### stateOptions
Structured as an array of JSON objects to support multiple data values for each identifier while supporting different data types

Here are the parameters of a single 'stateOptions' element:

Parameter | Default | Description
--------- | ------- | -----------
stateId |   | Alphanumeric key the specific module of your StintFlow. This will be visible to you on your StintFlow upload data page
options |   | An array of JSON objects each referring to a single option

*'options'*

Each element of the stateOptions array has the following structure:

Parameter | Default | Description
--------- | ------- | -----------
optionId |   | Unique alphanumeric key for a single option/class
type |   | Can be any one of - TEXT, IMAGE
info |   | The name of the option/class
caption |   | The display name for this option

<aside class="notice">
optionId is mandatory and can not be changed once set. It will take the value of info if left blank the first time. You will receive responses corresponding to 'optionId'.
</aside>

<aside class="notice">
caption is optional
</aside>

[Details here](#stintflow-responses)

# StintFlow responses

## Fixed Endpoint
You can update your 'callbackUrl' in the Accounts section on the Console.

## Configure Endpoint

> Response status code 200

```json
{
	"identifier" : "",	# Unique alphanumeric string
	"status" : "",		# COMPLETED
	"created" : "",		# UNIX time in milliseconds
	"completed" : "",	# UNIX time in milliseconds
	"sfId" : "",		# Unique alphanumeric string corresponding to your StintFlow
	"batchId" : "",		# Unique alphanumeric string corresponding to your Batch
	"cost" : "",		# Credits consumed to complete request
	"responses" : []	# An array of JSON objects 
}
```

Alternatively, you can send a specific callbackUrl in the POST request as mentioned in the [section above](#post_parameters). In the absence of 'callbackUrl' in the POST request, we will default to the URL mentioned in your account.

<aside class="warning">
Our POST request will make at the most 20 failed attempts to send the query response.
By default, response code '200' will be treated as a successful attempt.
</aside>

# FETCH responses

## StintFlow responses

### API Endpoint
`FETCH https://stintmint.com/api/v1/fetch`

### Authentication

Stintmint uses Basic Auth to allow access to the API. You need to signup on Stintmint Console to get an authentication key on our [signup page](https://stintmint.com/enterprise/signup).

Requests to your StintFlow will need the Stintmint authentication key to be included in all API calls in a header that looks like the following:

**Username**

*The email address of the user*

**Password**

*The developer key of the user.* This will be available in your account section on the Console.

### Query Parameters

> FETCH StintFlow responses:

```python
import requests
from requests.auth import HTTPBasicAuth
url = 'https://stintmint.com/api/v1/fetch'
username = 'enterprise@stintmint.com'		#paste your username (email) here
key = ''									#paste your API key here
def post_(url,params1):
	r = requests.post(url,auth=(username, key),json=params1)
	return r

params = {
	"identifier" : "test_value",
	"sfId" : "test_value",
}

r = post_(url,params)
print r
```

```shell
# With shell, you can just pass the correct header with each request
curl -X GET https://stintmint.com/api/v1/fetch \
 -u username:{API_KEY} \
 -d identifier="test_value" \
 -d sfId="test_value" \
```

```java
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.DefaultHttpClient;
import org.apache.http.message.BasicHeader;
import org.apache.http.protocol.HTTP;
import org.json.JSONArray;
import org.json.JSONObject;

import java.io.BufferedReader;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.nio.charset.StandardCharsets;
import java.util.Base64;

public class Sample {
    public static String post( String url, JSONObject params,String username,String password ) {
        String encoded = Base64.getEncoder().encodeToString((username+":"+password).getBytes(StandardCharsets.UTF_8));  
        String responseString = null;
        try {
            HttpClient c = new DefaultHttpClient();
            HttpPost post = new HttpPost( url );
            StringEntity se = new StringEntity(params.toString());
            se.setContentType(new BasicHeader(HTTP.CONTENT_TYPE, "application/json"));
            post.setEntity(se);
            post.setHeader("Authorization","Basic "+encoded);

            HttpResponse response = c.execute( post );
            System.out.println(response.getStatusLine().getStatusCode());
            HttpEntity entity = response.getEntity();
            if (entity != null) {
                InputStream instream = entity.getContent();
                BufferedReader br = new BufferedReader( new InputStreamReader( instream ) );
                StringBuilder sb = new StringBuilder();
                String line;
                while ( ( line = br.readLine() ) != null ) {
                    sb.append( line );
                }
                responseString = sb.toString();
                instream.close();
            }
            c.getConnectionManager().shutdown();
        } catch ( Exception e ) {
            e.printStackTrace();
        }
        return responseString;
    }

    public static void main(String args[]) {
        String url = "https://stintmint.com/api/v1/fetch";
        String username = "enterprise@stintmint.com";				#paste your username (email) here
        String API_KEY = "";										#paste your API key here
        JSONObject jsonObject = new JSONObject(){{
            put("identifier","test_value");
            put("sfId","test_value");
        }};

        String response = Sample.post(url,jsonObject,username,API_KEY);
        System.out.println(response);
    }
}
```

> Response status code 200

```json
{
	"identifier" : "",	# Unique alphanumeric string
	"status" : "",		# COMPLETED
	"created" : "",		# UNIX time in milliseconds
	"completed" : "",	# UNIX time in milliseconds
	"sfId" : "",		# Unique alphanumeric string corresponding to your StintFlow
	"cost" : "",		# Credits consumed to complete request
	"responses" : []	# An array of JSON objects 
}
```

Parameter | Default | Description
--------- | ------- | -----------
identifier |   | Your unique identifier for this product. We will post the responses on the callback url provided by you using this identifier.
sfId |   | Unique alphanumeric string corresponding to your StintFlow

## Module responses

### API Endpoint
`FETCH https://stintmint.com/api/v1/fetch`

### Authentication

Stintmint uses Basic Auth to allow access to the API. You need to signup on Stintmint Console to get an authentication key on our [signup page](https://stintmint.com/enterprise/signup).

Requests to your StintFlow will need the Stintmint authentication key to be included in all API calls in a header that looks like the following:

**Username**

*The email address of the user*

**Password**

*The developer key of the user.* This will be available in your account section on the Console.

### Query Parameters

> FETCH StintFlow Module responses:

```python
import requests
from requests.auth import HTTPBasicAuth
url = 'https://stintmint.com/api/v1/fetch'
username = 'enterprise@stintmint.com'		#paste your username (email) here
key = ''									#paste your API key here
def post_(url,params1):
	r = requests.post(url,auth=(username, key),json=params1)
	return r

params = {
	"identifier" : "test_value",
	"sfId" : "test_value",
	"stateId" : "test_value",
}

r = post_(url,params)
print r
```

```shell
# With shell, you can just pass the correct header with each request
curl -X GET https://stintmint.com/api/v1/fetch \
 -u username:{API_KEY} \
 -d identifier="test_value" \
 -d sfId="test_value" \
 -d stateId="test_value" \
```

```java
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.DefaultHttpClient;
import org.apache.http.message.BasicHeader;
import org.apache.http.protocol.HTTP;
import org.json.JSONArray;
import org.json.JSONObject;

import java.io.BufferedReader;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.nio.charset.StandardCharsets;
import java.util.Base64;

public class Sample {
    public static String post( String url, JSONObject params,String username,String password ) {
        String encoded = Base64.getEncoder().encodeToString((username+":"+password).getBytes(StandardCharsets.UTF_8));  
        String responseString = null;
        try {
            HttpClient c = new DefaultHttpClient();
            HttpPost post = new HttpPost( url );
            StringEntity se = new StringEntity(params.toString());
            se.setContentType(new BasicHeader(HTTP.CONTENT_TYPE, "application/json"));
            post.setEntity(se);
            post.setHeader("Authorization","Basic "+encoded);

            HttpResponse response = c.execute( post );
            System.out.println(response.getStatusLine().getStatusCode());
            HttpEntity entity = response.getEntity();
            if (entity != null) {
                InputStream instream = entity.getContent();
                BufferedReader br = new BufferedReader( new InputStreamReader( instream ) );
                StringBuilder sb = new StringBuilder();
                String line;
                while ( ( line = br.readLine() ) != null ) {
                    sb.append( line );
                }
                responseString = sb.toString();
                instream.close();
            }
            c.getConnectionManager().shutdown();
        } catch ( Exception e ) {
            e.printStackTrace();
        }
        return responseString;
    }

    public static void main(String args[]) {
        String url = "https://stintmint.com/api/v1/fetch";
        String username = "enterprise@stintmint.com";				#paste your username (email) here
        String API_KEY = "";										#paste your API key here
        JSONObject jsonObject = new JSONObject(){{
            put("identifier","test_value");
            put("sfId","test_value");
            put("stateId","test_value");
        }};

        String response = Sample.post(url,jsonObject,username,API_KEY);
        System.out.println(response);
    }
}
```

> Response status code 200

```json
{
	"identifier" : "",	# Unique alphanumeric string
	"status" : "",		# COMPLETED
	"stateId" : "",	# Unique alphanumeric string corresponding to your StintFlow module
	"cost" : "",		# Credits consumed to complete request
	"responses" : []	# An array of JSON objects 
}
```

Parameter | Default | Description
--------- | ------- | -----------
identifier |   | Your unique identifier for this product. We will post the responses on the callback url provided by you using this identifier.
sfId |   | Unique alphanumeric string corresponding to your StintFlow
stateId |   | Alphanumeric key the specific module of your StintFlow. This will be visible to you on your StintFlow upload data page