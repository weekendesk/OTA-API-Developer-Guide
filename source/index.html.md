---
title: OTA API Developer Guide

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - php
  - csharp
  - java


toc_footers:
  - <a href='http://www.weekendesk.fr' target='_blank'>Access Weekendesk Website</a>
  - <a href='http://partners.weekendesk.com' target='_blank'>Access the Extranet</a>

includes:
  - errors

search: true
---

# Introduction to the Developer Guide

This document describes the interface specifications between the Weekendesk and a generic Channel Manager partner.<br>
The interface developed by the Channel partner, according to the specifications present in this document, will allow for “2-way” communication to be set up which will provide the following features:<br>

1. **ARI updates**: availability and restrictions, rates and inventory sent by the Channel partner and saved into Weekendesk inventory system
2. **Booking Notifications**: reservations received by Weekendesk are sent real time to the Channel partner.

Following this document, the Channel Manager partner can develop a web-service (client side) that will be compatible with Weekendesk inventory system (server side).

# OTA Standard

## Description

XML messages exchanged by the standard interface are based on standard Open Travel Alliance (OTA) 2003B.<br>
For further information on these standards, please consult:<br>

<b>OTA</b>: http://www.opentravel.org<br>
<b>XMLSchema</b>: http://www.w3.org/2001/XMLSchema

## Messages

The standard interface of Weekendesk handles several cases based on the following OTA messages:

### OTA_Ping

Ping

### OTA_HotelRoomList

Room List

### OTA_HotelAvailNotif

Availability and Restrictions

### OTA_HotelRateAmountNotif

Rates

#Setup

In this section it will be described all necessary steps to get your connectivity up and running.
<br>
Before starting remember to send the list of IPs you will be using both in testing and production to your technical contact at Weekendesk.

## Authentication

> To authorize, use this code:


```shell
  echo -n '102 iateasha' | base64
```

```php
$header = "Authorization: Basic " . base64_encode($username . ':' . $password);
```

> Make sure to replace `TEStTesttEsTt3st` with your Basic Authentication.

Weekendesk uses Basic Authentication to allow access to the API. You can request your user and password at your technical point of contact in Weekendesk.

Weekendesk expects for the Basic Authentication to be included in all API requests to the server in a header that looks like the following:

`Authorization: Basic TEStTesttEsTt3st`

<aside class="notice">
You must replace <code>TEStTesttEsTt3st</code> with your user and password encrypted in base64_encode.
</aside>

##Testing the connectivity

This message is used to test that the connectivity is working, your IPs whitelisted and basic authentication you are using is correct.

```shell
curl
  -X POST
  -H "Content-Type: text/xml"
  -H "Authorization: Basic TEStTesttEsTt3st"
  -H "Cache-Control: no-cache"
  -d '<?xml version="1.0" encoding="UTF-8"?>
<OTA_PingRQ Version="1.01" TimeStamp="2008-05-29T10:58:21">
</OTA_PingRQ>' "http://qualification-ari.weekendesk.com/ari/"
```

```php
<?php

$request = new HttpRequest();
$request->setUrl('qualification-ari.weekendesk.com/ari/');
$request->setMethod(HTTP_METH_POST);

$request->setHeaders(array(
  'cache-control' => 'no-cache',
  'authorization' => 'Basic TEStTesttEsTt3st',
  'content-type' => 'text/xml'
));

$request->setBody('<?xml version="1.0" encoding="UTF-8"?>
<OTA_PingRQ Version="1.01" TimeStamp="2008-05-29T10:58:21">
</OTA_PingRQ>');

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}
```

```csharp
var client = new RestClient("http://qualification-ari.weekendesk.com/ari/");
var request = new RestRequest(Method.POST);
request.AddHeader("cache-control", "no-cache");
request.AddHeader("authorization", "Basic TEStTesttEsTt3st");
request.AddHeader("content-type", "text/xml");
request.AddParameter("text/xml", "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n<OTA_PingRQ Version=\"1.01\" TimeStamp=\"2008-05-29T10:58:21\">\n</OTA_PingRQ>", ParameterType.RequestBody);
IRestResponse response = client.Execute(request);
```

```java
OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("text/xml");
RequestBody body = RequestBody.create(mediaType, "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n<OTA_PingRQ Version=\"1.01\" TimeStamp=\"2008-05-29T10:58:21\">\n</OTA_PingRQ>");
Request request = new Request.Builder()
  .url("http://qualification-ari.weekendesk.com/ari/")
  .post(body)
  .addHeader("content-type", "text/xml")
  .addHeader("authorization", "Basic TEStTesttEsTt3st")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();
```

> The above command returns XML structured like this:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<OTA_PingRS xmlns="http://www.opentravel.org/OTA/2003/05" TimeStamp="">
    <Success/>
</OTA_PingRS>
```

### HTTP Request

Testing:<br>
`POST` http://qualification-ari.weekendesk.com/ari/
<br>Production:<br>
`POST` http://ari.weekendesk.com/ari/


##Mapping

In order to start updating the information into the Weekendesk system you will have to map the IDs of the Room and RatePlans with the ones within your system.<br>
Weekendesk allows you to do that by using the OTA_HotelRoomList request which fetch all rooms and rate plans given a certain Weekendesk HotelID.

```shell
curl
  -X POST
  -H "Content-Type: text/xml"
  -H "Authorization: Basic TEStTesttEsTt3st"
  -H "Cache-Control: no-cache"
  -d '<?xml version="1.0" encoding="UTF-8"?>
<OTA_PingRQ Version="1.01" TimeStamp="2008-05-29T10:58:21">
</OTA_PingRQ>' "http://qualification-ari.weekendesk.com/ari/"
```

```php
<?php

$request = new HttpRequest();
$request->setUrl('qualification-ari.weekendesk.com/ari/');
$request->setMethod(HTTP_METH_POST);

$request->setHeaders(array(
  'cache-control' => 'no-cache',
  'authorization' => 'Basic TEStTesttEsTt3st',
  'content-type' => 'text/xml'
));

$request->setBody('<?xml version="1.0" encoding="UTF-8"?>
<OTA_PingRQ Version="1.01" TimeStamp="2008-05-29T10:58:21">
</OTA_PingRQ>');

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}
```

```csharp
var client = new RestClient("http://qualification-ari.weekendesk.com/ari/");
var request = new RestRequest(Method.POST);
request.AddHeader("cache-control", "no-cache");
request.AddHeader("authorization", "Basic TEStTesttEsTt3st");
request.AddHeader("content-type", "text/xml");
request.AddParameter("text/xml", "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n<OTA_PingRQ Version=\"1.01\" TimeStamp=\"2008-05-29T10:58:21\">\n</OTA_PingRQ>", ParameterType.RequestBody);
IRestResponse response = client.Execute(request);
```

```java
OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("text/xml");
RequestBody body = RequestBody.create(mediaType, "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n<OTA_PingRQ Version=\"1.01\" TimeStamp=\"2008-05-29T10:58:21\">\n</OTA_PingRQ>");
Request request = new Request.Builder()
  .url("http://qualification-ari.weekendesk.com/ari/")
  .post(body)
  .addHeader("content-type", "text/xml")
  .addHeader("authorization", "Basic TEStTesttEsTt3st")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();
```

> The above command returns XML structured like this:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelRoomListRS xmlns="http://www.opentravel.org/OTA/2003/05" Version="2.0">
    <Success />
    <HotelRoomLists>
        <HotelRoomList HotelCode="AL_TEST">
            <RoomStays>
                <RoomStay>
                    <RoomTypes>
                        <RoomType IsRoom="true" RoomID="RO_TEST">
                            <RoomDescription Name="Test" />
                        </RoomType>
                    </RoomTypes>
                    <RatePlans>
                        <RatePlan RatePlanName="BAR" RatePlanID="001" />
                        <RatePlan RatePlanName="SALE" RatePlanID="003" />
                    </RatePlans>
                </RoomStay>
            </RoomStays>
        </HotelRoomList>
    </HotelRoomLists>
</OTA_HotelRoomListRS>
```

### HTTP Request

Testing:<br>
`POST` http://qualification-ari.weekendesk.com/ari/
<br>Production:<br>
`POST` http://ari.weekendesk.com/ari/

### Query Parameters

Element | Type | Required | Description
--------- | ------- | ----- |-----------
ID | string | Yes | The Weekendesk Hotel ID


# ARI Updates

## Update Availability

Weekendesk handles

```shell
curl -X POST
-H "Authorization: Basic TEStTesttEsTt3st"
-H "Content-Type: text/xml" -H "Cache-Control: no-cache"
-d '<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelAvailNotifRQ xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-24T23:00:26" Target="Production">
   <AvailStatusMessages HotelCode="AL_TEST">
      <AvailStatusMessage LocatorID="1">
         <StatusApplicationControl Start="2017-09-01" End="2017-09-02" RatePlanCode="003" InvTypeCode="RO_TEST" />
         <RestrictionStatus Status="Open" />
      </AvailStatusMessage>
   </AvailStatusMessages>
</OTA_HotelAvailNotifRQ>
' "http://qualification-ari.weekendesk.com/ari/"
```

```php
<?php

$request = new HttpRequest();
$request->setUrl('http://qualification-ari.weekendesk.com/ari/');
$request->setMethod(HTTP_METH_POST);

$request->setHeaders(array(
  'cache-control' => 'no-cache',
  'content-type' => 'text/xml',
  'authorization' => 'Basic TEStTesttEsTt3st'
));

$request->setBody('<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelAvailNotifRQ xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-24T23:00:26" Target="Production">
   <AvailStatusMessages HotelCode="AL_TEST">
      <AvailStatusMessage LocatorID="1">
         <StatusApplicationControl Start="2017-09-01" End="2017-09-02" RatePlanCode="003" InvTypeCode="RO_TEST" />
         <RestrictionStatus Status="Open" />
      </AvailStatusMessage>
   </AvailStatusMessages>
</OTA_HotelAvailNotifRQ>
');

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}
```

```csharp
var client = new RestClient("http://qualification-ari.weekendesk.com/ari/");
var request = new RestRequest(Method.POST);
request.AddHeader("cache-control", "no-cache");
request.AddHeader("authorization", "Basic TEStTesttEsTt3st");
request.AddHeader("content-type", "text/xml");
request.AddParameter("text/xml", "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n<OTA_PingRQ Version=\"1.01\" TimeStamp=\"2008-05-29T10:58:21\">\n</OTA_PingRQ>", ParameterType.RequestBody);
IRestResponse response = client.Execute(request);
```

```java
OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("text/xml");
RequestBody body = RequestBody.create(mediaType, "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n<OTA_PingRQ Version=\"1.01\" TimeStamp=\"2008-05-29T10:58:21\">\n</OTA_PingRQ>");
Request request = new Request.Builder()
  .url("http://qualification-ari.weekendesk.com/ari/")
  .post(body)
  .addHeader("content-type", "text/xml")
  .addHeader("authorization", "Basic TEStTesttEsTt3st")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();
```

> The above command returns XML structured like this:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelRoomListRS xmlns="http://www.opentravel.org/OTA/2003/05" Version="2.0">
    <Success />
    <HotelRoomLists>
        <HotelRoomList HotelCode="AL_TEST">
            <RoomStays>
                <RoomStay>
                    <RoomTypes>
                        <RoomType IsRoom="true" RoomID="RO_TEST">
                            <RoomDescription Name="Test" />
                        </RoomType>
                    </RoomTypes>
                    <RatePlans>
                        <RatePlan RatePlanName="BAR" RatePlanID="001" />
                        <RatePlan RatePlanName="SALE" RatePlanID="003" />
                    </RatePlans>
                </RoomStay>
            </RoomStays>
        </HotelRoomList>
    </HotelRoomLists>
</OTA_HotelRoomListRS>
```

### HTTP Request

Testing:<br>
`POST` http://qualification-ari.weekendesk.com/ari/
<br>Production:<br>
`POST` http://ari.weekendesk.com/ari/

### Query Parameters

Element | Type | Required | Description
--------- | ------- | ----- |-----------
ID | string | Yes | The Weekendesk Hotel ID

##Update Restrictions

In order to start updating the information into the Weekendesk system you will have to map the IDs of the Room and RatePlans with the ones within your system.<br>
Weekendesk allows you to do that by using the OTA_HotelRoomList request which fetch all rooms and rate plans given a certain Weekendesk HotelID.

```shell
curl
  -X POST
  -H "Content-Type: text/xml"
  -H "Authorization: Basic TEStTesttEsTt3st"
  -H "Cache-Control: no-cache"
  -d '<?xml version="1.0" encoding="UTF-8"?>
<OTA_PingRQ Version="1.01" TimeStamp="2008-05-29T10:58:21">
</OTA_PingRQ>' "http://qualification-ari.weekendesk.com/ari/"
```

```php
<?php

$request = new HttpRequest();
$request->setUrl('qualification-ari.weekendesk.com/ari/');
$request->setMethod(HTTP_METH_POST);

$request->setHeaders(array(
  'cache-control' => 'no-cache',
  'authorization' => 'Basic TEStTesttEsTt3st',
  'content-type' => 'text/xml'
));

$request->setBody('<?xml version="1.0" encoding="UTF-8"?>
<OTA_PingRQ Version="1.01" TimeStamp="2008-05-29T10:58:21">
</OTA_PingRQ>');

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}
```

```csharp
var client = new RestClient("http://qualification-ari.weekendesk.com/ari/");
var request = new RestRequest(Method.POST);
request.AddHeader("cache-control", "no-cache");
request.AddHeader("authorization", "Basic TEStTesttEsTt3st");
request.AddHeader("content-type", "text/xml");
request.AddParameter("text/xml", "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n<OTA_PingRQ Version=\"1.01\" TimeStamp=\"2008-05-29T10:58:21\">\n</OTA_PingRQ>", ParameterType.RequestBody);
IRestResponse response = client.Execute(request);
```

```java
OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("text/xml");
RequestBody body = RequestBody.create(mediaType, "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n<OTA_PingRQ Version=\"1.01\" TimeStamp=\"2008-05-29T10:58:21\">\n</OTA_PingRQ>");
Request request = new Request.Builder()
  .url("http://qualification-ari.weekendesk.com/ari/")
  .post(body)
  .addHeader("content-type", "text/xml")
  .addHeader("authorization", "Basic TEStTesttEsTt3st")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();
```

> The above command returns XML structured like this:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelRoomListRS xmlns="http://www.opentravel.org/OTA/2003/05" Version="2.0">
    <Success />
    <HotelRoomLists>
        <HotelRoomList HotelCode="AL_TEST">
            <RoomStays>
                <RoomStay>
                    <RoomTypes>
                        <RoomType IsRoom="true" RoomID="RO_TEST">
                            <RoomDescription Name="Test" />
                        </RoomType>
                    </RoomTypes>
                    <RatePlans>
                        <RatePlan RatePlanName="BAR" RatePlanID="001" />
                        <RatePlan RatePlanName="SALE" RatePlanID="003" />
                    </RatePlans>
                </RoomStay>
            </RoomStays>
        </HotelRoomList>
    </HotelRoomLists>
</OTA_HotelRoomListRS>
```

### HTTP Request

Testing:<br>
`POST` http://qualification-ari.weekendesk.com/ari/
<br>Production:<br>
`POST` http://ari.weekendesk.com/ari/

### Query Parameters

Element | Type | Required | Description
--------- | ------- | ----- |-----------
ID | string | Yes | The Weekendesk Hotel ID

##Update Inventory

In order to start updating the information into the Weekendesk system you will have to map the IDs of the Room and RatePlans with the ones within your system.<br>
Weekendesk allows you to do that by using the OTA_HotelRoomList request which fetch all rooms and rate plans given a certain Weekendesk HotelID.

```shell
curl
  -X POST
  -H "Content-Type: text/xml"
  -H "Authorization: Basic TEStTesttEsTt3st"
  -H "Cache-Control: no-cache"
  -d '<?xml version="1.0" encoding="UTF-8"?>
<OTA_PingRQ Version="1.01" TimeStamp="2008-05-29T10:58:21">
</OTA_PingRQ>' "http://qualification-ari.weekendesk.com/ari/"
```

```php
<?php

$request = new HttpRequest();
$request->setUrl('qualification-ari.weekendesk.com/ari/');
$request->setMethod(HTTP_METH_POST);

$request->setHeaders(array(
  'cache-control' => 'no-cache',
  'authorization' => 'Basic TEStTesttEsTt3st',
  'content-type' => 'text/xml'
));

$request->setBody('<?xml version="1.0" encoding="UTF-8"?>
<OTA_PingRQ Version="1.01" TimeStamp="2008-05-29T10:58:21">
</OTA_PingRQ>');

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}
```

```csharp
var client = new RestClient("http://qualification-ari.weekendesk.com/ari/");
var request = new RestRequest(Method.POST);
request.AddHeader("cache-control", "no-cache");
request.AddHeader("authorization", "Basic TEStTesttEsTt3st");
request.AddHeader("content-type", "text/xml");
request.AddParameter("text/xml", "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n<OTA_PingRQ Version=\"1.01\" TimeStamp=\"2008-05-29T10:58:21\">\n</OTA_PingRQ>", ParameterType.RequestBody);
IRestResponse response = client.Execute(request);
```

```java
OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("text/xml");
RequestBody body = RequestBody.create(mediaType, "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n<OTA_PingRQ Version=\"1.01\" TimeStamp=\"2008-05-29T10:58:21\">\n</OTA_PingRQ>");
Request request = new Request.Builder()
  .url("http://qualification-ari.weekendesk.com/ari/")
  .post(body)
  .addHeader("content-type", "text/xml")
  .addHeader("authorization", "Basic TEStTesttEsTt3st")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();
```

> The above command returns XML structured like this:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelRoomListRS xmlns="http://www.opentravel.org/OTA/2003/05" Version="2.0">
    <Success />
    <HotelRoomLists>
        <HotelRoomList HotelCode="AL_TEST">
            <RoomStays>
                <RoomStay>
                    <RoomTypes>
                        <RoomType IsRoom="true" RoomID="RO_TEST">
                            <RoomDescription Name="Test" />
                        </RoomType>
                    </RoomTypes>
                    <RatePlans>
                        <RatePlan RatePlanName="BAR" RatePlanID="001" />
                        <RatePlan RatePlanName="SALE" RatePlanID="003" />
                    </RatePlans>
                </RoomStay>
            </RoomStays>
        </HotelRoomList>
    </HotelRoomLists>
</OTA_HotelRoomListRS>
```

### HTTP Request

Testing:<br>
`POST` http://qualification-ari.weekendesk.com/ari/
<br>Production:<br>
`POST` http://ari.weekendesk.com/ari/

### Query Parameters

Element | Type | Required | Description
--------- | ------- | ----- |-----------
ID | string | Yes | The Weekendesk Hotel ID

##Update Rates

In order to start updating the information into the Weekendesk system you will have to map the IDs of the Room and RatePlans with the ones within your system.<br>
Weekendesk allows you to do that by using the OTA_HotelRoomList request which fetch all rooms and rate plans given a certain Weekendesk HotelID.

```shell
curl
  -X POST
  -H "Content-Type: text/xml"
  -H "Authorization: Basic TEStTesttEsTt3st"
  -H "Cache-Control: no-cache"
  -d '<?xml version="1.0" encoding="UTF-8"?>
<OTA_PingRQ Version="1.01" TimeStamp="2008-05-29T10:58:21">
</OTA_PingRQ>' "http://qualification-ari.weekendesk.com/ari/"
```

```php
<?php

$request = new HttpRequest();
$request->setUrl('qualification-ari.weekendesk.com/ari/');
$request->setMethod(HTTP_METH_POST);

$request->setHeaders(array(
  'cache-control' => 'no-cache',
  'authorization' => 'Basic TEStTesttEsTt3st',
  'content-type' => 'text/xml'
));

$request->setBody('<?xml version="1.0" encoding="UTF-8"?>
<OTA_PingRQ Version="1.01" TimeStamp="2008-05-29T10:58:21">
</OTA_PingRQ>');

try {
  $response = $request->send();

  echo $response->getBody();
} catch (HttpException $ex) {
  echo $ex;
}
```

```csharp
var client = new RestClient("http://qualification-ari.weekendesk.com/ari/");
var request = new RestRequest(Method.POST);
request.AddHeader("cache-control", "no-cache");
request.AddHeader("authorization", "Basic TEStTesttEsTt3st");
request.AddHeader("content-type", "text/xml");
request.AddParameter("text/xml", "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n<OTA_PingRQ Version=\"1.01\" TimeStamp=\"2008-05-29T10:58:21\">\n</OTA_PingRQ>", ParameterType.RequestBody);
IRestResponse response = client.Execute(request);
```

```java
OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("text/xml");
RequestBody body = RequestBody.create(mediaType, "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n<OTA_PingRQ Version=\"1.01\" TimeStamp=\"2008-05-29T10:58:21\">\n</OTA_PingRQ>");
Request request = new Request.Builder()
  .url("http://qualification-ari.weekendesk.com/ari/")
  .post(body)
  .addHeader("content-type", "text/xml")
  .addHeader("authorization", "Basic TEStTesttEsTt3st")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();
```

> The above command returns XML structured like this:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelRoomListRS xmlns="http://www.opentravel.org/OTA/2003/05" Version="2.0">
    <Success />
    <HotelRoomLists>
        <HotelRoomList HotelCode="AL_TEST">
            <RoomStays>
                <RoomStay>
                    <RoomTypes>
                        <RoomType IsRoom="true" RoomID="RO_TEST">
                            <RoomDescription Name="Test" />
                        </RoomType>
                    </RoomTypes>
                    <RatePlans>
                        <RatePlan RatePlanName="BAR" RatePlanID="001" />
                        <RatePlan RatePlanName="SALE" RatePlanID="003" />
                    </RatePlans>
                </RoomStay>
            </RoomStays>
        </HotelRoomList>
    </HotelRoomLists>
</OTA_HotelRoomListRS>
```

### HTTP Request

Testing:<br>
`POST` http://qualification-ari.weekendesk.com/ari/
<br>Production:<br>
`POST` http://ari.weekendesk.com/ari/

### Query Parameters

Element | Type | Required | Description
--------- | ------- | ----- |-----------
ID | string | Yes | The Weekendesk Hotel ID

##Inventory

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let kittens = api.kittens.get();
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

# Receive Booking Notifications

## Get All Kittens

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let kittens = api.kittens.get();
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

## Delete a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.delete(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.delete(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -X DELETE
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.delete(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "deleted" : ":("
}
```

This endpoint retrieves a specific kitten.

### HTTP Request

`DELETE http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to delete

# Receive Cancellation Notifications

## Get All Kittens

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let kittens = api.kittens.get();
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

## Delete a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.delete(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.delete(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -X DELETE
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.delete(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "deleted" : ":("
}
```

This endpoint retrieves a specific kitten.

### HTTP Request

`DELETE http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to delete
