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

# Developer Platform

**For an ecosystem to communicate, there needs to be a common language.**

The hotel technology industry faces pain points from all sides.

For technology vendors, the lack of direct access to hotels is driving up the cost of customer acquisition, and forcing them to allocate resources towards sales & marketing instead of innovation. 

For hotels, they lack an independent marketplace to understand products as well as an easy way of migrating their data once they want to deploy applications.

The Booking Suite developer platform is here to give the technology ecosystem a standard template to store data & a common language to share data.  Our goal is to make it easy for hotels & technology to connect to each other.


# Test Environments
To start, you'll be given access to the following sandboxes:

- **Test PMS**
The property management system (PMS) will allow you to simulate a real property and the actions that a traditional hotel would do.

- **Test Booking.com Account** 
The test Booking.com account will allow you to simulate inventory on an OTA and make live reservations for a test property.

- **Authentication Keys**
You will be given a set of API keys to access the sandbox of the Booking Suite Developer Platform.

**We've taken down the barriers.**

No commercial agreements to sign, no waiting for us to send you documents to read over.  Start coding immediately and chat with other developers in our public Slack forum.  

When you're ready & you think you have a product that's ready to go live, reach out to us.  Good luck!

**We've provided the platform.  You build the products.**

> To authorize, use this code:

```curl
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here"
  -H "X-BKS-Token: yourtokengoeshere"
```

> Make sure to replace `yourtokengoeshere` with your API key.

# Inventory API's
There are three basic layers of hotel inventory:

- **Base Rooms**
The base rooms represents the physical rooms that are inside a hotel, along with the names that a hotel gives those rooms (such as "Room 101" or "Penthouse B").  

- **Dynamic Rooms**
A dynamic room is a virtual room that has a parent/child relationship with an physical room.  Examples include:

- a 2-bedroom villa can sell their rooms as individual rooms or the entire villa.  The entire villa option is a dynamic room & is unbookable the moment a single individual room is occupied.
- a hostel room with 4 dorm beds can be sold as individual shared dorm beds or the entire room.  The entire room option is a dynamic room & is unbookable if a single dorm bed is occupied.

- **Rate Plans**

## Room Types

The Room Type represents the different types of **physical** rooms that are inside a hotel, such as:

- Master Suite
- Double Room

Things that do **not** qualify as a room type include:

- **Rate Plan derivatives** such as "Master Suite non-refundable" or "Master Suite flexible cancellation"
- **Meal Plan derivatives** such as "Double Room breakfast included" or "Double Room no breakfast"
- **Dynamic Rooms** (more on that later)
- **OTA-specific rates** (such as "Master Suite, Booking.com price")
- **GDS codes** (such as "Double Room corporate rate")

### HTTP Request

`/roomtypes/list/v1?hotelId=2527055&auth_token=<token>`

```shell
curl -k  -L -X POST -H 'X-BKS-Token: 8c6zfkwf4a1160ankv6r48rve' -H 'Content-Type: application/json' -d '{
  "property_id": "25"
}' 'https://api.dev-bookingsuite.cm/roomtypes/list/v1?hotelId=2527055&auth_token=<token>'
```

> JSON RESPONSE:

```json
[
    {
        "roomType": "Suite",
        "roomCount": "1",
        "roomTypeId": "5",
        "occupancy": "10",
        "name": "Real King Suite",
        "id": "252705501",
        "standardName": "Superior King Suite"
    },
    {
        "name": "Peasants Room",
        "occupancy": "1",
        "standardName": "Single Room",
        "id": "252705502",
        "roomCount": "11",
        "roomType": "Single",
        "roomTypeId": "10"
    }
]

```


### Parameters

Parameter | Description | Mandatory
--------- | ----------- | ---------
roomType | The name of the room type | *
roomCount | The number of these room types | *
RoomTypeID | a unique value for the room type | *
occupancy | how many people can this room sleep | *
name | a label for this room | *
id | an ID for the specific room | *
standardName | a name for the room | *


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

# Distribution API's
This section covers the section of the developer platform that covers topics of distribution like channel managers & GDS.
## Rates & Availability
## Distribution Mapping
# Content API's
