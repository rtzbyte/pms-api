---
title: Booking Suite Developer Platform

language_tabs:
  - read
  - create
  - modify
  - delete

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

## What is the HUB?

The HUB is a human-powered system where thousands of employees in the Booking.com basement process thousands of data requests by hand and manually enter them into a switchboard to other hotel applications.

![test2](https://github.com/jeffpan1/pms-api/blob/master/source/images/giphy.gif)

Just kidding. Enter this URL into your browser:

`https://hub-api.booking.com/roomreservations/list/v1?hotelId=2527055&auth_token=YtvwIJvM4Nr8ySOBlpnui6OXufdh6li4f1Qwo5L1RDpw`

That's you pulling a specific reservation from hotelID from the HUB.  

The URL (hub-api.booking.com) will allow you to read & write into a centralized database (with standardized data schemas) to indirectly communicate with hotel technology applications.

## PMS Connectivity Summit 2017

Would you be interested in attending our PMS Connectivity Summit?

During this connectivity summit, we will be bringing PMS providers from around the Globe:

- to stress test our PMS connectivity API's
- to discuss feedback on what features & builds are desirable for PMS providers
- to discuss commercial strategies on how we can help PMS's scale

# Test Environments
To start, you'll be given access to the following sandboxes:

- **Test PMS**
The property management system (PMS) will allow you to simulate a real property and the actions that a traditional hotel would do.

- **Test Booking.com Account** 
The test Booking.com account will allow you to simulate inventory on an OTA and make live reservations for a test property.

- **Authentication Keys**
You will be given a set of API keys to access the sandbox of the Booking Suite Developer Platform.

No commercial agreements to sign, no waiting for us to send you documents to read over.  Start coding immediately and chat with other developers in our public Slack forum.  

When you're ready & you think you have a product that's ready to go live, reach out to us.  Good luck!

**We've taken down the barriers.  We've provided the platform.  You build the products.**

> To authorize, use this code:

```curl
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here"
  -H "X-BKS-Token: yourtokengoeshere"
```

> Make sure to replace `yourtokengoeshere` with your API key.

# Inventory API's

The inventory API synchronizes the physicial & virtual rooms of a property with their PMS, third-party technology applications, and the OTA's that they use.

- **Physical Inventory** refers to the physical rooms inside a property & how they are laid out.
- **Virtual Inventory** refers to the way those rooms are laid out online.  Sometimes properties choose to withhold inventory, or sometimes they'll advertise different rooms on different OTA's and at different prices.

## Layers of Inventory

There are three basic layers of hotel inventory:

**Base Rooms**
The base rooms represents the physical rooms that are inside a hotel, along with the names that a hotel gives those rooms (such as "Room 101" or "Penthouse B").  

**Dynamic Rooms**
A dynamic room is a virtual room that has a parent/child relationship with an physical room.  Examples include:

- a 2-bedroom villa can sell their rooms as individual rooms or the entire villa.  The entire villa option is a dynamic room & is unbookable the moment a single individual room is occupied.
- a hostel room with 4 dorm beds can be sold as individual shared dorm beds or the entire room.  The entire room option is a dynamic room & is unbookable if a single dorm bed is occupied.

**Rate Plans**
A rate plan takes a base room or a dynamic room & changes the price.  Examples include:

- taking a base room (Master Suite) and giving it a specific price on an OTA ("Master Suite Booking.com price")
- taking a dynamic room (Whole Villa) and giving it an auxiliary-specific price ("Whole Villa breakfast included")

## Physical Rooms API (full list)

The Room Types List API shows the full list of a hotel's room types.

Room types are the **physical** rooms that are inside a hotel, such as:

- Master Suite
- Double Room

Things that do **not** qualify as a room type (and will not be in the Room Types List API) include:

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
        "name": "Real King Suite",
        "id": "252705501",
        "roomType": "Suite",
        "standardName": "Superior King Suite",
        "roomTypeId": "5",
        "roomCount": "2",
        "occupancy": "10",
    },
    {
        "name": "Peasants Room",
        "id": "252705502",
        "roomType": "Single",
        "standardName": "Single Room",
        "roomTypeId": "10",
        "roomCount": "11",
        "occupancy": "1",
    }
]

```

### Parameters

Parameter | Description | Mandatory
--------- | ----------- | ---------
name | a label for the roomType created by the property | *
id | an ID for the specific room | *
roomType | The internal standardized Booking.com name of the room type | *
standardName | the internal Booking.com label of the room type | *
RoomTypeID | the internal Booking.com ID of the room type | *
roomCount | The total number of these room types that exist | *
occupancy | how many people can this room sleep | *

Note that if the property has occupancy-based pricing (charging different rates based on the number of people), those occupancy-based rates will be a separate **rate plan**, not a physical room.

The roomType is a standard internal Booking ID.  We've provided an open-sourced library that matches internal Booking roomType names with other OTA's below, making it easy to connect and distribute inventory.

As your properties expand their distribution, this library should (in theory) make it easier to help you automate your mapping.

### RoomType standards

roomType | roomTypeID | Expedia | AirBNB
--------- | ----------- | --------- | ---------
Suite | 2 | exp_suite | airbnb_suite
Single Room | 10 | exp_single | airbnb_single

## Physical Rooms API (partial)

You can also call specific details about individual room details with a different url endpoint.

`/roomtypes/get/v1?id=252705502&hotelId=2527055&auth_token=<token>`

Which will return the information on a single room

```
{
    "roomCount": "11",
    "roomType": "Single",
    "roomTypeId": "10",
    "name": "Peasants Room",
    "occupancy": "1",
    "standardName": "Single Room",
    "id": "252705502"
}

```

## Physical Room Names API

The Room Names API call pulls a list of all of the names of that specific room type.

`/rooms/list/v1?roomTypeId=252705502&hotelId=2527055&auth_token=<token>`

Parameter | Description | Mandatory
--------- | ----------- | ---------
roomType | The name of the room type | *
roomCount | The number of these room types | *
RoomTypeID | a unique value for the room type | *
occupancy | how many people can this room sleep | *
name | a label for this room | *
id | an ID for the specific room | *
standardName | a name for the room | *

> JSON RESPONSE:

```
[
    {
        "hotelId": "2527055",
        "roomTypeId": "252705501",
        "id": "481",
        "name": "1"
    }
]

```

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
}'
```



### Parameters

Parameter | Description | Mandatory
--------- | ----------- | ---------
property_id | The ID of the property | *
rooms | Multiple rooms  | *
name | name of the room  | *

## Dynamic Rooms API 

The dynamic room creates a parent/child physical room.

The API specifies the rooms on the child level.  

`/dynamicrooms/list/v1?hotelId=2527055&auth_token=<token>`

```
{
  "id": "101",
  "name": "Example of a Dynamic Room",
  "type": "dynamic",
  "rooms": [
    {
      "id": "700"
      "name": "Dynamic Room A (first dynamic room)",
      "child": "300", "301"
    }
    {
      "id": 701"
      "name": "Second Dynamic Room"
      "child": "302", "303"
    }
  ]
}
```

## Rate Plans API 

There are four types of rate plans that you can attach to a rate plan.  The child relationship helps specify the availability of the rate plan since the availability will always match the child room's availability.

### Parameters

Parameter | Description | End-User
--------- | ----------- | ---------
GDS | for distribution related to business travel | GDS, business travelers, enterprise travel managers
OTA | rate plans specific to an OTA  | OTA, metasearch
auxiliary | rate plan with ancilliary revenue attached to it  | (n/a)
occupancy-based | different price based on number of guests | (n/a)

`/rateplans/list/v1?hotelId=2527055&auth_token=<token>`

```
[
  {
    "id": "101",
    "name": "Double Room (GDS public)", -- this wholesale rate is specifically for the GDS
    "type": "gds",
    "child": "201" -- points to the physical room type
  }
  {
    "id": "102",
    "name": "Double Room (GDS ABN Amro)", -- this GDS rate is specifically for employees of ABN Amro
    "type": "gds",
    "child": "202"
  }
  {
    "id": "103",
    "name": "Double Room (with breakfast)",
    "type": "auxiliary"
    "child": "203" -- points to the original physical room type
    "auxiliary": "301" -- mapped to the auxiliary item (in this case, 301 is breakfast)
  }
  {
    "id": "104"
    "name": "Double Room (AirBNB price)",
    "type": "ota"
    "child": "204" -- double-check if we need to attach specific ID of OTA, or if that's for distribution API
  }
  {
    "id": "105"
    "name": "Double Room with breakfast, Expedia price",
    "type": "ota"
    "type": "auxiliary" -- notice this room has two tags (ota & auxiliary)
    "child": "205"
    "auxiliary": "301" -- breakfast tag
  }
  {
    "id": "106"
    "name": "Double Room, single occupancy"
    "type": "occupancy"
    "child": "206"
    "occupancy": "1" -- specify's the number of poeple
]
```

# Reservation API's

## Retrieve Reservation API

Retrieve a list of reservations for a property.  Note that it is possible for a reservation to have multiple roomReservations (if it is listed in multiple properties)

'/reservations/get/v1?id=6719&hotelId=2527055&auth_token=<token>'

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
    "id": "6719",
    "roomReservations": [
        "7738",
        "7739"
    ],
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

## Room Reservations API

'/roomreservations/get/v1?id=7738&hotelId=2527055&auth_token=<token>'

### HTTP Request

`POST https://api.switch.cm/api/reservation/create`

```shell
{
    "roomId": "481",
    "roomTypeId": "252705501",
    "status": "OPEN",
    "id": "7738",
    "reservationId": "6719"
}

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

## Notes API

`/notes/list/v1?reservationId=6719&hotelId=2527055&auth_token=<token>`

```shell
[
    {
        "origin": "internal",
        "content": "This is an internal comment"
    }
]
```


### Parameters

Parameter | Description | Mandatory
--------- | ----------- | ---------
property_id | The ID of the property | *
booking_id | Booking ID  | *

# Distribution API's
The Distribution API connects an ecosystem of channel managers to the HUB, allowing third-party technology vendors (such as a PMS) to access reservations from a wide variety of sources (like OTA's).

![gif](https://media.giphy.com/media/l1J9EH625fh1uRAHK/giphy.gif)

On the front-end, a technology vendor can connect to thousands of OTA's and then select from a list of connectivity partners who are certified partners of a particular OTA.

**Advantages for Hotels**

- properties can use multiple channel managers (not just one)
- the billing is handled automatically by Booking.com (instead of juggling multiple subscriptions)
- you can mix-and-match any combination of PMS's and Channel Managers, even if they don't have a direct connection with each other.

**Advantages for Connectivity Partners**

- easy onboarding (no data migration or re-mapping required)
- easy billing (billing is handled by Booking.com)
- direct exposure to millions of partners
- lowered customer acquisition costs 

## Connectivity Endpoints

The first connectivity endpoints allows you to query a predefined list of OTA's & distribution sites.

`/connectivity/v1?auth_token=<token>`

```
[
    {
        "ota_id": "1",
        "ota_name": "AirBNB"
    }
]
```

## Certified Partners

The second endpoint queries a list of connectivity partners that have certifications with a specific OTA.

`/connectivity/v1?auth_token=<token>`

```
[
    {
        "ota_id": "1",
        "ota_name": "AirBNB"
        "channel_manager_id": "1"
        "channel_manager_name": "Sirvoy"
    }
]
```

## Settings

The third endpoint stores the settings of that particular OTA.  In certain cases, if the connectivity provider is changed, this data is stored.

`/connectivity/v1?auth_token=<token>`

```
[
    {
        "ota_id": "1",
        "ota_name": "AirBNB"
        "channel_manager_id": "1"
        "channel_manager_name": "Sirvoy"
        "ota_username": "myairbnb@email.com"
        "ota_password": "password123"
    }
]
```

# Content API's
