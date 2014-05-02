# API principles

* [Parameters](#parameters)
* [HTTP verbs](#http-verbs)
* [Responses](#responses)
    * [Generic/Shared](#genericshared)
        * [User not signed in](#user-not-signed-in)
        * [Insufficient user rights](#insufficient-user-rights)
        * [Not found](#not-found)
        * [Bad Request](#bad-request)
        * [Internal Server Error](#internal-server-error)
    * [Get data](#get-data)
        * [Data formats](#data-formats)
            * [Date and time](#date-and-time)
            * [Images](#images)
        * [Get single object](#get-single-object)
        * [Get multiple objects](#get-multiple-objects)
            * [Pagination](#pagination)
    * [Create data](#create-data)
        * [Create single object](#create-single-object)
        * [Required parameters missing](#required-parameters-missing)
        * [Object validation failed](#object-validation-failed)
        * [Uploading images](#uploading-images)
    * [Update data](#update-data)
        * [Update single object](#update-single-object)
        * [Required parameters missing](#required-parameters-missing-1)
        * [Object validation failed](#object-validation-failed-1)
    * [Delete data](#delete-data)
        * [Delete single object](#delete-single-object)


## Parameters

Many API methods take optional parameters. For GET requests, any parameters not specified as a segment in the path can be passed as an HTTP query string parameter:

```
curl -i "https://shape.dk/api/v1/employees?title=developer"
```

For POST, PATCH, and DELETE requests, parameters not included in the URL should be encoded as JSON with a Content-Type of ‘application/json’:

```
curl -X POST -H "Content-Type: application/json" -d '{"name":"Gert Jørgensen","title":"developer"}' https://shape.dk/api/v1/employees
```


## HTTP verbs

Where possible our API's strives to use appropriate HTTP verbs for each action.

Verb | Description
--- | ---
GET | Used for retrieving resources.
POST | Used for creating resources.
PATCH | Used for updating resources with partial JSON data. For instance, an Issue resource has title and body attributes. A PATCH request may accept one or more of the attributes to update the resource.
DELETE | Used for deleting resources.


## Responses


### Generic/Shared

Generic responses can be encountered from any request to the API and the requester should therefore always be prepared to handle these responses.

#### User not signed in

Returns status: 401 Unauthorized

```JSON
{
  "status": "error",
  "data": {
    "message": "Unauthorized",
    "error_code": "unauthorized"
  }
}
```

#### Insufficient user rights

Returns status: 403 Forbidden

```JSON
{
  "status": "error",
  "data": {
    "message": "Forbidden",
    "error_code": "forbidden"
  }
}
```

#### Not found

Returns status: 404 Not Found

```JSON
{
  "status": "error",
  "data": {
    "message": "Not Found",
    "error_code": "not_found"
  }
}
```

#### Bad Request

Returns status: 400 Bad Request

```JSON
{
  "status": "error",
  "data": {
    "message": "Not specified",
    "error_code": "not_specified"
  }
}
```

#### Internal Server Error

Returns status: 500 Internal Server Error

```JSON
{
  "status": "error",
  "data": {
    "message": "Internal Server Error",
    "error_code": "exception"
  }
}
```


### Get data

#### Data formats

##### Date and time

All timestamps are returned in ISO 8601 format:

```
YYYY-MM-DDTHH:MM:SSZ
```

and time-only (without date):

```
THH:MM:SSZ
```

Timestamps are always saved and returned in UTC (GMT/Zulu)-time.

##### Images

Images are returned as an "image" entity including a "normal" and a "retina" version:

```JSON
{
  "status" : "success",
  "data" : {
    "_id" : 1,
    "name" : "Gert Jørgensen",
    "title" : "Developer",
    "image" : {
      "normal" : "https://shape.dk/images/employees/gert.jpg",
      "normal" : "https://shape.dk/images/employees/gert@2x.jpg"
    }
  },
  "meta" : {
    "url" : "https://shape.dk/api/v1/employees/1"
  }
}
```

For multiple images the response would be:

```JSON
{
  "status" : "success",
  "data" : {
    "_id" : 1,
    "name" : "Gert Jørgensen",
    "title" : "Developer",
    "images" : [
      {
        "normal" : "https://shape.dk/images/employees/gert.jpg",
        "normal" : "https://shape.dk/images/employees/gert@2x.jpg"
      },
      {
        "normal" : "https://shape.dk/images/employees/gert_alternative_.jpg",
        "normal" : "https://shape.dk/images/employees/gert_alternative_@2x.jpg"
      }
    ]
  },
  "meta" : {
    "url" : "https://shape.dk/api/v1/employees/1"
  }
}
```

#### Get single object

#### Get multiple objects

##### Pagination


### Create data

#### Create single object

#### Required parameters missing

#### Object validation failed

#### Uploading images


### Update data

#### Update single object

#### Required parameters missing

#### Object validation failed


### Delete data

#### Delete single object








## Successful responses

Successful responses always returns JSON data in the following format:

```JSON
{
  "status" : "success",
  "data" : {
    "_id" : 1,
    "name" : "Gert Jørgensen",
    "title" : "Developer"
  },
  "meta" : {
    "url" : "https://shape.dk/api/v1/employees/1"
  }
}
```

For multiple resources the response would be:

```JSON
{
  "status" : "success",
  "data" : [
    {
      "_id" : 1,
      "name" : "Gert Jørgensen",
      "title" : "Developer",
      "meta" : {
        "url" : "https://shape.dk/api/v1/employees/1"
      }
    },
    {
      "_id" : 2,
      "name" : "Philip Bruce",
      "title" : "Partner",
      "meta" : {
        "url" : "https://shape.dk/api/v1/employees/2"
      }
    }
  ],
  "meta" : {
    "url" : "https://shape.dk/api/v1/employees"
  }
}
```

## Errors

Unsuccessful responses always returns JSON data in the following format:

```JSON
{
  "status" : "error",
  "data" : {
    "error_code" : "unauthorized",
    "message" : "Unauthorized"
  }
}
```
The possible error codes and their HTTP status are shown in the section: [Status codes](#status-codes).

## Status codes

### 200 ok

Returned for successful GET requests.

### 201 created

Returned for successful POST requests that create new entities in the database.

### 204 no_content

Returned for successful PATCH and DELETE requests that update or delete entities in the database.

### 400 params_missing

Returned for unsuccessful POST and PATCH requests if mandatory parameters are missing.

Example:

```JSON
{
  "status" : "error",
  "data" : {
    "error_code" : "param_missing",
    "message" : "param not found: name"
  }
}
```

### 401 unauthorized

Returned for any request with an invalid authentication token.

Example:

```JSON
{
  "status" : "error",
  "data" : {
    "error_code" : "unauthorized",
    "message" : "Unauthorized"
  }
}
```

### 403 forbidden

Returned for any request that the user don't have permissions for.

Example:

```JSON
{
  "status" : "error",
  "data" : {
    "error_code" : "forbidden",
    "message" : "User doesn't have rights to create employees"
  }
}
```

### 404 not_found

Returned for any request for which the entities was not found in the database.

```JSON
{
  "status" : "error",
  "data" : {
    "error_code" : "not_found",
    "message" : "Not found"
  }
}
```

### 500 exception

Returned for any request for which the server encountered an unknown error.

```JSON
{
  "status" : "error",
  "data" : {
    "error_code" : "exception",
    "message" : "The server encountered an unknown error"
  }
}
```

## Authentication

TODO.

## Date and time

All timestamps are returned in ISO 8601 format:

```
YYYY-MM-DDTHH:MM:SSZ
```

and time-only (without date):

```
THH:MM:SSZ
```

Timestamps are always saved and returned in UTC (GMT/Zulu)-time.

## Attachments

### Images

Images are returned as an "image" entity including a "normal" and a "retina" version:

```JSON
{
  "status" : "success",
  "data" : {
    "_id" : 1,
    "name" : "Gert Jørgensen",
    "title" : "Developer",
    "image" : {
      "normal" : "https://shape.dk/images/employees/gert.jpg",
      "normal" : "https://shape.dk/images/employees/gert@2x.jpg"
    }
  },
  "meta" : {
    "url" : "https://shape.dk/api/v1/employees/1"
  }
}
```

For multiple images the response would be:

```JSON
{
  "status" : "success",
  "data" : {
    "_id" : 1,
    "name" : "Gert Jørgensen",
    "title" : "Developer",
    "images" : [
      {
        "normal" : "https://shape.dk/images/employees/gert.jpg",
        "normal" : "https://shape.dk/images/employees/gert@2x.jpg"
      },
      {
        "normal" : "https://shape.dk/images/employees/gert_alternative_.jpg",
        "normal" : "https://shape.dk/images/employees/gert_alternative_@2x.jpg"
      }
    ]
  },
  "meta" : {
    "url" : "https://shape.dk/api/v1/employees/1"
  }
}
```

## File uploads

TODO.

## Pagination

TODO.