# API principles

* [Overview](#overview)
* [Parameters](#parameters)
* [HTTP verbs](#http-verbs)
* [Errors](#errors)
* [Status codes](#status-codes)
    * [200 ok](#200-ok)
    * [201 created](#201-created)
    * [204 no_content](#204-no_content)
    * [400 params_missing](#400-params_missing)
    * [401 unauthorized](#401-unauthorized)
    * [403 forbidden](#403-forbidden)
    * [404 not_found](#404-not_found)
    * [500 exception](#500-exception)
* [Authentication](#authentication)
* [Date and time](#date-and-time)
* [Attachments](#attachments)
    * [Images](#images)
* [File uploads](#file-uploads)
* [Meta](#meta)
* [Pagination](#pagination)

## Overview

TODO.

## Parameters

TODO.

## HTTP verbs

Where possible our API's strives to use appropriate HTTP verbs for each action.

Verb | Description
--- | ---
GET | Used for retrieving resources.
POST | Used for creating resources.
PATCH | Used for updating resources with partial JSON data. For instance, an Issue resource has title and body attributes. A PATCH request may accept one or more of the attributes to update the resource.
DELETE | Used for deleting resources.

## Errors

TODO.

## Status codes

### 200 ok

Returned for successful GET requests.

### 201 created

Returned for successful POST requests that create new entities in the database.

### 204 no_content

Returned for successful PATCH and DELETE requests that update or delete entities in the database.

### 400 params_missing

Returned for unsuccessful POST and PATCH requests if mandatory parameters are missing.

### 401 unauthorized

Returned for any request with an invalid authentication token.

### 403 forbidden

Returned for any request that the user don't have permissions for.

### 404 not_found

Returned for any request for which the entities was not found in the database.

### 500 exception

Returned for any request for which the server encountered an unknown error.

## Authentication

TODO.

## Date and time

TODO.

## Attachments

### Images

TODO.

## File uploads

TODO.

## Meta

TODO.

## Pagination

TODO.