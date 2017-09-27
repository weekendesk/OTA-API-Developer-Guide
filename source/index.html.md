---
title: OTA API Developer Guide

language_tabs: # must be one of https://git.io/vQNgJ
  - xml
  - shell
  - php
  - csharp
  - java


toc_footers:
  - <a href='http://www.weekendesk.fr' target='_blank'>Weekendesk Website</a>
  - <a href='http://partners.weekendesk.com' target='_blank'>Partner Extranet</a>
  - <a href='http://test-partners.weekendesk.com' target='_blank'>TEST - Partner Extranet</a>

includes:
  - errors

search: true
---

# Introduction

This document describes the interface specifications between the Weekendesk and a Channel Manager partner.<br>
The interface developed by the Channel partner, according to the specifications present in this document, will allow a “2-ways” communication which will provide the following features:<br>

1. **ARI updates**: Availability&Restrictions, Rates and Inventory are received from Channel partner and saved into Weekendesk inventory system
2. **Booking Notifications**: Reservations as soon as they are confirmed on Weekendesk front Website are PUSHed to the channel partner. When a reservation is cancelled a Cancellation notification is also PUSHed to the channel system.

Following this document, the Channel Manager partner can develop a web-service (client side) that will be compatible with Weekendesk inventory system (server side).

# OTA Standard

## Description

XML messages exchanged by the standard interface are based on standard Open Travel Alliance (OTA) 2003B.<br>
For further information on these standards, please consult:<br>

<b>OTA</b>:<a href='http://www.opentravel.org' target='blank'>http://www.opentravel.org</a><br>
<b>XMLSchema</b>:<a href='http://www.w3.org/2001/XMLSchema' target='blank'>http://www.w3.org/2001/XMLSchema</a>


## Messages

The standard interface of Weekendesk handles several cases based on the following OTA messages:

### OTA_Ping

The Ping RQ/RS allows the channel partner to test the connectivity is working.

### OTA_HotelRoomList

The RoomList RQ/RS is used by the channel partner to map the Weekendesk rooms and rate plans with the ones on its system.

### OTA_HotelAvailNotif

The Availability Notification RQ/RS allows the channel partner to send Availability, Restrictions and Booking Limits to Weekendesk for each rate plan mapped in its system.

### OTA_HotelRateAmountNotif

The Rates Amount Notification RQ/RS allows the channel partner to send prices for each rate that is mapped in its system.

#Setup

In this section it will be described all necessary steps to get your connectivity up and running.
<br>
Important things to remember before starting:
 <ul> - Every message will be exchanged on HTTP transport layer via HTTP POST method. </ul>
 <ul> - Request the credentials to your technical contact at Weekendesk in order to start making requests.</ul>
<ul> - A login and a password to connect to the Extranet may be requested and provided to the channel.</ul>

<aside class="success">
Remember - Before starting <b>send the list of IPs</b> and <b>the Endpoint for Reservation Delivery</b> you will be using both in testing and production to your technical contact at Weekendesk.
</aside>


## Authentication

> To authorize, use this code:

```xml
  Basic Auth user:password
```

```shell
  echo -n '102 iateasha' | base64
```

```php
$header = "Authorization: Basic " . base64_encode($username . ':' . $password);
```

```csharp
No command found
```

```java
No command found
```

> Make sure to replace `TEStTesttEsTt3st` with your Basic Authentication.

Weekendesk uses Basic Authentication to authenticate all requests. You can request your user and password at your technical point of contact in Weekendesk.

Weekendesk expects for the Basic Authentication to be included in all API requests to the server in a header that looks like the following:

`Authorization: Basic TEStTesttEsTt3st`

<aside class="notice">
You must replace <code>TEStTesttEsTt3st</code> with your user and password encrypted in base64_encode.
</aside>

##Testing the connectivity

This message is used to test that the connectivity is working, your IPs whitelisted and basic authentication you are using is correct.

```xml
HEADER
Authorization: Basic TEStTesttEsTt3st
Content-Type: text/xml

BODY
<?xml version="1.0" encoding="UTF-8"?>
<OTA_PingRQ Version="1.01" TimeStamp="2008-05-29T10:58:21">
</OTA_PingRQ>
```

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

```shell
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<OTA_PingRS xmlns="http://www.opentravel.org/OTA/2003/05" TimeStamp="">
    <Success/>
</OTA_PingRS>
```

```php
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<OTA_PingRS xmlns="http://www.opentravel.org/OTA/2003/05" TimeStamp="">
    <Success/>
</OTA_PingRS>
```

```csharp
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<OTA_PingRS xmlns="http://www.opentravel.org/OTA/2003/05" TimeStamp="">
    <Success/>
</OTA_PingRS>
```

```java
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


#Retrieve Rooms

In order to start updating the information into the Weekendesk system you will have to map the IDs of the Room and RatePlans with the ones within your system.<br>
Weekendesk allows you to do that by using the OTA_HotelRoomList request which fetch all rooms and rate plans given a certain Weekendesk HotelID.

```xml
HEADER
Authorization: Basic TEStTesttEsTt3st
Content-Type: text/xml

BODY
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelRoomListRQ xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
 <POS>
    <Source>
       <RequestorID Type="1" ID="AL_TEST" />
    </Source>
 </POS>
 <HotelRoomLists>
    <HotelRoomList />
 </HotelRoomLists>
</OTA_HotelRoomListRQ>
```

```shell
curl
 -X POST
 -H "Content-Type: text/xml"
 -H "Authorization: Basic TEStTesttEsTt3st"
 -H "Cache-Control: no-cache"
 -d '<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelRoomListRQ xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
 <POS>
    <Source>
       <RequestorID Type="1" ID="AL_TEST" />
    </Source>
 </POS>
 <HotelRoomLists>
    <HotelRoomList />
 </HotelRoomLists>
</OTA_HotelRoomListRQ>' "http://qualification-ari.weekendesk.com/ari/"
```

```php
<?php

$request = new HttpRequest();
$request->setUrl('http://qualification-ari.weekendesk.com/ari/');
$request->setMethod(HTTP_METH_POST);

$request->setHeaders(array(
  'cache-control' => 'no-cache',
  'authorization' => 'Basic TEStTesttEsTt3st',
  'content-type' => 'text/xml'
));

$request->setBody('<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelRoomListRQ xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
   <POS>
      <Source>
         <RequestorID Type="1" ID="AL_TEST" />
      </Source>
   </POS>
   <HotelRoomLists>
      <HotelRoomList />
   </HotelRoomLists>
</OTA_HotelRoomListRQ>');

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
request.AddParameter("text/xml", "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\r\n<OTA_HotelRoomListRQ xmlns=\"http://www.opentravel.org/OTA/2003/05\" xmlns:xsd=\"http://www.w3.org/2001/XMLSchema\" xmlns:xsi=\"http://www.w3.org/2001/XMLSchema-instance\">\r\n   <POS>\r\n      <Source>\r\n         <RequestorID Type=\"1\" ID=\"AL_TEST\" />\r\n      </Source>\r\n   </POS>\r\n   <HotelRoomLists>\r\n      <HotelRoomList />\r\n   </HotelRoomLists>\r\n</OTA_HotelRoomListRQ>", ParameterType.RequestBody);
IRestResponse response = client.Execute(request);
```

```java
OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("text/xml");
RequestBody body = RequestBody.create(mediaType, "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\r\n<OTA_HotelRoomListRQ xmlns=\"http://www.opentravel.org/OTA/2003/05\" xmlns:xsd=\"http://www.w3.org/2001/XMLSchema\" xmlns:xsi=\"http://www.w3.org/2001/XMLSchema-instance\">\r\n   <POS>\r\n      <Source>\r\n         <RequestorID Type=\"1\" ID=\"AL_TEST\" />\r\n      </Source>\r\n   </POS>\r\n   <HotelRoomLists>\r\n      <HotelRoomList />\r\n   </HotelRoomLists>\r\n</OTA_HotelRoomListRQ>");
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

```shell
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

```php
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

```csharp
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

```java
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
Type | integer | Yes | Always use Type=1 for Hotels
ID | string | Yes | Weekendesk Hotel ID


# ARI Updates

The updates of Availability, Rates and Inventory are performed by the Channel partner to Weekendesk system.<br>Data receive via these updates are reflected in the product displayed on the front which are updated consequently.<br>
In details<br>
- The <b> OTA_HotelAvailNotifRQ </b> handles Availability, Restrictions and Inventory<br>
- The <b> OTA_HotelRateAmountNotifRQ </b> handles the prices for each Rate

## Update Availability

The Channel partner can Open or Close the availability of any RatePlan of any Room mapped with Weekendesk. Once Closed, the RatePlan will not be available for sale on the Website and Applications.

<aside class="success">
Remember - The Availability is per RatePlan. If your Room has more than one RatePlan <b>only the RatePlan that is specified in the request</b> will be Closed (or Opened).
</aside>

```xml
HEADER
Authorization: Basic TEStTesttEsTt3st
Content-Type: text/xml

BODY
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelAvailNotifRQ xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-24T23:00:26" Target="Production">
   	<AvailStatusMessages HotelCode="AL_TEST">
      <AvailStatusMessage LocatorID="1">
         <StatusApplicationControl Start="2017-09-01" End="2017-09-02" RatePlanCode="003" InvTypeCode="RO_TEST" />
         <RestrictionStatus Status="Open" />
      </AvailStatusMessage>
    </AvailStatusMessages>
</OTA_HotelAvailNotifRQ>
```

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
request.AddHeader("content-type", "text/xml");
request.AddHeader("authorization", "Basic TEStTesttEsTt3st");
request.AddParameter("text/xml", "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\r\n
<OTA_HotelAvailNotifRQ xmlns=\"http://www.opentravel.org/OTA/2003/05\" xmlns:xsd=\"http://www.w3.org/2001/XMLSchema\" xmlns:xsi=\"http://www.w3.org/2001/XMLSchema-instance\" Version=\"1\" TimeStamp=\"2016-02-24T23:00:26\" Target=\"Production\">\r\n   \t<AvailStatusMessages HotelCode=\"AL_TEST\">\r\n      
<AvailStatusMessage LocatorID=\"1\">\r\n         
<StatusApplicationControl Start=\"2017-09-01\" End=\"2017-09-02\" RatePlanCode=\"003\" InvTypeCode=\"RO_TEST\" />\r\n         
<RestrictionStatus Status=\"Open\" />\r\n      
</AvailStatusMessage>\r\n    </AvailStatusMessages>\r\n</OTA_HotelAvailNotifRQ>\r\n", ParameterType.RequestBody);
IRestResponse response = client.Execute(request);
```

```java
OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("text/xml");
RequestBody body = RequestBody.create(mediaType,
"<?xml version=\"1.0\" encoding=\"UTF-8\"?>\r\n
<OTA_HotelAvailNotifRQ xmlns=\"http://www.opentravel.org/OTA/2003/05\" xmlns:xsd=\"http://www.w3.org/2001/XMLSchema\" xmlns:xsi=\"http://www.w3.org/2001/XMLSchema-instance\" Version=\"1\" TimeStamp=\"2016-02-24T23:00:26\" Target=\"Production\">\r\n   \t<AvailStatusMessages HotelCode=\"AL_TEST\">\r\n      
<AvailStatusMessage LocatorID=\"1\">\r\n         
<StatusApplicationControl Start=\"2018-09-01\" End=\"2018-09-02\" RatePlanCode=\"003\" InvTypeCode=\"RO_TEST\" />\r\n         
<RestrictionStatus Status=\"Open\" />\r\n      
</AvailStatusMessage>\r\n    
</AvailStatusMessages>\r\n
</OTA_HotelAvailNotifRQ>\r\n");
Request request = new Request.Builder()
  .url("http://qualification-ari.weekendesk.com/ari/")
  .post(body)
  .addHeader("authorization", "Basic TEStTesttEsTt3st")
  .addHeader("content-type", "text/xml")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();
```

> The above command returns XML structured like this:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelAvailNotifRS xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-24T23:00:26" Target="Production">
    <Success />
</OTA_HotelAvailNotifRS>
```

```shell
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelAvailNotifRS xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-24T23:00:26" Target="Production">
    <Success />
</OTA_HotelAvailNotifRS>
```

```php
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelAvailNotifRS xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-24T23:00:26" Target="Production">
    <Success />
</OTA_HotelAvailNotifRS>
```

```csharp
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelAvailNotifRS xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-24T23:00:26" Target="Production">
    <Success />
</OTA_HotelAvailNotifRS>
```

```java
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelAvailNotifRS xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-24T23:00:26" Target="Production">
    <Success />
</OTA_HotelAvailNotifRS>
```

### HTTP Request

Testing:<br>
`POST` http://qualification-ari.weekendesk.com/ari/
<br>Production:<br>
`POST` http://ari.weekendesk.com/ari/

### Query Parameters

Element | Type | Required | Description
--------- | ------- | ----- |-----------
HotelCode | string | Yes | The Weekendesk Hotel ID
LocatorID | integer | Yes | Progressive number based on the number of AvailStatusMessage tag within the same request
Start | date | Yes | Starting date of the updated period
End | date | Yes | Ending date of the updated period
RatePlanCode | string | Yes | Code of the Rate Plan for which the update is sent
InvTypeCode | string | Yes | Code of the Room for which the update is sent
Status | string | Yes | Open=The rate plan is sellable; Close=Stop sale

##Update Restrictions

Weekendesk handles several types of restrictions.
<br><br>
- MinStay (Minimum Stay Through)<br>
- MaxStay (Maximum Stay Through)<br>
- CTA (Close to Arrival)<br>
- CTD (Close to Departure)<br>
<br>Each of the these restrictions can be sent in the same request and/or combined with Availability and Inventory updates.

```xml
HEADER
Authorization: Basic TEStTesttEsTt3st
Content-Type: text/xml

BODY
<OTA_HotelAvailNotifRQ xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-26T10:32:51" Target="Production">
	   <AvailStatusMessages HotelCode="AL_TEST">
	      <AvailStatusMessage LocatorID="1">
	         <StatusApplicationControl Start="2017-02-27" End="2017-02-27" RatePlanCode="003" InvTypeCode="RO_TEST" />
	         <LengthsOfStay>
	            <LengthOfStay Time="3" MinMaxMessageType="SetMinLOS" />
	            <LengthOfStay Time="11" MinMaxMessageType="SetMaxLOS" />
	         </LengthsOfStay>
	         <RestrictionStatus Restriction="Arrival" Status="Open" />
	         <RestrictionStatus Restriction="Departure" Status="Open" />
	      </AvailStatusMessage>
	   </AvailStatusMessages>
	</OTA_HotelAvailNotifRQ>
```

```shell
curl
  -X POST
  -H "Content-Type: text/xml"
  -H "Authorization: Basic TEStTesttEsTt3st"
  -H "Cache-Control: no-cache"
  -d '<OTA_HotelAvailNotifRQ xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-26T10:32:51" Target="Production">
	   <AvailStatusMessages HotelCode="AL_TEST">
	      <AvailStatusMessage LocatorID="1">
	         <StatusApplicationControl Start="2017-02-27" End="2017-02-27" RatePlanCode="003" InvTypeCode="RO_TEST" />
	         <LengthsOfStay>
	            <LengthOfStay Time="3" MinMaxMessageType="SetMinLOS" />
	            <LengthOfStay Time="11" MinMaxMessageType="SetMaxLOS" />
	         </LengthsOfStay>
	         <RestrictionStatus Restriction="Arrival" Status="Open" />
	         <RestrictionStatus Restriction="Departure" Status="Open" />
	      </AvailStatusMessage>
	   </AvailStatusMessages>
	</OTA_HotelAvailNotifRQ>' "http://qualification-ari.weekendesk.com/ari/"
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

$request->setBody('<OTA_HotelAvailNotifRQ xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-26T10:32:51" Target="Production">
	   <AvailStatusMessages HotelCode="AL_TEST">
	      <AvailStatusMessage LocatorID="1">
	         <StatusApplicationControl Start="2017-02-27" End="2017-02-27" RatePlanCode="003" InvTypeCode="RO_TEST" />
	         <LengthsOfStay>
	            <LengthOfStay Time="3" MinMaxMessageType="SetMinLOS" />
	            <LengthOfStay Time="11" MinMaxMessageType="SetMaxLOS" />
	         </LengthsOfStay>
	         <RestrictionStatus Restriction="Arrival" Status="Open" />
	         <RestrictionStatus Restriction="Departure" Status="Open" />
	      </AvailStatusMessage>
	   </AvailStatusMessages>
	</OTA_HotelAvailNotifRQ>');

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
request.AddParameter("text/xml", "<OTA_HotelAvailNotifRQ xmlns=\"http://www.opentravel.org/OTA/2003/05\" xmlns:xsd=\"http://www.w3.org/2001/XMLSchema\" xmlns:xsi=\"http://www.w3.org/2001/XMLSchema-instance\" Version=\"1\" TimeStamp=\"2016-02-26T10:32:51\" Target=\"Production\">\n\t   <AvailStatusMessages HotelCode=\"AL_TEST\">\n\t      <AvailStatusMessage LocatorID=\"1\">\n\t         <StatusApplicationControl Start=\"2017-02-27\" End=\"2017-02-27\" RatePlanCode=\"003\" InvTypeCode=\"RO_TEST\" />\n\t         <LengthsOfStay>\n\t            <LengthOfStay Time=\"3\" MinMaxMessageType=\"SetMinLOS\" />\n\t            <LengthOfStay Time=\"11\" MinMaxMessageType=\"SetMaxLOS\" />\n\t         </LengthsOfStay>\n\t         <RestrictionStatus Restriction=\"Arrival\" Status=\"Open\" />\n\t         <RestrictionStatus Restriction=\"Departure\" Status=\"Open\" />\n\t      </AvailStatusMessage>\n\t   </AvailStatusMessages>\n\t</OTA_HotelAvailNotifRQ>", ParameterType.RequestBody);
IRestResponse response = client.Execute(request);
```

```java
OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("text/xml");
RequestBody body = RequestBody.create(mediaType, "<OTA_HotelAvailNotifRQ xmlns=\"http://www.opentravel.org/OTA/2003/05\" xmlns:xsd=\"http://www.w3.org/2001/XMLSchema\" xmlns:xsi=\"http://www.w3.org/2001/XMLSchema-instance\" Version=\"1\" TimeStamp=\"2016-02-26T10:32:51\" Target=\"Production\">\n\t   <AvailStatusMessages HotelCode=\"AL_TEST\">\n\t      <AvailStatusMessage LocatorID=\"1\">\n\t         <StatusApplicationControl Start=\"2017-02-27\" End=\"2017-02-27\" RatePlanCode=\"003\" InvTypeCode=\"RO_TEST\" />\n\t         <LengthsOfStay>\n\t            <LengthOfStay Time=\"3\" MinMaxMessageType=\"SetMinLOS\" />\n\t            <LengthOfStay Time=\"11\" MinMaxMessageType=\"SetMaxLOS\" />\n\t         </LengthsOfStay>\n\t         <RestrictionStatus Restriction=\"Arrival\" Status=\"Open\" />\n\t         <RestrictionStatus Restriction=\"Departure\" Status=\"Open\" />\n\t      </AvailStatusMessage>\n\t   </AvailStatusMessages>\n\t</OTA_HotelAvailNotifRQ>");
Request request = new Request.Builder()
  .url("http://qualification-ari.weekendesk.com/ari/")
  .post(body)
  .addHeader("authorization", "Basic TEStTesttEsTt3st")
  .addHeader("content-type", "text/xml")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();
```

> The above command returns XML structured like this:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelAvailNotifRS xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-24T23:00:26" Target="Production">
    <Success />
</OTA_HotelAvailNotifRS>
```

```shell
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelAvailNotifRS xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-24T23:00:26" Target="Production">
    <Success />
</OTA_HotelAvailNotifRS>
```

```php
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelAvailNotifRS xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-24T23:00:26" Target="Production">
    <Success />
</OTA_HotelAvailNotifRS>
```

```csharp
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelAvailNotifRS xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-24T23:00:26" Target="Production">
    <Success />
</OTA_HotelAvailNotifRS>
```

```java
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelAvailNotifRS xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-24T23:00:26" Target="Production">
    <Success />
</OTA_HotelAvailNotifRS>
```


### HTTP Request

Testing:<br>
`POST` http://qualification-ari.weekendesk.com/ari/
<br>Production:<br>
`POST` http://ari.weekendesk.com/ari/

### Query Parameters

Element | Type | Required | Description
--------- | ------- | ----- |-----------
HotelCode | string | Yes | The Weekendesk Hotel ID
LocatorID | integer | Yes | Progressive number based on the number of AvailStatusMessage tag within the same request
Start | date | Yes | Starting date of the updated period
End | date | Yes | Ending date of the updated period
RatePlanCode | string | Yes | Code of the Rate Plan for which the update is sent
InvTypeCode | string | Yes | Code of the Room for which the update is sent
Type | integer | Yes | Defines the Minimum (or Maximum) number of days of Stay required for a specific Rate Plan
MinMaxMessageType | string | Yes | SetMinLOS=updates the MinStay restriction; SetMaxLOS=updates the MaxStay restriction
Restriction | string | Yes | Arrival=updates the CTA restriction; Departure=updates the CTD restriction
Status | string | Yes | Open=The CheckIn (or CheckOut) is available for a specific RatePlan; Close=The CheckIn (or CheckOut) is NOT available for a specific RatePlan

##Update Inventory

Weekendesk supports the Inventory at the RatePlan level. The Channel partner can either send the same value for all RatePlans or a different value for each RatePlan.
<br>
If the Channel partner handles the stock at the room level, is supposed to send the same value for all RatePlans, and update the same for all RatePlans when receiving a new reservation.

```xml
HEADER
Authorization: Basic TEStTesttEsTt3st
Content-Type: text/xml

BODY
<OTA_HotelAvailNotifRQ xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-26T10:32:51" Target="Production">
	   <AvailStatusMessages HotelCode="AL_TEST">
	      <AvailStatusMessage BookingLimit="10" LocatorID="1">
	         <StatusApplicationControl Start="2017-02-27" End="2017-02-27" RatePlanCode="003" InvTypeCode="RO_TEST" />
	      </AvailStatusMessage>
	   </AvailStatusMessages>
	</OTA_HotelAvailNotifRQ>
```

```shell
curl
-X POST
-H "Authorization: Basic TEStTesttEsTt3st"
-H "Content-Type: text/xml"
-H "Cache-Control: no-cache"
-d '<OTA_HotelAvailNotifRQ xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-26T10:32:51" Target="Production">
	   <AvailStatusMessages HotelCode="AL_TEST">
	      <AvailStatusMessage BookingLimit="10" LocatorID="1">
	         <StatusApplicationControl Start="2017-02-27" End="2017-02-27" RatePlanCode="003" InvTypeCode="RO_TEST" />
	      </AvailStatusMessage>
	   </AvailStatusMessages>
	</OTA_HotelAvailNotifRQ>' "http://qualification-ari.weekendesk.com/ari/"
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

$request->setBody('<OTA_HotelAvailNotifRQ xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-26T10:32:51" Target="Production">
	   <AvailStatusMessages HotelCode="AL_TEST">
	      <AvailStatusMessage BookingLimit="10" LocatorID="1">
	         <StatusApplicationControl Start="2017-02-27" End="2017-02-27" RatePlanCode="003" InvTypeCode="RO_TEST" />
	      </AvailStatusMessage>
	   </AvailStatusMessages>
	</OTA_HotelAvailNotifRQ>');

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
request.AddHeader("content-type", "text/xml");
request.AddHeader("authorization", "Basic TEStTesttEsTt3st");
request.AddParameter("text/xml", "<OTA_HotelAvailNotifRQ xmlns=\"http://www.opentravel.org/OTA/2003/05\" xmlns:xsd=\"http://www.w3.org/2001/XMLSchema\" xmlns:xsi=\"http://www.w3.org/2001/XMLSchema-instance\" Version=\"1\" TimeStamp=\"2016-02-26T10:32:51\" Target=\"Production\">\n\t   <AvailStatusMessages HotelCode=\"AL_TEST\">\n\t      <AvailStatusMessage BookingLimit=\"10\" LocatorID=\"1\">\n\t         <StatusApplicationControl Start=\"2017-02-27\" End=\"2017-02-27\" RatePlanCode=\"003\" InvTypeCode=\"RO_TEST\" />\n\t      </AvailStatusMessage>\n\t   </AvailStatusMessages>\n\t</OTA_HotelAvailNotifRQ>", ParameterType.RequestBody);
IRestResponse response = client.Execute(request);
```

```java
OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("text/xml");
RequestBody body = RequestBody.create(mediaType, "<OTA_HotelAvailNotifRQ xmlns=\"http://www.opentravel.org/OTA/2003/05\" xmlns:xsd=\"http://www.w3.org/2001/XMLSchema\" xmlns:xsi=\"http://www.w3.org/2001/XMLSchema-instance\" Version=\"1\" TimeStamp=\"2016-02-26T10:32:51\" Target=\"Production\">\n\t   <AvailStatusMessages HotelCode=\"AL_TEST\">\n\t      <AvailStatusMessage BookingLimit=\"10\" LocatorID=\"1\">\n\t         <StatusApplicationControl Start=\"2017-02-27\" End=\"2017-02-27\" RatePlanCode=\"003\" InvTypeCode=\"RO_TEST\" />\n\t      </AvailStatusMessage>\n\t   </AvailStatusMessages>\n\t</OTA_HotelAvailNotifRQ>");
Request request = new Request.Builder()
  .url("http://qualification-ari.weekendesk.com/ari/")
  .post(body)
  .addHeader("authorization", "Basic TEStTesttEsTt3st")
  .addHeader("content-type", "text/xml")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();
```

> The above command returns XML structured like this:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelAvailNotifRS xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-26T10:32:51" Target="Production">
    <Success />
</OTA_HotelAvailNotifRS>
```

```shell
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelAvailNotifRS xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-26T10:32:51" Target="Production">
    <Success />
</OTA_HotelAvailNotifRS>
```

```php
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelAvailNotifRS xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-26T10:32:51" Target="Production">
    <Success />
</OTA_HotelAvailNotifRS>
```

```csharp
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelAvailNotifRS xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-26T10:32:51" Target="Production">
    <Success />
</OTA_HotelAvailNotifRS>
```

```java
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelAvailNotifRS xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-26T10:32:51" Target="Production">
    <Success />
</OTA_HotelAvailNotifRS>
```

### HTTP Request

Testing:<br>
`POST` http://qualification-ari.weekendesk.com/ari/
<br>Production:<br>
`POST` http://ari.weekendesk.com/ari/

### Query Parameters

Element | Type | Required | Description
--------- | ------- | ----- |-----------
HotelCode | string | Yes | The Weekendesk Hotel ID
LocatorID | integer | Yes | Progressive number based on the number of AvailStatusMessage tag within the same request
Start | date | Yes | Starting date of the updated period
End | date | Yes | Ending date of the updated period
RatePlanCode | string | Yes | Code of the Rate Plan for which the update is sent
InvTypeCode | string | Yes | Code of the Room for which the update is sent
BookingLimit | integer | Yes | Defines the number of Stock available for a specific Rate Plan

##Multiple Updates
(Availability, Restrictions, Inventory)

The Channel partner can combine multiple updates of availability, restrictions and inventory in the same request, for different room and rates, specifying each section with a <b>different LocatorID</b>s.
<br>
<aside class="warning">We highly recommend to use multiple updates only for short periods in order to avoid service disruptions. Weekendesk reserves the right of restricting the number of requests per minute if noticed a high usage of this type of request</aside>

```xml
HEADER
Authorization: Basic TEStTesttEsTt3st
Content-Type: text/xml

BODY
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelAvailNotifRQ xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-26T10:32:51" Target="Production">
   <AvailStatusMessages HotelCode="AL_TEST">
      <AvailStatusMessage BookingLimit="29" LocatorID="1">
         <StatusApplicationControl Start="2017-04-23" End="2017-04-30" RatePlanCode="001" InvTypeCode="RO_TEST" />
         <LengthsOfStay>
            <LengthOfStay Time="2" MinMaxMessageType="SetMinLOS" />
            <LengthOfStay Time="99" MinMaxMessageType="SetMaxLOS" />
         </LengthsOfStay>
         <RestrictionStatus Status="Close" />
         <RestrictionStatus Restriction="Arrival" Status="Open" />
         <RestrictionStatus Restriction="Departure" Status="Open" />
      </AvailStatusMessage>
      <AvailStatusMessage BookingLimit="20" LocatorID="2">
         <StatusApplicationControl Start="2017-03-23" End="2017-03-30" RatePlanCode="003" InvTypeCode="RO_TEST" />
         <LengthsOfStay>
            <LengthOfStay Time="1" MinMaxMessageType="SetMinLOS" />
            <LengthOfStay Time="99" MinMaxMessageType="SetMaxLOS" />
         </LengthsOfStay>
         <RestrictionStatus Status="Open" />
         <RestrictionStatus Restriction="Arrival" Status="Open" />
         <RestrictionStatus Restriction="Departure" Status="Close" />
      </AvailStatusMessage>
      <AvailStatusMessage BookingLimit="20" LocatorID="3">
         <StatusApplicationControl Start="2017-03-23" End="2017-03-30" RatePlanCode="003" InvTypeCode="RO_TEST2" />
         <LengthsOfStay>
            <LengthOfStay Time="3" MinMaxMessageType="SetMinLOS" />
            <LengthOfStay Time="99" MinMaxMessageType="SetMaxLOS" />
         </LengthsOfStay>
         <RestrictionStatus Status="Open" />
         <RestrictionStatus Restriction="Arrival" Status="Open" />
         <RestrictionStatus Restriction="Departure" Status="Open" />
      </AvailStatusMessage>
   </AvailStatusMessages>
</OTA_HotelAvailNotifRQ>
```

```shell
curl
-X POST
-H "Authorization: Basic TEStTesttEsTt3st"
-H "Content-Type: text/xml"
-H "Cache-Control: no-cache"
-d '<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelAvailNotifRQ xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-26T10:32:51" Target="Production">
   <AvailStatusMessages HotelCode="AL_TEST">
      <AvailStatusMessage BookingLimit="29" LocatorID="1">
         <StatusApplicationControl Start="2017-04-23" End="2017-04-30" RatePlanCode="001" InvTypeCode="RO_TEST" />
         <LengthsOfStay>
            <LengthOfStay Time="2" MinMaxMessageType="SetMinLOS" />
            <LengthOfStay Time="99" MinMaxMessageType="SetMaxLOS" />
         </LengthsOfStay>
         <RestrictionStatus Status="Close" />
         <RestrictionStatus Restriction="Arrival" Status="Open" />
         <RestrictionStatus Restriction="Departure" Status="Open" />
      </AvailStatusMessage>
      <AvailStatusMessage BookingLimit="20" LocatorID="2">
         <StatusApplicationControl Start="2017-03-23" End="2017-03-30" RatePlanCode="003" InvTypeCode="RO_TEST" />
         <LengthsOfStay>
            <LengthOfStay Time="1" MinMaxMessageType="SetMinLOS" />
            <LengthOfStay Time="99" MinMaxMessageType="SetMaxLOS" />
         </LengthsOfStay>
         <RestrictionStatus Status="Open" />
         <RestrictionStatus Restriction="Arrival" Status="Open" />
         <RestrictionStatus Restriction="Departure" Status="Close" />
      </AvailStatusMessage>
      <AvailStatusMessage BookingLimit="20" LocatorID="3">
         <StatusApplicationControl Start="2017-03-23" End="2017-03-30" RatePlanCode="003" InvTypeCode="RO_TEST2" />
         <LengthsOfStay>
            <LengthOfStay Time="3" MinMaxMessageType="SetMinLOS" />
            <LengthOfStay Time="99" MinMaxMessageType="SetMaxLOS" />
         </LengthsOfStay>
         <RestrictionStatus Status="Open" />
         <RestrictionStatus Restriction="Arrival" Status="Open" />
         <RestrictionStatus Restriction="Departure" Status="Open" />
      </AvailStatusMessage>
   </AvailStatusMessages>
</OTA_HotelAvailNotifRQ>' "http://qualification-ari.weekendesk.com/ari/"
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
<OTA_HotelAvailNotifRQ xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-26T10:32:51" Target="Production">
   <AvailStatusMessages HotelCode="AL_TEST">
      <AvailStatusMessage BookingLimit="29" LocatorID="1">
         <StatusApplicationControl Start="2017-04-23" End="2017-04-30" RatePlanCode="001" InvTypeCode="RO_TEST" />
         <LengthsOfStay>
            <LengthOfStay Time="2" MinMaxMessageType="SetMinLOS" />
            <LengthOfStay Time="99" MinMaxMessageType="SetMaxLOS" />
         </LengthsOfStay>
         <RestrictionStatus Status="Close" />
         <RestrictionStatus Restriction="Arrival" Status="Open" />
         <RestrictionStatus Restriction="Departure" Status="Open" />
      </AvailStatusMessage>
      <AvailStatusMessage BookingLimit="20" LocatorID="2">
         <StatusApplicationControl Start="2017-03-23" End="2017-03-30" RatePlanCode="003" InvTypeCode="RO_TEST" />
         <LengthsOfStay>
            <LengthOfStay Time="1" MinMaxMessageType="SetMinLOS" />
            <LengthOfStay Time="99" MinMaxMessageType="SetMaxLOS" />
         </LengthsOfStay>
         <RestrictionStatus Status="Open" />
         <RestrictionStatus Restriction="Arrival" Status="Open" />
         <RestrictionStatus Restriction="Departure" Status="Close" />
      </AvailStatusMessage>
      <AvailStatusMessage BookingLimit="20" LocatorID="3">
         <StatusApplicationControl Start="2017-03-23" End="2017-03-30" RatePlanCode="003" InvTypeCode="RO_TEST2" />
         <LengthsOfStay>
            <LengthOfStay Time="3" MinMaxMessageType="SetMinLOS" />
            <LengthOfStay Time="99" MinMaxMessageType="SetMaxLOS" />
         </LengthsOfStay>
         <RestrictionStatus Status="Open" />
         <RestrictionStatus Restriction="Arrival" Status="Open" />
         <RestrictionStatus Restriction="Departure" Status="Open" />
      </AvailStatusMessage>
   </AvailStatusMessages>
</OTA_HotelAvailNotifRQ>');

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
request.AddHeader("content-type", "text/xml");
request.AddHeader("authorization", "Basic TEStTesttEsTt3st");
request.AddParameter("text/xml", "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\r\n<OTA_HotelAvailNotifRQ xmlns=\"http://www.opentravel.org/OTA/2003/05\" xmlns:xsd=\"http://www.w3.org/2001/XMLSchema\" xmlns:xsi=\"http://www.w3.org/2001/XMLSchema-instance\" Version=\"1\" TimeStamp=\"2016-02-26T10:32:51\" Target=\"Production\">\r\n   <AvailStatusMessages HotelCode=\"AL_TEST\">\r\n      <AvailStatusMessage BookingLimit=\"29\" LocatorID=\"1\">\r\n         <StatusApplicationControl Start=\"2017-04-23\" End=\"2017-04-30\" RatePlanCode=\"001\" InvTypeCode=\"RO_TEST\" />\r\n         <LengthsOfStay>\r\n            <LengthOfStay Time=\"2\" MinMaxMessageType=\"SetMinLOS\" />\r\n            <LengthOfStay Time=\"99\" MinMaxMessageType=\"SetMaxLOS\" />\r\n         </LengthsOfStay>\r\n         <RestrictionStatus Status=\"Close\" />\r\n         <RestrictionStatus Restriction=\"Arrival\" Status=\"Open\" />\r\n         <RestrictionStatus Restriction=\"Departure\" Status=\"Open\" />\r\n      </AvailStatusMessage>\r\n      <AvailStatusMessage BookingLimit=\"20\" LocatorID=\"2\">\r\n         <StatusApplicationControl Start=\"2017-03-23\" End=\"2017-03-30\" RatePlanCode=\"003\" InvTypeCode=\"RO_TEST\" />\r\n         <LengthsOfStay>\r\n            <LengthOfStay Time=\"1\" MinMaxMessageType=\"SetMinLOS\" />\r\n            <LengthOfStay Time=\"99\" MinMaxMessageType=\"SetMaxLOS\" />\r\n         </LengthsOfStay>\r\n         <RestrictionStatus Status=\"Open\" />\r\n         <RestrictionStatus Restriction=\"Arrival\" Status=\"Open\" />\r\n         <RestrictionStatus Restriction=\"Departure\" Status=\"Close\" />\r\n      </AvailStatusMessage>\r\n      <AvailStatusMessage BookingLimit=\"20\" LocatorID=\"3\">\r\n         <StatusApplicationControl Start=\"2017-03-23\" End=\"2017-03-30\" RatePlanCode=\"003\" InvTypeCode=\"RO_TEST2\" />\r\n         <LengthsOfStay>\r\n            <LengthOfStay Time=\"3\" MinMaxMessageType=\"SetMinLOS\" />\r\n            <LengthOfStay Time=\"99\" MinMaxMessageType=\"SetMaxLOS\" />\r\n         </LengthsOfStay>\r\n         <RestrictionStatus Status=\"Open\" />\r\n         <RestrictionStatus Restriction=\"Arrival\" Status=\"Open\" />\r\n         <RestrictionStatus Restriction=\"Departure\" Status=\"Open\" />\r\n      </AvailStatusMessage>\r\n   </AvailStatusMessages>\r\n</OTA_HotelAvailNotifRQ>", ParameterType.RequestBody);
IRestResponse response = client.Execute(request);
```

```java
OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("text/xml");
RequestBody body = RequestBody.create(mediaType, "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\r\n<OTA_HotelAvailNotifRQ xmlns=\"http://www.opentravel.org/OTA/2003/05\" xmlns:xsd=\"http://www.w3.org/2001/XMLSchema\" xmlns:xsi=\"http://www.w3.org/2001/XMLSchema-instance\" Version=\"1\" TimeStamp=\"2016-02-26T10:32:51\" Target=\"Production\">\r\n   <AvailStatusMessages HotelCode=\"AL_TEST\">\r\n      <AvailStatusMessage BookingLimit=\"29\" LocatorID=\"1\">\r\n         <StatusApplicationControl Start=\"2017-04-23\" End=\"2017-04-30\" RatePlanCode=\"001\" InvTypeCode=\"RO_TEST\" />\r\n         <LengthsOfStay>\r\n            <LengthOfStay Time=\"2\" MinMaxMessageType=\"SetMinLOS\" />\r\n            <LengthOfStay Time=\"99\" MinMaxMessageType=\"SetMaxLOS\" />\r\n         </LengthsOfStay>\r\n         <RestrictionStatus Status=\"Close\" />\r\n         <RestrictionStatus Restriction=\"Arrival\" Status=\"Open\" />\r\n         <RestrictionStatus Restriction=\"Departure\" Status=\"Open\" />\r\n      </AvailStatusMessage>\r\n      <AvailStatusMessage BookingLimit=\"20\" LocatorID=\"2\">\r\n         <StatusApplicationControl Start=\"2017-03-23\" End=\"2017-03-30\" RatePlanCode=\"003\" InvTypeCode=\"RO_TEST\" />\r\n         <LengthsOfStay>\r\n            <LengthOfStay Time=\"1\" MinMaxMessageType=\"SetMinLOS\" />\r\n            <LengthOfStay Time=\"99\" MinMaxMessageType=\"SetMaxLOS\" />\r\n         </LengthsOfStay>\r\n         <RestrictionStatus Status=\"Open\" />\r\n         <RestrictionStatus Restriction=\"Arrival\" Status=\"Open\" />\r\n         <RestrictionStatus Restriction=\"Departure\" Status=\"Close\" />\r\n      </AvailStatusMessage>\r\n      <AvailStatusMessage BookingLimit=\"20\" LocatorID=\"3\">\r\n         <StatusApplicationControl Start=\"2017-03-23\" End=\"2017-03-30\" RatePlanCode=\"003\" InvTypeCode=\"RO_TEST2\" />\r\n         <LengthsOfStay>\r\n            <LengthOfStay Time=\"3\" MinMaxMessageType=\"SetMinLOS\" />\r\n            <LengthOfStay Time=\"99\" MinMaxMessageType=\"SetMaxLOS\" />\r\n         </LengthsOfStay>\r\n         <RestrictionStatus Status=\"Open\" />\r\n         <RestrictionStatus Restriction=\"Arrival\" Status=\"Open\" />\r\n         <RestrictionStatus Restriction=\"Departure\" Status=\"Open\" />\r\n      </AvailStatusMessage>\r\n   </AvailStatusMessages>\r\n</OTA_HotelAvailNotifRQ>");
Request request = new Request.Builder()
  .url("http://qualification-ari.weekendesk.com/ari/")
  .post(body)
  .addHeader("authorization", "Basic TEStTesttEsTt3st")
  .addHeader("content-type", "text/xml")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();
```

> The above command returns XML structured like this:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelAvailNotifRS xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-26T10:32:51" Target="Production">
    <Success />
</OTA_HotelAvailNotifRS>
```

```shell
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelAvailNotifRS xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-26T10:32:51" Target="Production">
    <Success />
</OTA_HotelAvailNotifRS>
```

```php
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelAvailNotifRS xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-26T10:32:51" Target="Production">
    <Success />
</OTA_HotelAvailNotifRS>
```

```csharp
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelAvailNotifRS xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-26T10:32:51" Target="Production">
    <Success />
</OTA_HotelAvailNotifRS>
```

```java
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelAvailNotifRS xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-26T10:32:51" Target="Production">
    <Success />
</OTA_HotelAvailNotifRS>
```

### HTTP Request

Testing:<br>
`POST` http://qualification-ari.weekendesk.com/ari/
<br>Production:<br>
`POST` http://ari.weekendesk.com/ari/

### Query Parameters

Element | Type | Required | Description
--------- | ------- | ----- |-----------
HotelCode | string | Yes | The Weekendesk Hotel ID
LocatorID | integer | Yes | Progressive number based on the number of AvailStatusMessage tag within the same request
Start | date | Yes | Starting date of the updated period
End | date | Yes | Ending date of the updated period
RatePlanCode | string | Yes | Code of the Rate Plan for which the update is sent
InvTypeCode | string | Yes | Code of the Room for which the update is sent
BookingLimit | integer | Yes | Defines the number of Stock available for a specific Rate Plan
Status | string | Yes | Open=The rate plan is sellable; Close=Stop sale
Type | integer | Yes | Defines the Minimum (or Maximum) number of days of Stay required for a specific Rate Plan
MinMaxMessageType | string | Yes | SetMinLOS=updates the MinStay restriction; SetMaxLOS=updates the MaxStay restriction
Restriction | string | Yes | Arrival=updates the CTA restriction; Departure=updates the CTD restriction
Status | string | Yes | Open=The CheckIn (or CheckOut) is available for a specific RatePlan; Close=The CheckIn (or CheckOut) is NOT available for a specific RatePlan


##Update Rates

In order to update Rates of a specific RatePlan use the following OTA request:
<br>
OTA_HotelRateAmountNotifRQ
<br>
The update can be sent for a specific RatePlanCode of a Hotel Room for a specific period.

```xml
HEADER
Authorization: Basic TEStTesttEsTt3st
Content-Type: text/xml

BODY
<OTA_HotelRateAmountNotifRQ xmlns="http://www.opentravel.org/OTA/2003/05"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    Version="1" TimeStamp="2013-07-19T10:03:09" Target="Production">
    <RateAmountMessages HotelCode="AL_TEST">
        <RateAmountMessage LocatorID="1">
            <StatusApplicationControl Start="2017-02-23" End="2017-03-30" RatePlanCode="001" InvTypeCode="RO_TEST" />
            <Rates>
                <Rate>
                    <BaseByGuestAmts>
                        <BaseByGuestAmt AmountAfterTax="200.00" DecimalPlaces="0" />
                    </BaseByGuestAmts>
                </Rate>
            </Rates>
        </RateAmountMessage>
        <RateAmountMessage LocatorID="2">
            <StatusApplicationControl Start="2017-02-20" End="2017-03-30" RatePlanCode="003" InvTypeCode="RO_TEST" />
            <Rates>
                <Rate>
                    <BaseByGuestAmts>
                        <BaseByGuestAmt AmountAfterTax="100.00" DecimalPlaces="0" />
                    </BaseByGuestAmts>
                </Rate>
            </Rates>
        </RateAmountMessage>
    </RateAmountMessages>
</OTA_HotelRateAmountNotifRQ>
```

```shell
curl
-X POST
-H "Authorization: Basic TEStTesttEsTt3st"
-H "Content-Type: text/xml"
-H "Cache-Control: no-cache"
-d '<OTA_HotelRateAmountNotifRQ xmlns="http://www.opentravel.org/OTA/2003/05"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    Version="1" TimeStamp="2013-07-19T10:03:09" Target="Production">
    <RateAmountMessages HotelCode="AL_TEST">
        <RateAmountMessage LocatorID="1">
            <StatusApplicationControl Start="2017-02-23" End="2017-03-30" RatePlanCode="001" InvTypeCode="RO_TEST" />
            <Rates>
                <Rate>
                    <BaseByGuestAmts>
                        <BaseByGuestAmt AmountAfterTax="200.00" DecimalPlaces="0" />
                    </BaseByGuestAmts>
                </Rate>
            </Rates>
        </RateAmountMessage>
        <RateAmountMessage LocatorID="2">
            <StatusApplicationControl Start="2017-02-20" End="2017-03-30" RatePlanCode="003" InvTypeCode="RO_TEST" />
            <Rates>
                <Rate>
                    <BaseByGuestAmts>
                        <BaseByGuestAmt AmountAfterTax="100.00" DecimalPlaces="0" />
                    </BaseByGuestAmts>
                </Rate>
            </Rates>
        </RateAmountMessage>
    </RateAmountMessages>
</OTA_HotelRateAmountNotifRQ>' "http://qualification-ari.weekendesk.com/ari/"
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

$request->setBody('<OTA_HotelRateAmountNotifRQ xmlns="http://www.opentravel.org/OTA/2003/05"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    Version="1" TimeStamp="2013-07-19T10:03:09" Target="Production">
    <RateAmountMessages HotelCode="AL_TEST">
        <RateAmountMessage LocatorID="1">
            <StatusApplicationControl Start="2017-02-23" End="2017-03-30" RatePlanCode="001" InvTypeCode="RO_TEST" />
            <Rates>
                <Rate>
                    <BaseByGuestAmts>
                        <BaseByGuestAmt AmountAfterTax="200.00" DecimalPlaces="0" />
                    </BaseByGuestAmts>
                </Rate>
            </Rates>
        </RateAmountMessage>
        <RateAmountMessage LocatorID="2">
            <StatusApplicationControl Start="2017-02-20" End="2017-03-30" RatePlanCode="003" InvTypeCode="RO_TEST" />
            <Rates>
                <Rate>
                    <BaseByGuestAmts>
                        <BaseByGuestAmt AmountAfterTax="100.00" DecimalPlaces="0" />
                    </BaseByGuestAmts>
                </Rate>
            </Rates>
        </RateAmountMessage>
    </RateAmountMessages>
</OTA_HotelRateAmountNotifRQ>');

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
request.AddHeader("content-type", "text/xml");
request.AddHeader("authorization", "Basic TEStTesttEsTt3st");
request.AddParameter("text/xml", "<OTA_HotelRateAmountNotifRQ xmlns=\"http://www.opentravel.org/OTA/2003/05\"\n    xmlns:xsi=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns:xsd=\"http://www.w3.org/2001/XMLSchema\"\n    Version=\"1\" TimeStamp=\"2013-07-19T10:03:09\" Target=\"Production\">\n    <RateAmountMessages HotelCode=\"AL_TEST\">\n        <RateAmountMessage LocatorID=\"1\">\n            <StatusApplicationControl Start=\"2017-02-23\" End=\"2017-03-30\" RatePlanCode=\"001\" InvTypeCode=\"RO_TEST\" />\n            <Rates>\n                <Rate>\n                    <BaseByGuestAmts>\n                        <BaseByGuestAmt AmountAfterTax=\"200.00\" DecimalPlaces=\"0\" />\n                    </BaseByGuestAmts>\n                </Rate>\n            </Rates>\n        </RateAmountMessage>\n        <RateAmountMessage LocatorID=\"2\">\n            <StatusApplicationControl Start=\"2017-02-20\" End=\"2017-03-30\" RatePlanCode=\"003\" InvTypeCode=\"RO_TEST\" />\n            <Rates>\n                <Rate>\n                    <BaseByGuestAmts>\n                        <BaseByGuestAmt AmountAfterTax=\"100.00\" DecimalPlaces=\"0\" />\n                    </BaseByGuestAmts>\n                </Rate>\n            </Rates>\n        </RateAmountMessage>\n    </RateAmountMessages>\n</OTA_HotelRateAmountNotifRQ>", ParameterType.RequestBody);
IRestResponse response = client.Execute(request);
```

```java
OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("text/xml");
RequestBody body = RequestBody.create(mediaType, "<OTA_HotelRateAmountNotifRQ xmlns=\"http://www.opentravel.org/OTA/2003/05\"\n    xmlns:xsi=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns:xsd=\"http://www.w3.org/2001/XMLSchema\"\n    Version=\"1\" TimeStamp=\"2013-07-19T10:03:09\" Target=\"Production\">\n    <RateAmountMessages HotelCode=\"AL_TEST\">\n        <RateAmountMessage LocatorID=\"1\">\n            <StatusApplicationControl Start=\"2017-02-23\" End=\"2017-03-30\" RatePlanCode=\"001\" InvTypeCode=\"RO_TEST\" />\n            <Rates>\n                <Rate>\n                    <BaseByGuestAmts>\n                        <BaseByGuestAmt AmountAfterTax=\"200.00\" DecimalPlaces=\"0\" />\n                    </BaseByGuestAmts>\n                </Rate>\n            </Rates>\n        </RateAmountMessage>\n        <RateAmountMessage LocatorID=\"2\">\n            <StatusApplicationControl Start=\"2017-02-20\" End=\"2017-03-30\" RatePlanCode=\"003\" InvTypeCode=\"RO_TEST\" />\n            <Rates>\n                <Rate>\n                    <BaseByGuestAmts>\n                        <BaseByGuestAmt AmountAfterTax=\"100.00\" DecimalPlaces=\"0\" />\n                    </BaseByGuestAmts>\n                </Rate>\n            </Rates>\n        </RateAmountMessage>\n    </RateAmountMessages>\n</OTA_HotelRateAmountNotifRQ>");
Request request = new Request.Builder()
  .url("http://qualification-ari.weekendesk.com/ari/")
  .post(body)
  .addHeader("authorization", "Basic TEStTesttEsTt3st")
  .addHeader("content-type", "text/xml")
  .addHeader("cache-control", "no-cache")
  .build();

Response response = client.newCall(request).execute();
```

> The above command returns XML structured like this:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelRateAmountNotifRS xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-24T23:00:26" Target="Production">
    <Success />
</OTA_HotelRateAmountNotifRS>
```

```shell
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelRateAmountNotifRS xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-24T23:00:26" Target="Production">
    <Success />
</OTA_HotelRateAmountNotifRS>
```

```php
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelRateAmountNotifRS xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-24T23:00:26" Target="Production">
    <Success />
</OTA_HotelRateAmountNotifRS>
```

```csharp
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelRateAmountNotifRS xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-24T23:00:26" Target="Production">
    <Success />
</OTA_HotelRateAmountNotifRS>
```

```java
<?xml version="1.0" encoding="UTF-8"?>
<OTA_HotelRateAmountNotifRS xmlns="http://www.opentravel.org/OTA/2003/05" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1" TimeStamp="2016-02-24T23:00:26" Target="Production">
    <Success />
</OTA_HotelRateAmountNotifRS>
```

### HTTP Request

Testing:<br>
`POST` http://qualification-ari.weekendesk.com/ari/
<br>Production:<br>
`POST` http://ari.weekendesk.com/ari/

### Query Parameters

Element | Type | Required | Description
--------- | ------- | ----- |-----------
HotelCode | string | Yes | The Weekendesk Hotel ID
LocatorID | integer | Yes | Progressive number based on the number of AvailStatusMessage tag within the same request
Start | date | Yes | Starting date of the updated period
End | date | Yes | Ending date of the updated period
RatePlanCode | string | Yes | Code of the Rate Plan for which the update is sent
InvTypeCode | string | Yes | Code of the Room for which the update is sent
AmountAfterTax | float | Yes | The price for the Rate of a specific Rate Plan
DecimalPlaces | integer | No | Number of decimal places used in the AmountAfterTax

#ARI Read (Beta Test)

Functionality under developement

#Reservation Delivery

The Reservation delivery allows the Channel partner to receive both Booking and Cancel Notifications real time via the web-service.<br>
Weekendesk Reservation API is based on XML messages based on the standards OTA (http://www.opentravel.org/)<br>
<br>
Specifically the following are used for notifying the Channel partner of a Booking or Cancellation:<br>
<br>
- OTA_HotelResRQ/RS<br>
- OTA_CancelRQ/RS<br>
<br>

## Receive Booking Notifications

Booking notifications allows the Channel partner to be acknowledged about a new booking that entered into the Weekendesk system.<br>
Once received the notification, and successfully confirmed, it is responsability of the Channel partner to notify the hotel in order to reserve the room for the stay.


### HTTP Request

Endpoint:<br>
`POST` https://channel-manager-endpoint.com

<aside class="success">
Remember - Before starting receiving Booking Notifications <b>send the Endpoint and Credentials (if any)</b> of both your testing and production environment to your technical contact at Weekendesk.
</aside>


### Query Parameters

Element | Type | Required | Description
--------- | ------- | ----- |-----------
ResStatus | string | Yes | Weekendesk only supports one value "Commit" to indicate that the reservation is confirmed
@RequestorID |  |  |
ID | integer | Yes | Progressive number based on the number of AvailStatusMessage tag within the same request
MessagePassword | date | Yes | Starting date of the updated period
@RoomType |  |  |
RoomTypeCode | date | Yes | Ending date of the updated period
RoomType | string | Yes | Code of the Rate Plan for which the update is sent
NumberOfUnits | string | Yes | Code of the Room for which the update is sent
@RatePlan |  |  |
RatePlanCode | integer | Yes | Defines the number of Stock available for a specific Rate Plan
RatePlanName | string | Yes | Open=The rate plan is sellable; Close=Stop sale
@Rate |  |  |
EffectiveDate | integer | Yes | Defines the Minimum (or Maximum) number of days of
ExpireDate | string | Yes | SetMinLOS=updat
@Base |  |  |
AmountAfterTax | string | Yes | Arrival=updates
CurrencyCode | string | Yes | Open=The CheckIn (or
@GuestCount |  |  |
AgeQualifyingCode | string | Yes | Arrival=updates
Count | string | Yes | Open=The CheckIn (or
ResGuestRPH | string | Yes | Arrival=updates
@TimeSpan |  |  |
Start | string | Yes | Open=The CheckIn (or
End | string | Yes | Arrival=updates
@BasicPropertyInfo |  |  |
BrandCode | string | Yes | Open=The CheckIn (or
HotelCode | string | Yes | Open=The CheckIn (or
HotelName | string | Yes | Open=The CheckIn (or
@Service |  |  |
Quantity | string | Yes | Open=The CheckIn (or
ServicePricingType | string | Yes | Open=The CheckIn (or
@TimeSpan |  |  |
Duration | string | Yes | Open=The CheckIn (or
@Description |  |  |
Language | string | Yes | Open=The CheckIn (or
Text | string | Yes | Open=The CheckIn (or
@Profile |  |  |
ProfileType | string | Yes | Open=The CheckIn (or
@PersonName |  |  |
NamePrefix | string | Yes | Open=The CheckIn (or
GivenName | string | Yes | Open=The CheckIn (or
Surname | string | Yes | Open=The CheckIn (or
@Telephone |  |  |
PhoneNumber | string | Yes | Open=The CheckIn (or
PhoneLocationType | string | Yes | Open=The CheckIn (or
PhoneTechType | string | Yes | Open=The CheckIn (or
Email | string | Yes | Open=The CheckIn (or
AddressLine | string | Yes | Open=The CheckIn (or
CityName | string | Yes | Open=The CheckIn (or
PostalCode | string | Yes | Open=The CheckIn (or
CountryName | string | Yes | Open=The CheckIn (or








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

## Receive Cancellation Notifications

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
