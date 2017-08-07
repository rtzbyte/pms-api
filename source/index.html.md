---
title: Booking Suite Developer Platform

language_tabs:
  - curl

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='#'>Footer 2 Test</a>

includes:
  - errors

search: true
---

# The Platform

## What is the platform?
The Developer's Platform is a collection of experimental API's released by the NPD team in Booking Suite.
## Why the platform?
under construction
## Who uses the platform?
under construction
# Test Environment

To start, you'll have the following development environments:

## Test PMS
- a test pms (to simulate a real property)
## Test Booking.com Account
- a test booking.com account (to simulate inventory on an OTA)
## Test MySQL account
- a test MySQL database (to view raw data in real-time)
## Authentication Keys
- authentication keys to a sandbox

> To authorize, use this code:

```curl
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here"
  -H "X-Switch-Token: yourtokengoeshere"
```

> Make sure to replace `yourtokengoeshere` with your API key.

# Inventory API's
The inventory API's allow you to synchronize inventory data with Booking.com

## Physical Rooms
insert content here

### What are the Retrieved Rooms API use for?

insert content here

Please ONLY return a list of 'Active' Room Code & Rate Code combinations in your Retrieved Rooms API.

### HTTP Request

`POST https://api.dev-bookingsuite.cm/api/room/fetch`

```shell
curl -k  -L -X POST -H 'X-Switch-Token: 8c6zfkwf4a1160ankv6r48rve' -H 'Content-Type: application/json' -d '{
  "property_id": "25"
}' 'https://api.dev-bookingsuite.cm/api/room/fetch'
```

> JSON RESPONSE:

```json
{
  "rooms": [
    {
      "name": "Master Suite",
      "id": "4",
      "room_type": "base",
      "base_price": "45.00",
      "labels": [
        {
          "id": "57",
          "name": "A"
        },
        {
          "id": "58",
          "name": "B"
        },
        {
          "id": "59",
          "name": "C"
        },
        {
          "id": "60",
          "name": "D"
        },
        {
          "id": "61",
          "name": "E"
        }
      ]
    },
    {
      "name": "Mixed Dorm",
      "id": "5",
      "room_type": "base",
      "base_price": "20.00",
      "labels": [
        {
          "id": "62",
          "name": "A-1"
        },
        {
          "id": "63",
          "name": "A-2"
        },
        {
          "id": "64",
          "name": "A-3"
        },
        {
          "id": "65",
          "name": "A-4"
        },
        {
          "id": "66",
          "name": "B-1"
        },
        {
          "id": "67",
          "name": "B-2"
        },
        {
          "id": "68",
          "name": "B-3"
        },
        {
          "id": "69",
          "name": "B-4"
        }
      ]
    }
  ]
}
```


### Parameters

Parameter | Description | Mandatory
--------- | ----------- | ---------
property_id | The ID of the property | *



## Virtual Rooms

There are two types of virtual rooms: dynamic & rate plans.

### HTTP Request

`POST https://api.dev-bookingsuite.cm/api/room/create`

```shell
curl -k  -L -X POST -H 'X-Switch-Token: 8c6zfkwf4a1160ankv6r48rve' -H 'Content-Type: application/json' -d '{
  "property_id": "25",
  "rooms": [
    {
      "name": "Mixed Dorm",
      "price": "200.00",
      "type": "base",
      "labels": [
        {
          "name": "room a"
        },
        {
          "name": "room b"
        }
      ]
    },
    {
      "name": "Female Dorm",
      "price": "300.00",
      "type": "base",
      "labels": [
        {
          "name": "room 1"
        }
      ]
    }
  ]
}' 'https://api.switch.cm/api/room/create'
```



### Parameters

Parameter | Description | Mandatory
--------- | ----------- | ---------
property_id | The ID of the property | *
rooms | Multiple rooms  | *
name | name of the room  | *

# Reservation API's

## Retrieve Reservation API

Retrieve a list of reservations for a property.


### HTTP Request

`POST https://api.dev-bookingsuite.cm/api/reservation`

```shell
curl -k  -L -X POST -H 'X-
-Token: 8c6zfkwf4a1160ankv6r48rve' -H 'Content-Type: application/json' -d '{
  "property_id": "25"
}' 'https://api.switch.cm/api/reservation'
```

> JSON RESPONSE:

```json
{
  "reservations": [
        {
          "booking_id": "101657667746",
          "booking_date": "2017-02-17",
          "organiser_name": "John Doe",
          "rooms": [
            {
              "id": "4567",
              "booking_date": "2017-02-17",
              "check_out": "2016-11-28",
              "status": "waiting for guest",
              "source": "5",
              "booked_room": "5120",
              "deleted_at": null,
              "created_at": "2017-02-14 11:18:20",
              "updated_at": "2017-02-14 11:18:20",
              "total": "225000.00",
              "deposit": "0.00",
              "total_payment": null,
              "cleaning_fee": "0.00",
              "commission": "33750.00",
              "service_fee": "0.00",
              "gross_revenue": "225000.00",
              "net_revenue": "191250.00",
              "remaining_balance": "225000.00",
              "auxiliaries": null,
              "sales_tax": "0.00",
              "tax_rate": "0.00",
              "guest": {
                "name": "John Doe",
                "email": "476115+bulk@switch.cm",
                "phone": "6478219711",
                "address": "90 queens drive",
                "city": "Toronto",
                "zip": "M6l3e2"
              },
              "nights": [
                {
                  "date": "2017-02-17",
                  "price": "149.00",
                  "assigned_room": "57"
                },
                {
                  "date": "2017-02-18",
                  "price": "275.00",
                  "assigned_room": "57"
                }
              ]
            }
          ]
        }
      ]
}
```


### Parameters

Parameter | Description | Mandatory
--------- | ----------- | ---------
property_id | The ID of the property | *
start | filter result with date from (yyyy-mm-dd) |
end | filter result date to (yyyy-mm-dd) |

### Notes

The search parameters will return all reservations with any days inside the date parameters.  For example, a search for April 3rd to April 5th will yield a reservation from April 1st to April 7th (even though the check-in & check-out dates fall outside of the search parameter dates).

## Create Reservation API

SWITCH.CM uses the Create Reservation API to receive reservations.

Sending a Create Reservation API call will generate a new reservation on the SWITCH.CM calendar.

### HTTP Request

`POST https://api.switch.cm/api/reservation/create`

```shell
curl -k  -L -X POST -H 'X--Token: 8c6zfkwf4a1160ankv6r48rve' -H 'Content-Type: application/json' -d '{
  "property_id": "25",
  "reservations": [
      {
        "booking_id": "101657667746",
        "booking_date": "2017-02-17",
        "organiser_name": "John Doe",
        "rooms": [
          {
            "booked_room": "4",
            "status": "waiting for guest",
            "check_in": "2017-02-17",
            "check_out": "2017-02-19",
            "guest": {
              "name": "John Doe",
              "email": "476115+bulk@switch.cm",
              "phone": "6478219711",
              "address": "90 queens drive",
              "city": "Toronto",
              "zip": "M6l3e2"
            },
            "nights": [
              {
                "date": "2017-02-17",
                "price": "149.00",
                "assigned_room": "57"
              },
              {
                "date": "2017-02-18",
                "price": "275.00",
                "assigned_room": "57"
              }
            ]
          }
        ]
      },
      {
        "booking_id": "101657667747",
        "booking_date": "2017-02-17",
        "organiser_name": "John Doe2",
        "rooms": [
          {
            "booked_room": "4",
            "status": "waiting for guest",
            "check_in": "2017-02-19",
            "check_out": "2017-02-20",
            "guest": {
              "name": "John Doe2",
              "email": "476115+bulk@switch.cm",
              "phone": "6478219711",
              "address": "100 queens drive",
              "city": "Toronto",
              "zip": "M6l3e2"
            },
            "nights": [
              {
                "date": "2017-02-19",
                "price": "149.00",
                "assigned_room": "58"
              }
            ]
          }
        ]
      }
    ]
}' 'https://api.dev-bookingsuite.cm/api/reservation/create'
```



### Parameters

Parameter | Description | Mandatory
--------- | ----------- | ---------
property_id | The ID of the property | *
rooms | Multiple rooms  | *
name | name of the room  | *



## Modify Reservation API

insert content

### HTTP Request

`POST https://api.dev-bookingsuite.cm/api/reservation/modify`

```shell
curl -k  -L -X POST -H 'X-Switch-Token: 8c6zfkwf4a1160ankv6r48rve' -H 'Content-Type: application/json' -d '{
  "property_id": "25",
  "reservations": [
      {
        "booking_id": "101657667746",
        "booking_date": "2017-02-17",
        "organiser_name": "John Doe",
        "rooms": [
          {
            "booked_room": "4",
            "status": "waiting for guest",
            "check_in": "2017-02-17",
            "check_out": "2017-02-19",
            "guest": {
              "name": "John Doe",
              "email": "476115+bulk@switch.cm",
              "phone": "6478219711",
              "address": "90 queens drive",
              "city": "Toronto",
              "zip": "M6l3e2"
            },
            "nights": [
              {
                "date": "2017-02-17",
                "price": "149.00",
                "assigned_room": "57"
              },
              {
                "date": "2017-02-18",
                "price": "275.00",
                "assigned_room": "57"
              }
            ]
          }
        ]
      },
      {
        "booking_id": "101657667747",
        "booking_date": "2017-02-17",
        "organiser_name": "John Doe2",
        "rooms": [
          {
            "booked_room": "4",
            "status": "waiting for guest",
            "check_in": "2017-02-19",
            "check_out": "2017-02-20",
            "guest": {
              "name": "John Doe2",
              "email": "476115+bulk@switch.cm",
              "phone": "6478219711",
              "address": "100 queens drive",
              "city": "Toronto",
              "zip": "M6l3e2"
            },
            "nights": [
              {
                "date": "2017-02-19",
                "price": "149.00",
                "assigned_room": "58"
              }
            ]
          }
        ]
      }
    ]
}' 'https://api.dev-bookingsuite.cm/api/reservation/modify'
```



### Parameters

Parameter | Description | Mandatory
--------- | ----------- | ---------
property_id | The ID of the property | *
rooms | Multiple rooms  | *
name | name of the room  | *


## Delete Reservation API

insert content


### HTTP Request

`POST https://api.dev-bookingsuite.cm/api/reservation/delete`

```shell
curl -k  -L -X POST -H 'X-Switch-Token: 8c6zfkwf4a1160ankv6r48rve' -H 'Content-Type: application/json' -d '{
  "property_id": "25",
  "booking_id": "9871"
}' 'https://api.dev-bookingsuite.cm/api/reservation/delete'
```



### Parameters

Parameter | Description | Mandatory
--------- | ----------- | ---------
property_id | The ID of the property | *
booking_id | Booking ID  | *
