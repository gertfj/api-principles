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
    "message": "User doesn't have rights to create employees",
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

Returns status: 200 OK

```JSON
{
  "status": "success",
  "data": {
    "_id": 1,
    "body": "This is the body.",
    "created_at": "2013-12-10T15:13:22Z"
  }
}
```

#### Get multiple objects

Returns status: 200 OK

```JSON
{
  "status": "success",
  "data": [
    {
      "_id": 1,
      "body": "This is the body.",
      "created_at": "2013-12-10T15:13:22Z"
    },
    {
      "_id": 2,
      "body": "This is the second body.",
      "created_at": "2014-12-10T15:13:22Z"
    }
  ]
}
```

##### Pagination

Pagination info is included in the meta part of the response if available.

```JSON
{
  "status": "success",
  "data": [ ... ],
  "meta": {
    "current_page": 1,
    "total_page_count": 4,
    "page_record_count": 10,
    "total_record_count": 34,
    "links": {
      "next": "https://shape.dk/api/v1/employees/page/2",
      "prev": null,
      "last": "https://shape.dk/api/v1/employees/page/4",
      "first": "https://shape.dk/api/v1/employees/page/1"
    }
  }
}
```

### Create data

#### Create single object

Returns status: 201 Created

```JSON
{
  "status": "success",
  "data": {
    "_id": 1,
    "body": "This is the body.",
    "created_at": "2013-12-10T15:13:22Z"
  }
}
```

#### Required parameters missing

Returns status: 400 Bad Request

```JSON
{
  "status": "error",
  "data": {
    "message": "Param not found: employee_title",
    "error_code": "param_missing"
  }
}
```

#### Object validation failed

Returns status: 422 Unprocessable Entity

TODO: Update with example showing which parameters have invalid data and what the errors are.

```JSON
{
  "status": "error",
  "data": {
    "message": "Invalid",
    "error_code": "invalid"
  }
}
```

#### Uploading images

TODO.

### Update data

#### Update single object

Returns status: 204 No Content

#### Required parameters missing

Returns status: 400 Bad Request

```JSON
{
  "status": "error",
  "data": {
    "message": "Param not found: employee_title",
    "error_code": "param_missing"
  }
}
```

#### Object validation failed

Returns status: 422 Unprocessable Entity

TODO: Update with example showing which parameters have invalid data and what the errors are.

```JSON
{
  "status": "error",
  "data": {
    "message": "Invalid",
    "error_code": "invalid"
  }
}
```

### Delete data

#### Delete single object

Returns status: 204 No Content


