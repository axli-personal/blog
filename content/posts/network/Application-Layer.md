---
title: "Application Layer"
date: 2022-07-10T12:00:00+08:00
categories: ["Network"]
summary: "Application Layer"
---

## HTTP

### Status Code

#### 301: Moved Permanently

#### 302: Found

#### 400: Bad Request

#### 403: Forbidden

* Not have sufficient access to perform action.
* Not have sufficient privilege to perform action.

#### 404: Not Found

### HTTP/1.1

* Request pipelining.

### HTTP/2

* Break down message into frames, schedule frames to mitigate head-of-line blocking.
* Allows each stream to have an associated weight and dependency.
* Push unrequested objects to client.
* Efficient coding for HTTP fields.
* All header field names are lowercase.
* Request line is split into individual `:method`, `:scheme`, `:authority`, and `:path` pseudo-header fields.