# Merchant API

Shore provides an API specifically for the merchants so that
they can manage their accounts, branches, appointments,
customers, etc..

## POST /api/merchant/me/authenticate

> Example Request

```language-curl
$ curl https://api.shore.com/api/merchant/me/authenticate \
  -X POST \
  -u Mzs6i13SUB16xfyGFAm4: \
  -d datetime='2014-07-16T19:20+01:00'
  -d duration='60'
  -d inform_customer='true'
  -d reason='Because I said so.'
  -H 'Accept: application/vnd.termine24.de; version=3'
  -H 'client_id: de.termine24.dienstleister.ios.1'
  -H 'client_secret: secret'
  -H 'client_bundle_identifier: de.termine24.dienstleister'
  -H 'client_version: 1.0.0'
```

> Example Response

```language-javascript
{
  "email": "mail@example.com",
  "urban_airship_alias":"abc..123",                 # UrbanAirship alias for this merchant's devices or null
                                                    #  if this merchant doesn't have any devices registered
                                                    #  yet.
  "authentication_token": "BG1QJ1Jyzfwnjc2tLm1x",
  "service_providers": [  # Mandatory: All MerchantProfiles belonging to this account (including the current)
    {
      "id": "achsel_alex_gmbh",     # Mandatory: The slug unique to this MerchantProfile (since V3)
      "name": "Achsel Alex GmbH",
      "profile_url": "https://profile.shore.com/muenchen/achsel-alex-gmbh",
      "booking_url": "https://connect.shore.com/achsel-alex-gmbh/widget",
      "fss_url": "https://fss.shore.com/39847bc0-5f20-417b-bd44-e966343797f1/documents",
      "config": {   # since V3.1 - various configuration settings for this merchant
        "required_capacity": true,        # If true, then the merchant wants to see the person count when creating new appointments.
                                          #   If false, then the merchant explicitly does not want person count. If null, then no
                                          #   configuration has been made.
        "required_capacity_options": [    # The options the merchant configured for appointments' person count. Is never null.
          {"name":"1 Person", "value":1}, # 'name' is the localized key to display. 'value' is the integer or string value to pass
          ...                             #   to the create appointment endpoint.
          {"name":"Mehr als 10 Personen", "value":"Mehr als 10 Personen"}
        ],
       },
      "logo_url": "/media/.../logo.jpg", # Optional: The logo, first image or nil (since V3)
      "logo_size": "100x100",            # Mandatory: The requested size (WIDTHxHEIGHT) of the logo
                                         #   even if the logo_url is nil. Default is 100x100 (since V3)
      "time_zone": "Europe/Berlin"  # Mandatory: The time zone of the
        # merchant in form of a TZ Database (https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)
        # time zone.
    }
  ]
}
```

Authenticate the merchant profile's credentials (a.k.a. sign in, login).

### Request URL
`https://api.shore.com/api/merchant/me/authenticate`

### Request Params
<table class="attributes">
  <tr>
    <td>email<div>String, <span class="req">Required</span></div></td>
    <td>Valid email address of an existing MerchantProfile</td>
  </tr>
  <tr>
    <td>password<div>String, <span class="req">Required</span></div></td>
    <td>Plain-text password of the MerchantProfile with the given email address</td>
  </tr>
  <tr>
    <td>device_token<div>String, <span class="opt">Optional</span></div></td>
    <td>Provider specific device ID where this device can receive push notifications (since V3.2)</td>
  </tr>
  <tr>
    <td>device_token_provider<div>String, <span class="opt">Optional</span></div></td>
    <td>Provider for the given device_token (e.g. 'android' or blank for iOS) (since V3.2)</td>
  </tr>
  <tr>
    <td>logo_size<div>String, <span class="opt">Optional</span></div></td>
    <td>The String size of the returned merchant profile logo. Default is "100x100" (since V3)</td>
  </tr>
</table>

### Request Headers
<table class="attributes">
  <tr>
    <td>Accept<div>String, <span class="req">Required</span></div></td>
    <td>The api version to call.</td>
  </tr>
  <tr>
    <td>client_id<div>String, <span class="req">Required</span></div></td>
    <td>The client identifier</td>
  </tr>
  <tr>
    <td>client_secret<div>String, <span class="req">Required</span></div></td>
    <td>The client secret (see ENV['API_CLIENT_SECRET'])</td>
  </tr>
  <tr>
    <td>client_bundle_identifier<div>String, <span class="req">Required</span></div></td>
    <td>The unique identifier for the client (e.g. iOS bundle identifier)</td>
  </tr>
  <tr>
    <td>client_version<div>String, <span class="req">Required</span></div></td>
    <td>The version of the client.</td>
  </tr>
</table>

### Response Code
* `200` Success
* `400` device_token_provider is not a valid provider
* `401` email doesn't exist or password is incorrect
* `401` Client headers are missing or not valid


## GET /api/merchant/resources

> Example Request

```language-curl
$ curl https://api.shore.com/api/merchant/resources \
  -u Mzs6i13SUB16xfyGFAm4: \
  -H 'Accept: application/vnd.termine24.de; version=3'
  -H 'client_id: de.termine24.dienstleister.ios.1'
  -H 'client_secret: secret'
  -H 'client_bundle_identifier: de.termine24.dienstleister'
  -H 'client_version: 1.0.0'
  -H 'Authorization: Token token=jFjNCbGUs3q7wzxc7cPb'
  -H 'service_provider_id: the-merchant-id'
```

> Example Response

```language-javascript
{
  "resources": [
    {
      "id":1234,        # Resource#id
      "name":"Table 2", # Resource#name
    }
  ]
}
```

List this merchant's resources sorted in the order the order they should be displayed. If any of the appointment parameters (i.e. datetime, duration, service_id or required_capacity) are set, then the response will also contain whether or not each service could be requested for an appointment with the given parameters.

### Request URL
`https://api.shore.com/api/merchant/appointments/:id/move`

### Request Params
<table class="attributes">
</table>

### Request Headers
<table class="attributes">
  <tr>
    <td>Accept<div>String, <span class="req">Required</span></div></td>
    <td>The api version to call.</td>
  </tr>
  <tr>
    <td>client_id<div>String, <span class="req">Required</span></div></td>
    <td>The client identifier</td>
  </tr>
  <tr>
    <td>client_secret<div>String, <span class="req">Required</span></div></td>
    <td>The client secret (see ENV['API_CLIENT_SECRET'])</td>
  </tr>
  <tr>
    <td>client_bundle_identifier<div>String, <span class="req">Required</span></div></td>
    <td>The unique identifier for the client (e.g. iOS bundle identifier)</td>
  </tr>
  <tr>
    <td>client_version<div>String, <span class="req">Required</span></div></td>
    <td>The version of the client.</td>
  </tr>
  <tr>
    <td>Authorization<div>String, <span class="req">Required</span></div></td>
    <td>The MerchantAccount#authentication_token of the current merchant account.</td>
  </tr>
  <tr>
    <td>service_provider_id<div>Integer, <span class="opt">Optional</span></div></td>
    <td>The id or slug of the MerchantProfile
        to use for this request as the current profile.
        Default is the first profile created.</td>
  </tr>
</table>

### Response Code
* `200` Success
* `401` Authorization token is missing or is not valid
* `401` Client headers are missing or not valid


## GET /api/merchant/services

> Example Request

```language-curl
$ curl https://api.shore.com/api/merchant/services \
  -u Mzs6i13SUB16xfyGFAm4: \
  -H 'Accept: application/vnd.termine24.de; version=3'
  -H 'client_id: de.termine24.dienstleister.ios.1'
  -H 'client_secret: secret'
  -H 'client_bundle_identifier: de.termine24.dienstleister'
  -H 'client_version: 1.0.0'
  -H 'Authorization: Token token=jFjNCbGUs3q7wzxc7cPb'
  -H 'service_provider_id: the-merchant-id'
```

> Example Response

```language-javascript
{
  "services": [
    {
      "id":1234,                  # Service#id
      "name":"Bikini Waxing",     # Service#name
      "price_cents": 63895,       # Price in cents of the local currency of the merchant
                                  #  (e.g. Euro cents) or nil if no price for this service.
      "price_currency": "EUR"     # ISO 4217 code of the currency of the price or nil if
                                  #  no price for this service. See http://en.wikipedia.org/wiki/ISO_4217
      "price": "638,95 €",        # Formatted price in the local currency of the merchant
      "steps": [                            # Optional. Can be empty though or can also be null when
                                            # appointment was created without steps. Exists since V3.2.
        {
          "type": 'work',                   # There are 4 types of steps:
                                            #   1) 'pre_processing'. Must comes first. Preparation step to start 'work'.
                                            #   2) [default] 'work'. If no types specified than 'work' used as default.
                                            #                        Marks the start and end of the appointment for the customer.
                                            #   3) 'break'. Doesn't change the availability of the resource.
                                            #   4) 'post_processing' Must comes last. Step after 'work' finished.
          "duration": 30,                   # ServiceStep#duration in minutes. Default value is 30 minutes.
          "name": 'Step Name'               # ServiceStep#name. Default value is empty string.
        }
      ]
    }
  ]
}
```

List this merchant's services sorted in the order the order they should be displayed.

### Request URL
`https://api.shore.com/api/merchant/services`

### Request Params
<table class="attributes">
</table>

### Request Headers
<table class="attributes">
  <tr>
    <td>Accept<div>String, <span class="req">Required</span></div></td>
    <td>The api version to call.</td>
  </tr>
  <tr>
    <td>client_id<div>String, <span class="req">Required</span></div></td>
    <td>The client identifier</td>
  </tr>
  <tr>
    <td>client_secret<div>String, <span class="req">Required</span></div></td>
    <td>The client secret (see ENV['API_CLIENT_SECRET'])</td>
  </tr>
  <tr>
    <td>client_bundle_identifier<div>String, <span class="req">Required</span></div></td>
    <td>The unique identifier for the client (e.g. iOS bundle identifier)</td>
  </tr>
  <tr>
    <td>client_version<div>String, <span class="req">Required</span></div></td>
    <td>The version of the client.</td>
  </tr>
  <tr>
    <td>Authorization<div>String, <span class="req">Required</span></div></td>
    <td>The MerchantAccount#authentication_token of the current merchant account.</td>
  </tr>
  <tr>
    <td>service_provider_id<div>Integer, <span class="opt">Optional</span></div></td>
    <td>The id or slug of the MerchantProfile
        to use for this request as the current profile.
        Default is the first profile created.</td>
  </tr>
</table>

### Response Code
* `200` Success
* `401` Authorization token is missing or is not valid
* `401` Client headers are missing or not valid


## POST /api/merchant/appointments/:id/move

> Example Request

```language-curl
$ curl https://api.shore.com/api/merchant/appointments/1234/move \
  -X POST \
  -u Mzs6i13SUB16xfyGFAm4: \
  -d datetime='2014-07-16T19:20+01:00'
  -d duration='60'
  -d inform_customer='true'
  -d reason='Because I said so.'
  -H 'Accept: application/vnd.termine24.de; version=3'
  -H 'client_id: de.termine24.dienstleister.ios.1'
  -H 'client_secret: secret'
  -H 'client_bundle_identifier: de.termine24.dienstleister'
  -H 'client_version: 1.0.0'
  -H 'Authorization: Token token=jFjNCbGUs3q7wzxc7cPb'
  -H 'service_provider_id: the-merchant-id'
```

> Example Response

```language-javascript
{
  "appointment": {
    "id":1234,
    "phase": 1,                             # 0 = open, 1 = will take place, 2 = will not take place or already did take place
    "accepted_state": 10,                   # 10 (pending), 11 (accepted), 12 (cancelled)
    "action_required_by": 0,                # 0 = no action required, 1 = merchant action required, 2 = customer action required
    "human_state": "Termin findet statt"    # Localized description of the appointment's
                                            # current state from the merchant's perspective
    "datetime":"2014-07-16T19:20:00+02:00",
    "datetime_end":"2014-07-16T19:50:00+02:00",
    "duration": 30,
    "pre_duration": 0,
    "post_duration": 15,
    "service_name": "Reservierung, Anderes"   # Names of the services booked or null if none booked
    "human_price": "638,95 €",                # Price as formatted string or null if free
    "color": null,                            # Color assigned to the Appointment. "/#[0-9a-f]{6}/i" or "null".
    "attachments": ["123"],                   # JSON array of attachment ids from FSS.
    "attachments_info": [                         # Array of attachments with meta information about each attachment
      {
        "id": "123"                               # id of attachment
        "filename": "image.jpg",                  # filename
        "url": "http://fss.shore.com/image.jpg",  # url to the uploaded file (you will be redirected to amazon afterwards)
        "attachment_label": "Attachments",        # label to show for attachment
        "deletable": false                        # is it allowed to delete this attachment
      },
      ...
    ],
    "human_additional_information": {         # Mandatory. Can be {}. Keys are not predefined.
                                              # This is just an example of possible keys.
      "required_capacity": {                  # Each key always has a non-localized key
        "human_key": "Personenzahl",          # Each key always has the localized key
        "value": "5 Personen"               # Each key always has the string value
      },
      "subject": {                            # This is a commonly defined key. It
        "human_key": "Betreff",               # is optionally set when the merchant
        "value=>"the subject"                 # creates an appointment
      }
      "reason_for_booking": {                 # This is a commonly defined key. It
        "human_key": "Termindetails",         # is optionally set when the customer
        "value": "I want to celebrate party." # creates an appointment
      },
      ... Different keys or additional keys may be present ...
    },
    "customer": {
      "id":1234,                  # MerchantCustomer#id
      "uuid":1234,                # MerchantCustomer#uuid
      "title":"Herr",             # localized title (e.g. Herr/Frau) or null if title not known
      "first_name":"Max",         # first name or null if not known
      "last_name":"Mustermann",   # last name
      "email":"max@mail.de",      # contact email or null if not known
      "mobile":"017623648372",    # contact mobile phone number or null if they have none
      "deleted":false             # true if the customer has been deleted, otherwise false
    }
    "services": [                                 # Optional. Can be empty though. Exists since V3.2.
      {
        "id":"the-service-id",      # Service#id
        "name":"Bikini Waxing",     # Service#name
      }
    ],
    "resources": [                                # Optional. Can be empty though. Exists since V3.1.
      {
        "id":1234,        # Resource#id
        "name":"Table 2", # Resource#name
      }
    ],
    "steps": [                                    # Optional. Can be empty though or can also be null when
                                                  # appointment was created without steps. Exists since V3.2.
      {
        "type": 'work',                   # There are 4 types of steps:
                                          #   1) 'pre_processing'. Must comes first. Preparation step to start 'work'.
                                          #   2) [default] 'work'. If no types specified than 'work' used as default.
                                          #                        Marks the start and end of the appointment for the customer.
                                          #   3) 'break'. Doesn't change the availability of the resource.
                                          #   4) 'post_processing' Must comes last. Step after 'work' finished.
        "duration": 30,                   # ServiceStep#duration in minutes. Default value is 30 minutes.
        "name": 'Step Name',              # ServiceStep#name. Default value is empty string.
        "resource_ids": ['1234', '5678']  # Default value is empty array.
                                          # '1234' and '5678' are Resource#id. step#resource_ids are no such as ids in appointment#resources

      }
    ]
  }
}
```

Propose an alternative time for the appointment request with the given id belonging to the current merchant.

### Request URL
`https://api.shore.com/api/merchant/appointments/:id/move`

### Request Params
<table class="attributes">
  <tr>
    <td>id<div>String, <span class="req">Required</span></div></td>
    <td>Appointment#id to move</td>
  </tr>
  <tr>
    <td>datetime<div>String, <span class="opt">Optional</span></div></td>
    <td>New date and time for the appointment in ISO8601 format (see http://www.w3.org/TR/NOTE-datetime)</td>
  </tr>
  <tr>
    <td>id<div>Integer, <span class="opt">Optional</span></div></td>
    <td>New duration for the appointment in minutes.</td>
  </tr>
  <tr>
    <td>id<div>Boolean, <span class="opt">Optional</span></div></td>
    <td>If "true", a message will be sent to the appointment's
        customer's notification channels (if appointment has a customer).
        Otherwise, no message will be sent. This flag is ignored when
        the appointment has not been accepted yet. In that case, the customer
        is always informed about moves. Default is false.</td>
  </tr>
  <tr>
    <td>reason<div>Integer, <span class="opt">Optional</span></div></td>
    <td>Text from merchant why they want to move the appointment.</td>
  </tr>
</table>

### Request Headers
<table class="attributes">
  <tr>
    <td>Accept<div>String, <span class="req">Required</span></div></td>
    <td>The api version to call.</td>
  </tr>
  <tr>
    <td>client_id<div>String, <span class="req">Required</span></div></td>
    <td>The client identifier</td>
  </tr>
  <tr>
    <td>client_secret<div>String, <span class="req">Required</span></div></td>
    <td>The client secret (see ENV['API_CLIENT_SECRET'])</td>
  </tr>
  <tr>
    <td>client_bundle_identifier<div>String, <span class="req">Required</span></div></td>
    <td>The unique identifier for the client (e.g. iOS bundle identifier)</td>
  </tr>
  <tr>
    <td>client_version<div>String, <span class="req">Required</span></div></td>
    <td>The version of the client.</td>
  </tr>
  <tr>
    <td>Authorization<div>String, <span class="req">Required</span></div></td>
    <td>The MerchantAccount#authentication_token of the current merchant account.</td>
  </tr>
  <tr>
    <td>service_provider_id<div>Integer, <span class="opt">Optional</span></div></td>
    <td>The id or slug of the MerchantProfile
        to use for this request as the current profile.
        Default is the first profile created.</td>
  </tr>
</table>

### Response Code
* `200` Success
* `400` Appointment could not be rejected (e.g. already rejected)
* `404` Appointment with the given id does not exist
* `401` Authorization token is missing or is not valid
* `401` Client headers are missing or not valid

## PUT /api/merchant/appointments/:id/accept

> Example Request

```language-curl
$ curl https://api.shore.com/api/merchant/appointments/1234/accept \
  -X PUT \
  -H 'Accept: application/vnd.termine24.de; version=3'
  -H 'client_id: de.termine24.dienstleister.ios.1'
  -H 'client_secret: secret'
  -H 'client_bundle_identifier: de.termine24.dienstleister'
  -H 'client_version: 1.0.0'
  -H 'Authorization: Token token=jFjNCbGUs3q7wzxc7cPb'
  -H 'service_provider_id: the-merchant-id'
```

> Example Response

```language-javascript
{
  "appointment": {
    "id":1234,
    "phase": 1,                             # 0 = open, 1 = will take place, 2 = will not take place or already did take place
    "accepted_state": 10,                   # 10 (pending), 11 (accepted), 12 (cancelled)
    "action_required_by": 0,                # 0 = no action required, 1 = merchant action required, 2 = customer action required
    "human_state": "Termin findet statt"    # Localized description of the appointment's
                                            # current state from the merchant's perspective
    "datetime":"2014-07-16T19:20:00+02:00",
    "datetime_end":"2014-07-16T19:50:00+02:00",
    "duration": 30,
    "pre_duration": 0,
    "post_duration": 15,
    "service_name": "Reservierung, Anderes"   # Names of the services booked or null if none booked
    "human_price": "638,95 €",                # Price as formatted string or null if free
    "color": null,                            # Color assigned to the Appointment. "/#[0-9a-f]{6}/i" or "null".
    "attachments": ["123"],                   # JSON array of attachment ids from FSS.
    "attachments_info": [                         # Array of attachments with meta information about each attachment
      {
        "id": "123"                               # id of attachment
        "filename": "image.jpg",                  # filename
        "url": "http://fss.shore.com/image.jpg",  # url to the uploaded file (you will be redirected to amazon afterwards)
        "attachment_label": "Attachments",        # label to show for attachment
        "deletable": false                        # is it allowed to delete this attachment
      },
      ...
    ],
    "human_additional_information": {         # Mandatory. Can be {}. Keys are not predefined.
                                              # This is just an example of possible keys.
      "required_capacity": {                  # Each key always has a non-localized key
        "human_key": "Personenzahl",          # Each key always has the localized key
        "value": "5 Personen"               # Each key always has the string value
      },
      "subject": {                            # This is a commonly defined key. It
        "human_key": "Betreff",               # is optionally set when the merchant
        "value=>"the subject"                 # creates an appointment
      }
      "reason_for_booking": {                 # This is a commonly defined key. It
        "human_key": "Termindetails",         # is optionally set when the customer
        "value": "I want to celebrate party." # creates an appointment
      },
      ... Different keys or additional keys may be present ...
    },
    "customer": {
      "id":1234,                  # MerchantCustomer#id
      "uuid":1234,                # MerchantCustomer#uuid
      "title":"Herr",             # localized title (e.g. Herr/Frau) or null if title not known
      "first_name":"Max",         # first name or null if not known
      "last_name":"Mustermann",   # last name
      "email":"max@mail.de",      # contact email or null if not known
      "mobile":"017623648372",    # contact mobile phone number or null if they have none
      "deleted":false             # true if the customer has been deleted, otherwise false
    }
    "services": [                                 # Optional. Can be empty though. Exists since V3.2.
      {
        "id":"the-service-id",      # Service#id
        "name":"Bikini Waxing",     # Service#name
      }
    ],
    "resources": [                                # Optional. Can be empty though. Exists since V3.1.
      {
        "id":1234,        # Resource#id
        "name":"Table 2", # Resource#name
      }
    ],
    "steps": [                                    # Optional. Can be empty though or can also be null when
                                                  # appointment was created without steps. Exists since V3.2.
      {
        "type": 'work',                   # There are 4 types of steps:
                                          #   1) 'pre_processing'. Must comes first. Preparation step to start 'work'.
                                          #   2) [default] 'work'. If no types specified than 'work' used as default.
                                          #                        Marks the start and end of the appointment for the customer.
                                          #   3) 'break'. Doesn't change the availability of the resource.
                                          #   4) 'post_processing' Must comes last. Step after 'work' finished.
        "duration": 30,                   # ServiceStep#duration in minutes. Default value is 30 minutes.
        "name": 'Step Name',              # ServiceStep#name. Default value is empty string.
        "resource_ids": ['1234', '5678']  # Default value is empty array.
                                          # '1234' and '5678' are Resource#id. step#resource_ids are no such as ids in appointment#resources

      }
    ]
  }
}
```

Accept the appointment request with the given id belonging to the current merchant.

### Request URL
`https://api.shore.com/api/merchant/appointments/:id/accept`

### Request parameters
<table class="attributes">
  <tr>
    <td>id<div>String, <span class="req">Required</span></div></td>
    <td>Mandatory. Appointment#id to accept</td>
  </tr>
</table>

### Request Headers
<table class="attributes">
  <tr>
    <td>Accept<div>String, <span class="req">Required</span></div></td>
    <td>The api version to call.</td>
  </tr>
  <tr>
    <td>client_id<div>String, <span class="req">Required</span></div></td>
    <td>The client identifier</td>
  </tr>
  <tr>
    <td>client_secret<div>String, <span class="req">Required</span></div></td>
    <td>The client secret (see ENV['API_CLIENT_SECRET'])</td>
  </tr>
  <tr>
    <td>client_bundle_identifier<div>String, <span class="req">Required</span></div></td>
    <td>The unique identifier for the client (e.g. iOS bundle identifier)</td>
  </tr>
  <tr>
    <td>client_version<div>String, <span class="req">Required</span></div></td>
    <td>The version of the client.</td>
  </tr>
  <tr>
    <td>Authorization<div>String, <span class="req">Required</span></div></td>
    <td>The MerchantAccount#authentication_token of the current merchant account.</td>
  </tr>
  <tr>
    <td>service_provider_id<div>Integer, <span class="opt">Optional</span></div></td>
    <td>The id or slug of the MerchantProfile
        to use for this request as the current profile.
        Default is the first profile created.</td>
  </tr>
</table>

### Response Code
* `200` Success
* `400` Appointment could not be accepted (e.g. already rejected)
* `404` Appointment with the given id does not exist
* `401` Authorization token is missing or is not valid
* `401` Client headers are missing or not valid


## POST /api/merchant/appointments/

> Example Request

```language-curl
$ curl https://api.shore.com/api/merchant/appointments \
  -X POST \
  -d datetime='2014-07-16T19:20+01:00'
  -d duration='60'
  -H 'Accept: application/vnd.termine24.de; version=3'
  -H 'client_id: de.termine24.dienstleister.ios.1'
  -H 'client_secret: secret'
  -H 'client_bundle_identifier: de.termine24.dienstleister'
  -H 'client_version: 1.0.0'
  -H 'Authorization: Token token=jFjNCbGUs3q7wzxc7cPb'
  -H 'service_provider_id: the-merchant-id'
```

> Example Response

```language-javascript
{
  "appointment": {
    "id":1234,
    "phase": 1,                             # 0 = open, 1 = will take place, 2 = will not take place or already did take place
    "accepted_state": 10,                   # 10 (pending), 11 (accepted), 12 (cancelled)
    "action_required_by": 0,                # 0 = no action required, 1 = merchant action required, 2 = customer action required
    "human_state": "Termin findet statt"    # Localized description of the appointment's
                                            # current state from the merchant's perspective
    "datetime":"2014-07-16T19:20:00+02:00",
    "datetime_end":"2014-07-16T19:50:00+02:00",
    "duration": 30,
    "pre_duration": 0,
    "post_duration": 15,
    "service_name": "Reservierung, Anderes"   # Names of the services booked or null if none booked
    "human_price": "638,95 €",                # Price as formatted string or null if free
    "color": null,                            # Color assigned to the Appointment. "/#[0-9a-f]{6}/i" or "null".
    "attachments": ["123"],                   # JSON array of attachment ids from FSS.
    "attachments_info": [                         # Array of attachments with meta information about each attachment
      {
        "id": "123"                               # id of attachment
        "filename": "image.jpg",                  # filename
        "url": "http://fss.shore.com/image.jpg",  # url to the uploaded file (you will be redirected to amazon afterwards)
        "attachment_label": "Attachments",        # label to show for attachment
        "deletable": false                        # is it allowed to delete this attachment
      },
      ...
    ],
    "human_additional_information": {         # Mandatory. Can be {}. Keys are not predefined.
                                              # This is just an example of possible keys.
      "required_capacity": {                  # Each key always has a non-localized key
        "human_key": "Personenzahl",          # Each key always has the localized key
        "value": "5 Personen"               # Each key always has the string value
      },
      "subject": {                            # This is a commonly defined key. It
        "human_key": "Betreff",               # is optionally set when the merchant
        "value=>"the subject"                 # creates an appointment
      }
      "reason_for_booking": {                 # This is a commonly defined key. It
        "human_key": "Termindetails",         # is optionally set when the customer
        "value": "I want to celebrate party." # creates an appointment
      },
      ... Different keys or additional keys may be present ...
    },
    "customer": {
      "id":1234,                  # MerchantCustomer#id
      "uuid":1234,                # MerchantCustomer#uuid
      "title":"Herr",             # localized title (e.g. Herr/Frau) or null if title not known
      "first_name":"Max",         # first name or null if not known
      "last_name":"Mustermann",   # last name
      "email":"max@mail.de",      # contact email or null if not known
      "mobile":"017623648372",    # contact mobile phone number or null if they have none
      "deleted":false             # true if the customer has been deleted, otherwise false
    }
    "services": [                                 # Optional. Can be empty though. Exists since V3.2.
      {
        "id":"the-service-id",      # Service#id
        "name":"Bikini Waxing",     # Service#name
      }
    ],
    "resources": [                                # Optional. Can be empty though. Exists since V3.1.
      {
        "id":1234,        # Resource#id
        "name":"Table 2", # Resource#name
      }
    ],
    "steps": [                                    # Optional. Can be empty though or can also be null when
                                                  # appointment was created without steps. Exists since V3.2.
      {
        "type": 'work',                   # There are 4 types of steps:
                                          #   1) 'pre_processing'. Must comes first. Preparation step to start 'work'.
                                          #   2) [default] 'work'. If no types specified than 'work' used as default.
                                          #                        Marks the start and end of the appointment for the customer.
                                          #   3) 'break'. Doesn't change the availability of the resource.
                                          #   4) 'post_processing' Must comes last. Step after 'work' finished.
        "duration": 30,                   # ServiceStep#duration in minutes. Default value is 30 minutes.
        "name": 'Step Name',              # ServiceStep#name. Default value is empty string.
        "resource_ids": ['1234', '5678']  # Default value is empty array.
                                          # '1234' and '5678' are Resource#id. step#resource_ids are no such as ids in appointment#resources

      }
    ]
  }
}
```

Create a new appointment with the given parameters.

### Request URL
`https://api.shore.com/api/merchant/appointments`

### Request parameters
<table class="attributes">
  <tr>
    <td>datetime<div>String, <span class="req">Required</span></div></td>
    <td>Mandatory: Date + time of appointment in ISO8601
        format (see http://www.w3.org/TR/NOTE-datetime)</td>
  </tr>
  <tr>
    <td>service_ids<div>JSON Array, <span class="opt">Optional</span></div></td>
    <td>list of service ids {Service} performed during the appointment.
        See {Api::Merchant::V3::ServicesController#index}.
        Appointment duration, pre-duration and post-duration are set to
        this service's values unless otherwise specified in the parameters.</td>
  </tr>
  <tr>
    <td>duration<div>Integer, <span class="opt">Optional</span></div></td>
    <td>Duration of the appointment in minutes. Defaults to duration
        of the given service or 60 if no service defined.</td>
  </tr>
  <tr>
    <td>required_capacity<div>Integer, <span class="opt">Optional</span></div></td>
    <td>Number of participants for the appointment (positive integer).</td>
  </tr>
  <tr>
    <td>subject<div>String, <span class="opt">Optional</span></div></td>
    <td>Text description of the appointment.
        should receive a reminder via the merchant's notification
        channels. Default is "24".</td>
  </tr>
  <tr>
    <td>attachments<div>JSON Array, <span class="opt">Optional</span></div></td>
    <td>JSON array of attachment ids recieved from FSS.</td>
  </tr>
  <tr>
    <td>resource_ids<div>JSON Array, <span class="opt">Optional</span></div></td>
    <td>JSON array of {Resource#id}s.</td>
  </tr>
  <tr>
    <td>customer_id<div>String, <span class="opt">Optional</span></div></td>
    <td>{MerchantCustomer#id} of the customer for the appointment</td>
  </tr>
  <tr>
    <td>participating_account_ids<div>JSON Array, <span class="opt">Optional</span></div></td>
    <td>list of participating account ids</td>
  </tr>
  <tr>
    <td>color<div>String, <span class="opt">Optional</span></div></td>
    <td>String representing the HEX color code</td>
  </tr>
  <tr>
    <td>inform_customer<div>Boolean, <span class="opt">Optional</span></div></td>
    <td>If "true", a message will be sent to the appointment's
        customer's notification channels.</td>
  </tr>
  <tr>
    <td>customer[last_name]<div>String, <span class="opt">Optional</span></div></td>
    <td>Customer's last name. This and the following customer[*] parameters add
        this customer data to the appointment. Additionally,
        if the customer_id is given then this data is merged with the existing data for that MerchantCustomer.
        If no customer_id is given and no matching MerchantCustomer can be found, then a new
        MerchantCustomer is created.</td>
  </tr>
  <tr>
    <td>customer[first_name]<div>String, <span class="opt">Optional</span></div></td>
    <td>Customer's first name</td>
  </tr>
  <tr>
    <td>customer[gender]<div>String, <span class="opt">Optional</span></div></td>
    <td>Customer's gender "male" or "female"</td>
  </tr>
  <tr>
    <td>customer[email]<div>String, <span class="opt">Optional</span></div></td>
    <td>Customer's email address</td>
  </tr>
  <tr>
    <td>customer[mobile]<div>String, <span class="opt">Optional</span></div></td>
    <td>Customer's mobile phone number</td>
  </tr>
</table>

### Request Headers
<table class="attributes">
  <tr>
    <td>Accept<div>String, <span class="req">Required</span></div></td>
    <td>The api version to call.</td>
  </tr>
  <tr>
    <td>client_id<div>String, <span class="req">Required</span></div></td>
    <td>The client identifier</td>
  </tr>
  <tr>
    <td>client_secret<div>String, <span class="req">Required</span></div></td>
    <td>The client secret (see ENV['API_CLIENT_SECRET'])</td>
  </tr>
  <tr>
    <td>client_bundle_identifier<div>String, <span class="req">Required</span></div></td>
    <td>The unique identifier for the client (e.g. iOS bundle identifier)</td>
  </tr>
  <tr>
    <td>client_version<div>String, <span class="req">Required</span></div></td>
    <td>The version of the client.</td>
  </tr>
  <tr>
    <td>Authorization<div>String, <span class="req">Required</span></div></td>
    <td>The MerchantAccount#authentication_token of the current merchant account.</td>
  </tr>
  <tr>
    <td>service_provider_id<div>Integer, <span class="opt">Optional</span></div></td>
    <td>The id or slug of the MerchantProfile
        to use for this request as the current profile.
        Default is the first profile created.</td>
  </tr>
</table>

### Response Code
* `200` Success
* `400` Appointment could not be rejected (e.g. already rejected)
* `404` Appointment with the given id does not exist
* `401` Authorization token is missing or is not valid
* `401` Client headers are missing or not valid

## GET /api/merchant/appointments

> Example Request

```language-curl
$ curl https://api.shore.com/api/merchant/appointments \
  -H 'Accept: application/vnd.termine24.de; version=3'
  -H 'client_id: de.termine24.dienstleister.ios.1'
  -H 'client_secret: secret'
  -H 'client_bundle_identifier: de.termine24.dienstleister'
  -H 'client_version: 1.0.0'
  -H 'Authorization: Token token=jFjNCbGUs3q7wzxc7cPb'
  -H 'service_provider_id: the-merchant-id'
```

> Example Response

```language-javascript
{
  "appointments": [
    {
      "id":1234,
      "phase": 1,                             # 0 = open, 1 = will take place, 2 = will not take place or already did take place
      "accepted_state": 10,                   # 10 (pending), 11 (accepted), 12 (cancelled)
      "action_required_by": 0,                # 0 = no action required, 1 = merchant action required, 2 = customer action required
      "human_state": "Termin findet statt"    # Localized description of the appointment's
                                              # current state from the merchant's perspective
      "datetime":"2014-07-16T19:20:00+02:00",
      "datetime_end":"2014-07-16T19:50:00+02:00",
      "duration": 30,
      "pre_duration": 0,
      "post_duration": 15,
      "service_name": "Reservierung, Anderes"   # Names of the services booked or null if none booked
      "human_price": "638,95 €",                # Price as formatted string or null if free
      "color": null,                            # Color assigned to the Appointment. "/#[0-9a-f]{6}/i" or "null".
      "attachments": ["123"],                   # JSON array of attachment ids from FSS.
      "attachments_info": [                         # Array of attachments with meta information about each attachment
        {
          "id": "123"                               # id of attachment
          "filename": "image.jpg",                  # filename
          "url": "http://fss.shore.com/image.jpg",  # url to the uploaded file (you will be redirected to amazon afterwards)
          "attachment_label": "Attachments",        # label to show for attachment
          "deletable": false                        # is it allowed to delete this attachment
        },
        ...
      ],
      "human_additional_information": {         # Mandatory. Can be {}. Keys are not predefined.
                                                # This is just an example of possible keys.
        "required_capacity": {                  # Each key always has a non-localized key
          "human_key": "Personenzahl",          # Each key always has the localized key
          "value": "5 Personen"               # Each key always has the string value
        },
        "subject": {                            # This is a commonly defined key. It
          "human_key": "Betreff",               # is optionally set when the merchant
          "value=>"the subject"                 # creates an appointment
        }
        "reason_for_booking": {                 # This is a commonly defined key. It
          "human_key": "Termindetails",         # is optionally set when the customer
          "value": "I want to celebrate party." # creates an appointment
        },
        ... Different keys or additional keys may be present ...
      },
      "customer": {
        "id":1234,                  # MerchantCustomer#id
        "uuid":1234,                # MerchantCustomer#uuid
        "title":"Herr",             # localized title (e.g. Herr/Frau) or null if title not known
        "first_name":"Max",         # first name or null if not known
        "last_name":"Mustermann",   # last name
        "email":"max@mail.de",      # contact email or null if not known
        "mobile":"017623648372",    # contact mobile phone number or null if they have none
        "deleted":false             # true if the customer has been deleted, otherwise false
      }
      "services": [                                 # Optional. Can be empty though. Exists since V3.2.
        {
          "id":"the-service-id",      # Service#id
          "name":"Bikini Waxing",     # Service#name
        }
      ],
      "resources": [                                # Optional. Can be empty though. Exists since V3.1.
        {
          "id":1234,        # Resource#id
          "name":"Table 2", # Resource#name
        }
      ],
      "steps": [                                    # Optional. Can be empty though or can also be null when
                                                    # appointment was created without steps. Exists since V3.2.
        {
          "type": 'work',                   # There are 4 types of steps:
                                            #   1) 'pre_processing'. Must comes first. Preparation step to start 'work'.
                                            #   2) [default] 'work'. If no types specified than 'work' used as default.
                                            #                        Marks the start and end of the appointment for the customer.
                                            #   3) 'break'. Doesn't change the availability of the resource.
                                            #   4) 'post_processing' Must comes last. Step after 'work' finished.
          "duration": 30,                   # ServiceStep#duration in minutes. Default value is 30 minutes.
          "name": 'Step Name',              # ServiceStep#name. Default value is empty string.
          "resource_ids": ['1234', '5678']  # Default value is empty array.
                                            # '1234' and '5678' are Resource#id. step#resource_ids are no such as ids in appointment#resources

        }
      ]
    }
  ]
}
```

List this merchant's appointments sorted by the Appointment#datetime oldest first.

### Request URL
`https://api.shore.com/api/merchant/appointments`

### Request parameters
<table class="attributes">
  <tr>
    <td>accepted_states<div>String, <span class="req">Required</span></div></td>
    <td>Returns only the appointments with the given integer
        state separated by '|' to filter for
        appointments with state1 OR state2 OR stateN.
        if not provided - uses phases.
        10 (pending), 11 (accepted), and 12 (cancelled). Note: This parameter
        cannot be used in combination of the phases parameter!</td>
  </tr>
  <tr>
    <td>page<div>Integer, <span class="opt">Optional</span></div></td>
    <td>The current page of the results. Default is "1"</td>
  </tr>
  <tr>
    <td>per_page<div>Integer, <span class="opt">Optional</span></div></td>
    <td>The number of results per page. Default is "10". Set
        to "0" for unlimited results per page.</td>
  </tr>
  <tr>
    <td>after<div>ISO8601 String, <span class="opt">Optional</span></div></td>
    <td>Return only appointments whose datetime is >= 'after'.</td>
  </tr>
  <tr>
    <td>before<div>ISO8601 String, <span class="opt">Optional</span></div></td>
    <td>Return only appointments whose datetime is <= 'before'.</td>
  </tr>
  <tr>
    <td>order<div>String, <span class="opt">Optional</span></div></td>
    <td>Allowed values are "asc" and "desc". Default: "asc".
        Specifies ordering by datetime of the appointments.</td>
  </tr>
</table>

### Request Headers
<table class="attributes">
  <tr>
    <td>Accept<div>String, <span class="req">Required</span></div></td>
    <td>The api version to call.</td>
  </tr>
  <tr>
    <td>client_id<div>String, <span class="req">Required</span></div></td>
    <td>The client identifier</td>
  </tr>
  <tr>
    <td>client_secret<div>String, <span class="req">Required</span></div></td>
    <td>The client secret (see ENV['API_CLIENT_SECRET'])</td>
  </tr>
  <tr>
    <td>client_bundle_identifier<div>String, <span class="req">Required</span></div></td>
    <td>The unique identifier for the client (e.g. iOS bundle identifier)</td>
  </tr>
  <tr>
    <td>client_version<div>String, <span class="req">Required</span></div></td>
    <td>The version of the client.</td>
  </tr>
  <tr>
    <td>Authorization<div>String, <span class="req">Required</span></div></td>
    <td>The MerchantAccount#authentication_token of the current merchant account.</td>
  </tr>
  <tr>
    <td>service_provider_id<div>Integer, <span class="opt">Optional</span></div></td>
    <td>The id or slug of the MerchantProfile
        to use for this request as the current profile.
        Default is the first profile created.</td>
  </tr>
</table>

### Response Code
* `200` Success
* `400` Invalid parameter(s): <reason for being invalid>
* `401` Authorization token is missing or is not valid
* `401` Client headers are missing or not valid

## PUT /api/merchant/appointments/:id/reject

> Example Request

```language-curl
$ curl https://api.shore.com/api/merchant/appointments/:id/reject \
  -X PUT
  -H 'Accept: application/vnd.termine24.de; version=3'
  -H 'client_id: de.termine24.dienstleister.ios.1'
  -H 'client_secret: secret'
  -H 'client_bundle_identifier: de.termine24.dienstleister'
  -H 'client_version: 1.0.0'
  -H 'Authorization: Token token=jFjNCbGUs3q7wzxc7cPb'
  -H 'service_provider_id: the-merchant-id'
```

> Example Response

```language-javascript
{
  "appointment": {
    "id":1234,
    "phase": 1,                             # 0 = open, 1 = will take place, 2 = will not take place or already did take place
    "accepted_state": 10,                   # 10 (pending), 11 (accepted), 12 (cancelled)
    "action_required_by": 0,                # 0 = no action required, 1 = merchant action required, 2 = customer action required
    "human_state": "Termin findet statt"    # Localized description of the appointment's
                                            # current state from the merchant's perspective
    "datetime":"2014-07-16T19:20:00+02:00",
    "datetime_end":"2014-07-16T19:50:00+02:00",
    "duration": 30,
    "pre_duration": 0,
    "post_duration": 15,
    "service_name": "Reservierung, Anderes"   # Names of the services booked or null if none booked
    "human_price": "638,95 €",                # Price as formatted string or null if free
    "color": null,                            # Color assigned to the Appointment. "/#[0-9a-f]{6}/i" or "null".
    "attachments": ["123"],                   # JSON array of attachment ids from FSS.
    "attachments_info": [                         # Array of attachments with meta information about each attachment
      {
        "id": "123"                               # id of attachment
        "filename": "image.jpg",                  # filename
        "url": "http://fss.shore.com/image.jpg",  # url to the uploaded file (you will be redirected to amazon afterwards)
        "attachment_label": "Attachments",        # label to show for attachment
        "deletable": false                        # is it allowed to delete this attachment
      },
      ...
    ],
    "human_additional_information": {         # Mandatory. Can be {}. Keys are not predefined.
                                              # This is just an example of possible keys.
      "required_capacity": {                  # Each key always has a non-localized key
        "human_key": "Personenzahl",          # Each key always has the localized key
        "value": "5 Personen"               # Each key always has the string value
      },
      "subject": {                            # This is a commonly defined key. It
        "human_key": "Betreff",               # is optionally set when the merchant
        "value=>"the subject"                 # creates an appointment
      }
      "reason_for_booking": {                 # This is a commonly defined key. It
        "human_key": "Termindetails",         # is optionally set when the customer
        "value": "I want to celebrate party." # creates an appointment
      },
      ... Different keys or additional keys may be present ...
    },
    "customer": {
      "id":1234,                  # MerchantCustomer#id
      "uuid":1234,                # MerchantCustomer#uuid
      "title":"Herr",             # localized title (e.g. Herr/Frau) or null if title not known
      "first_name":"Max",         # first name or null if not known
      "last_name":"Mustermann",   # last name
      "email":"max@mail.de",      # contact email or null if not known
      "mobile":"017623648372",    # contact mobile phone number or null if they have none
      "deleted":false             # true if the customer has been deleted, otherwise false
    }
    "services": [                                 # Optional. Can be empty though. Exists since V3.2.
      {
        "id":"the-service-id",      # Service#id
        "name":"Bikini Waxing",     # Service#name
      }
    ],
    "resources": [                                # Optional. Can be empty though. Exists since V3.1.
      {
        "id":1234,        # Resource#id
        "name":"Table 2", # Resource#name
      }
    ],
    "steps": [                                    # Optional. Can be empty though or can also be null when
                                                  # appointment was created without steps. Exists since V3.2.
      {
        "type": 'work',                   # There are 4 types of steps:
                                          #   1) 'pre_processing'. Must comes first. Preparation step to start 'work'.
                                          #   2) [default] 'work'. If no types specified than 'work' used as default.
                                          #                        Marks the start and end of the appointment for the customer.
                                          #   3) 'break'. Doesn't change the availability of the resource.
                                          #   4) 'post_processing' Must comes last. Step after 'work' finished.
        "duration": 30,                   # ServiceStep#duration in minutes. Default value is 30 minutes.
        "name": 'Step Name',              # ServiceStep#name. Default value is empty string.
        "resource_ids": ['1234', '5678']  # Default value is empty array.
                                          # '1234' and '5678' are Resource#id. step#resource_ids are no such as ids in appointment#resources

      }
    ]
  }
}
```

Reject the appointment request with the given id belonging to the current merchant.

### Request URL
`https://api.shore.com/api/merchant/appointments/:id/reject`

### Request parameters
<table class="attributes">
  <tr>
    <td>id<div>String, <span class="req">Required</span></div></td>
    <td>Appointment#id to reject</td>
  </tr>
  <tr>
    <td>reason<div>String, <span class="opt">Optional</span></div></td>
    <td>Text reason for the rejection. Default is null.</td>
  </tr>
</table>

### Request Headers
<table class="attributes">
  <tr>
    <td>Accept<div>String, <span class="req">Required</span></div></td>
    <td>The api version to call.</td>
  </tr>
  <tr>
    <td>client_id<div>String, <span class="req">Required</span></div></td>
    <td>The client identifier</td>
  </tr>
  <tr>
    <td>client_secret<div>String, <span class="req">Required</span></div></td>
    <td>The client secret (see ENV['API_CLIENT_SECRET'])</td>
  </tr>
  <tr>
    <td>client_bundle_identifier<div>String, <span class="req">Required</span></div></td>
    <td>The unique identifier for the client (e.g. iOS bundle identifier)</td>
  </tr>
  <tr>
    <td>client_version<div>String, <span class="req">Required</span></div></td>
    <td>The version of the client.</td>
  </tr>
  <tr>
    <td>Authorization<div>String, <span class="req">Required</span></div></td>
    <td>The MerchantAccount#authentication_token of the current merchant account.</td>
  </tr>
  <tr>
    <td>service_provider_id<div>Integer, <span class="opt">Optional</span></div></td>
    <td>The id or slug of the MerchantProfile
        to use for this request as the current profile.
        Default is the first profile created.</td>
  </tr>
</table>

### Response Code
* `200` Success
* `400` Appointment could not be rejected (e.g. already rejected)
* `404` Appointment with the given id does not exist
* `401` Authorization token is missing or is not valid
* `401` Client headers are missing or not valid

## GET /api/merchant/appointments/:id

> Example Request

```language-curl
$ curl https://api.shore.com/api/merchant/appointments/:id \
  -H 'Accept: application/vnd.termine24.de; version=3'
  -H 'client_id: de.termine24.dienstleister.ios.1'
  -H 'client_secret: secret'
  -H 'client_bundle_identifier: de.termine24.dienstleister'
  -H 'client_version: 1.0.0'
  -H 'Authorization: Token token=jFjNCbGUs3q7wzxc7cPb'
  -H 'service_provider_id: the-merchant-id'
```

> Example Response

```language-javascript
{
  "appointment": {
    "id":1234,
    "phase": 1,                             # 0 = open, 1 = will take place, 2 = will not take place or already did take place
    "accepted_state": 10,                   # 10 (pending), 11 (accepted), 12 (cancelled)
    "action_required_by": 0,                # 0 = no action required, 1 = merchant action required, 2 = customer action required
    "human_state": "Termin findet statt"    # Localized description of the appointment's
                                            # current state from the merchant's perspective
    "datetime":"2014-07-16T19:20:00+02:00",
    "datetime_end":"2014-07-16T19:50:00+02:00",
    "duration": 30,
    "pre_duration": 0,
    "post_duration": 15,
    "service_name": "Reservierung, Anderes"   # Names of the services booked or null if none booked
    "human_price": "638,95 €",                # Price as formatted string or null if free
    "color": null,                            # Color assigned to the Appointment. "/#[0-9a-f]{6}/i" or "null".
    "attachments": ["123"],                   # JSON array of attachment ids from FSS.
    "attachments_info": [                         # Array of attachments with meta information about each attachment
      {
        "id": "123"                               # id of attachment
        "filename": "image.jpg",                  # filename
        "url": "http://fss.shore.com/image.jpg",  # url to the uploaded file (you will be redirected to amazon afterwards)
        "attachment_label": "Attachments",        # label to show for attachment
        "deletable": false                        # is it allowed to delete this attachment
      },
      ...
    ],
    "human_additional_information": {         # Mandatory. Can be {}. Keys are not predefined.
                                              # This is just an example of possible keys.
      "required_capacity": {                  # Each key always has a non-localized key
        "human_key": "Personenzahl",          # Each key always has the localized key
        "value": "5 Personen"               # Each key always has the string value
      },
      "subject": {                            # This is a commonly defined key. It
        "human_key": "Betreff",               # is optionally set when the merchant
        "value=>"the subject"                 # creates an appointment
      }
      "reason_for_booking": {                 # This is a commonly defined key. It
        "human_key": "Termindetails",         # is optionally set when the customer
        "value": "I want to celebrate party." # creates an appointment
      },
      ... Different keys or additional keys may be present ...
    },
    "customer": {
      "id":1234,                  # MerchantCustomer#id
      "uuid":1234,                # MerchantCustomer#uuid
      "title":"Herr",             # localized title (e.g. Herr/Frau) or null if title not known
      "first_name":"Max",         # first name or null if not known
      "last_name":"Mustermann",   # last name
      "email":"max@mail.de",      # contact email or null if not known
      "mobile":"017623648372",    # contact mobile phone number or null if they have none
      "deleted":false             # true if the customer has been deleted, otherwise false
    }
    "services": [                                 # Optional. Can be empty though. Exists since V3.2.
      {
        "id":"the-service-id",      # Service#id
        "name":"Bikini Waxing",     # Service#name
      }
    ],
    "resources": [                                # Optional. Can be empty though. Exists since V3.1.
      {
        "id":1234,        # Resource#id
        "name":"Table 2", # Resource#name
      }
    ],
    "steps": [                                    # Optional. Can be empty though or can also be null when
                                                  # appointment was created without steps. Exists since V3.2.
      {
        "type": 'work',                   # There are 4 types of steps:
                                          #   1) 'pre_processing'. Must comes first. Preparation step to start 'work'.
                                          #   2) [default] 'work'. If no types specified than 'work' used as default.
                                          #                        Marks the start and end of the appointment for the customer.
                                          #   3) 'break'. Doesn't change the availability of the resource.
                                          #   4) 'post_processing' Must comes last. Step after 'work' finished.
        "duration": 30,                   # ServiceStep#duration in minutes. Default value is 30 minutes.
        "name": 'Step Name',              # ServiceStep#name. Default value is empty string.
        "resource_ids": ['1234', '5678']  # Default value is empty array.
                                          # '1234' and '5678' are Resource#id. step#resource_ids are no such as ids in appointment#resources

      }
    ]
  }
}
```

Show the appointment with the given id.

### Request URL
`https://api.shore.com/api/merchant/appointments/:id`

### Request parameters
<table class="attributes">
  <tr>
    <td>id<div>String, <span class="req">Required</span></div></td>
    <td>ID of the Appointment to show</td>
  </tr>
</table>

### Request Headers
<table class="attributes">
  <tr>
    <td>Accept<div>String, <span class="req">Required</span></div></td>
    <td>The api version to call.</td>
  </tr>
  <tr>
    <td>client_id<div>String, <span class="req">Required</span></div></td>
    <td>The client identifier</td>
  </tr>
  <tr>
    <td>client_secret<div>String, <span class="req">Required</span></div></td>
    <td>The client secret (see ENV['API_CLIENT_SECRET'])</td>
  </tr>
  <tr>
    <td>client_bundle_identifier<div>String, <span class="req">Required</span></div></td>
    <td>The unique identifier for the client (e.g. iOS bundle identifier)</td>
  </tr>
  <tr>
    <td>client_version<div>String, <span class="req">Required</span></div></td>
    <td>The version of the client.</td>
  </tr>
  <tr>
    <td>Authorization<div>String, <span class="req">Required</span></div></td>
    <td>The MerchantAccount#authentication_token of the current merchant account.</td>
  </tr>
  <tr>
    <td>service_provider_id<div>Integer, <span class="opt">Optional</span></div></td>
    <td>The id or slug of the MerchantProfile
        to use for this request as the current profile.
        Default is the first profile created.</td>
  </tr>
</table>

### Response Code
* `200` Success
* `404` Appointment with the given id does not exist
* `401` Authorization token is missing or is not valid
* `401` Client headers are missing or not valid

## PUT /api/merchant/appointments/:id

> Example Request

```language-curl
$ curl https://api.shore.com/api/merchant/appointments/:id \
  -X PUT
  -H 'Accept: application/vnd.termine24.de; version=3'
  -H 'client_id: de.termine24.dienstleister.ios.1'
  -H 'client_secret: secret'
  -H 'client_bundle_identifier: de.termine24.dienstleister'
  -H 'client_version: 1.0.0'
  -H 'Authorization: Token token=jFjNCbGUs3q7wzxc7cPb'
  -H 'service_provider_id: the-merchant-id'
```

> Example Response

```language-javascript
{
  "appointment": {
    "id":1234,
    "phase": 1,                             # 0 = open, 1 = will take place, 2 = will not take place or already did take place
    "accepted_state": 10,                   # 10 (pending), 11 (accepted), 12 (cancelled)
    "action_required_by": 0,                # 0 = no action required, 1 = merchant action required, 2 = customer action required
    "human_state": "Termin findet statt"    # Localized description of the appointment's
                                            # current state from the merchant's perspective
    "datetime":"2014-07-16T19:20:00+02:00",
    "datetime_end":"2014-07-16T19:50:00+02:00",
    "duration": 30,
    "pre_duration": 0,
    "post_duration": 15,
    "service_name": "Reservierung, Anderes"   # Names of the services booked or null if none booked
    "human_price": "638,95 €",                # Price as formatted string or null if free
    "color": null,                            # Color assigned to the Appointment. "/#[0-9a-f]{6}/i" or "null".
    "attachments": ["123"],                   # JSON array of attachment ids from FSS.
    "attachments_info": [                         # Array of attachments with meta information about each attachment
      {
        "id": "123"                               # id of attachment
        "filename": "image.jpg",                  # filename
        "url": "http://fss.shore.com/image.jpg",  # url to the uploaded file (you will be redirected to amazon afterwards)
        "attachment_label": "Attachments",        # label to show for attachment
        "deletable": false                        # is it allowed to delete this attachment
      },
      ...
    ],
    "human_additional_information": {         # Mandatory. Can be {}. Keys are not predefined.
                                              # This is just an example of possible keys.
      "required_capacity": {                  # Each key always has a non-localized key
        "human_key": "Personenzahl",          # Each key always has the localized key
        "value": "5 Personen"               # Each key always has the string value
      },
      "subject": {                            # This is a commonly defined key. It
        "human_key": "Betreff",               # is optionally set when the merchant
        "value=>"the subject"                 # creates an appointment
      }
      "reason_for_booking": {                 # This is a commonly defined key. It
        "human_key": "Termindetails",         # is optionally set when the customer
        "value": "I want to celebrate party." # creates an appointment
      },
      ... Different keys or additional keys may be present ...
    },
    "customer": {
      "id":1234,                  # MerchantCustomer#id
      "uuid":1234,                # MerchantCustomer#uuid
      "title":"Herr",             # localized title (e.g. Herr/Frau) or null if title not known
      "first_name":"Max",         # first name or null if not known
      "last_name":"Mustermann",   # last name
      "email":"max@mail.de",      # contact email or null if not known
      "mobile":"017623648372",    # contact mobile phone number or null if they have none
      "deleted":false             # true if the customer has been deleted, otherwise false
    }
    "services": [                                 # Optional. Can be empty though. Exists since V3.2.
      {
        "id":"the-service-id",      # Service#id
        "name":"Bikini Waxing",     # Service#name
      }
    ],
    "resources": [                                # Optional. Can be empty though. Exists since V3.1.
      {
        "id":1234,        # Resource#id
        "name":"Table 2", # Resource#name
      }
    ],
    "steps": [                                    # Optional. Can be empty though or can also be null when
                                                  # appointment was created without steps. Exists since V3.2.
      {
        "type": 'work',                   # There are 4 types of steps:
                                          #   1) 'pre_processing'. Must comes first. Preparation step to start 'work'.
                                          #   2) [default] 'work'. If no types specified than 'work' used as default.
                                          #                        Marks the start and end of the appointment for the customer.
                                          #   3) 'break'. Doesn't change the availability of the resource.
                                          #   4) 'post_processing' Must comes last. Step after 'work' finished.
        "duration": 30,                   # ServiceStep#duration in minutes. Default value is 30 minutes.
        "name": 'Step Name',              # ServiceStep#name. Default value is empty string.
        "resource_ids": ['1234', '5678']  # Default value is empty array.
                                          # '1234' and '5678' are Resource#id. step#resource_ids are no such as ids in appointment#resources

      }
    ]
  }
}
```

Update an appointment with the given parameters.

### Request URL
`https://api.shore.com/api/merchant/appointments/:id`

### Request parameters
<table class="attributes">
  <tr>
    <td>datetime<div>String, <span class="req">Required</span></div></td>
    <td>Mandatory: Date + time of appointment in ISO8601
        format (see http://www.w3.org/TR/NOTE-datetime)</td>
  </tr>
  <tr>
    <td>service_ids<div>JSON Array, <span class="opt">Optional</span></div></td>
    <td>list of service ids {Service} performed during the appointment.
        See {Api::Merchant::V3::ServicesController#index}.
        Appointment duration, pre-duration and post-duration are set to
        this service's values unless otherwise specified in the parameters.</td>
  </tr>
  <tr>
    <td>duration<div>Integer, <span class="opt">Optional</span></div></td>
    <td>Duration of the appointment in minutes. Defaults to duration
        of the given service or 60 if no service defined.</td>
  </tr>
  <tr>
    <td>required_capacity<div>Integer, <span class="opt">Optional</span></div></td>
    <td>Number of participants for the appointment (positive integer).</td>
  </tr>
  <tr>
    <td>subject<div>String, <span class="opt">Optional</span></div></td>
    <td>Text description of the appointment.
        should receive a reminder via the merchant's notification
        channels. Default is "24".</td>
  </tr>
  <tr>
    <td>attachments<div>JSON Array, <span class="opt">Optional</span></div></td>
    <td>JSON array of attachment ids recieved from FSS.</td>
  </tr>
  <tr>
    <td>resource_ids<div>JSON Array, <span class="opt">Optional</span></div></td>
    <td>JSON array of {Resource#id}s.</td>
  </tr>
  <tr>
    <td>customer_id<div>String, <span class="opt">Optional</span></div></td>
    <td>{MerchantCustomer#id} of the customer for the appointment</td>
  </tr>
  <tr>
    <td>participating_account_ids<div>JSON Array, <span class="opt">Optional</span></div></td>
    <td>list of participating account ids</td>
  </tr>
  <tr>
    <td>color<div>String, <span class="opt">Optional</span></div></td>
    <td>String representing the HEX color code</td>
  </tr>
  <tr>
    <td>inform_customer<div>Boolean, <span class="opt">Optional</span></div></td>
    <td>If "true", a message will be sent to the appointment's
        customer's notification channels.</td>
  </tr>
  <tr>
    <td>customer[last_name]<div>String, <span class="opt">Optional</span></div></td>
    <td>Customer's last name. This and the following customer[*] parameters add
        this customer data to the appointment. Additionally,
        if the customer_id is given then this data is merged with the existing data for that MerchantCustomer.
        If no customer_id is given and no matching MerchantCustomer can be found, then a new
        MerchantCustomer is created.</td>
  </tr>
  <tr>
    <td>customer[first_name]<div>String, <span class="opt">Optional</span></div></td>
    <td>Customer's first name</td>
  </tr>
  <tr>
    <td>customer[gender]<div>String, <span class="opt">Optional</span></div></td>
    <td>Customer's gender "male" or "female"</td>
  </tr>
  <tr>
    <td>customer[email]<div>String, <span class="opt">Optional</span></div></td>
    <td>Customer's email address</td>
  </tr>
  <tr>
    <td>customer[mobile]<div>String, <span class="opt">Optional</span></div></td>
    <td>Customer's mobile phone number</td>
  </tr>
</table>

### Request Headers
<table class="attributes">
  <tr>
    <td>Accept<div>String, <span class="req">Required</span></div></td>
    <td>The api version to call.</td>
  </tr>
  <tr>
    <td>client_id<div>String, <span class="req">Required</span></div></td>
    <td>The client identifier</td>
  </tr>
  <tr>
    <td>client_secret<div>String, <span class="req">Required</span></div></td>
    <td>The client secret (see ENV['API_CLIENT_SECRET'])</td>
  </tr>
  <tr>
    <td>client_bundle_identifier<div>String, <span class="req">Required</span></div></td>
    <td>The unique identifier for the client (e.g. iOS bundle identifier)</td>
  </tr>
  <tr>
    <td>client_version<div>String, <span class="req">Required</span></div></td>
    <td>The version of the client.</td>
  </tr>
  <tr>
    <td>Authorization<div>String, <span class="req">Required</span></div></td>
    <td>The MerchantAccount#authentication_token of the current merchant account.</td>
  </tr>
  <tr>
    <td>service_provider_id<div>Integer, <span class="opt">Optional</span></div></td>
    <td>The id or slug of the MerchantProfile
        to use for this request as the current profile.
        Default is the first profile created.</td>
  </tr>
</table>

### Response Code
* `200` Success
* `400` Appointment could not be updated
* `404` Appointment with the given id does not exist
* `401` Authorization token is missing or is not valid
* `401` Client headers are missing or not valid

## POST /api/merchant/customers/

> Example Request

```language-curl
$ curl https://api.shore.com/api/merchant/customers \
  -H 'Accept: application/vnd.termine24.de; version=3'
  -H 'client_id: de.termine24.dienstleister.ios.1'
  -H 'client_secret: secret'
  -H 'client_bundle_identifier: de.termine24.dienstleister'
  -H 'client_version: 1.0.0'
  -H 'Authorization: Token token=jFjNCbGUs3q7wzxc7cPb'
  -H 'service_provider_id: the-merchant-id'
```

> Example Response

```language-javascript
{
  "appointment": {
    "id":1234,
    "phase": 1,                             # 0 = open, 1 = will take place, 2 = will not take place or already did take place
    "accepted_state": 10,                   # 10 (pending), 11 (accepted), 12 (cancelled)
    "action_required_by": 0,                # 0 = no action required, 1 = merchant action required, 2 = customer action required
    "human_state": "Termin findet statt"    # Localized description of the appointment's
                                            # current state from the merchant's perspective
    "datetime":"2014-07-16T19:20:00+02:00",
    "datetime_end":"2014-07-16T19:50:00+02:00",
    "duration": 30,
    "pre_duration": 0,
    "post_duration": 15,
    "service_name": "Reservierung, Anderes"   # Names of the services booked or null if none booked
    "human_price": "638,95 €",                # Price as formatted string or null if free
    "color": null,                            # Color assigned to the Appointment. "/#[0-9a-f]{6}/i" or "null".
    "attachments": ["123"],                   # JSON array of attachment ids from FSS.
    "attachments_info": [                         # Array of attachments with meta information about each attachment
      {
        "id": "123"                               # id of attachment
        "filename": "image.jpg",                  # filename
        "url": "http://fss.shore.com/image.jpg",  # url to the uploaded file (you will be redirected to amazon afterwards)
        "attachment_label": "Attachments",        # label to show for attachment
        "deletable": false                        # is it allowed to delete this attachment
      },
      ...
    ],
    "human_additional_information": {         # Mandatory. Can be {}. Keys are not predefined.
                                              # This is just an example of possible keys.
      "required_capacity": {                  # Each key always has a non-localized key
        "human_key": "Personenzahl",          # Each key always has the localized key
        "value": "5 Personen"               # Each key always has the string value
      },
      "subject": {                            # This is a commonly defined key. It
        "human_key": "Betreff",               # is optionally set when the merchant
        "value=>"the subject"                 # creates an appointment
      }
      "reason_for_booking": {                 # This is a commonly defined key. It
        "human_key": "Termindetails",         # is optionally set when the customer
        "value": "I want to celebrate party." # creates an appointment
      },
      ... Different keys or additional keys may be present ...
    },
    "customer": {
      "id":1234,                  # MerchantCustomer#id
      "uuid":1234,                # MerchantCustomer#uuid
      "title":"Herr",             # localized title (e.g. Herr/Frau) or null if title not known
      "first_name":"Max",         # first name or null if not known
      "last_name":"Mustermann",   # last name
      "email":"max@mail.de",      # contact email or null if not known
      "mobile":"017623648372",    # contact mobile phone number or null if they have none
      "deleted":false             # true if the customer has been deleted, otherwise false
    }
    "services": [                                 # Optional. Can be empty though. Exists since V3.2.
      {
        "id":"the-service-id",      # Service#id
        "name":"Bikini Waxing",     # Service#name
      }
    ],
    "resources": [                                # Optional. Can be empty though. Exists since V3.1.
      {
        "id":1234,        # Resource#id
        "name":"Table 2", # Resource#name
      }
    ],
    "steps": [                                    # Optional. Can be empty though or can also be null when
                                                  # appointment was created without steps. Exists since V3.2.
      {
        "type": 'work',                   # There are 4 types of steps:
                                          #   1) 'pre_processing'. Must comes first. Preparation step to start 'work'.
                                          #   2) [default] 'work'. If no types specified than 'work' used as default.
                                          #                        Marks the start and end of the appointment for the customer.
                                          #   3) 'break'. Doesn't change the availability of the resource.
                                          #   4) 'post_processing' Must comes last. Step after 'work' finished.
        "duration": 30,                   # ServiceStep#duration in minutes. Default value is 30 minutes.
        "name": 'Step Name',              # ServiceStep#name. Default value is empty string.
        "resource_ids": ['1234', '5678']  # Default value is empty array.
                                          # '1234' and '5678' are Resource#id. step#resource_ids are no such as ids in appointment#resources

      }
    ]
  }
}
```

Create a new customer with the given parameters.

### Request URL
`https://api.shore.com/api/merchant/customers`

### Request parameters
<table class="attributes">
  <tr>
	<td>last_name: "Mueller"<div>String, <span class="req"># Mandatory if first_name blank: last name</span></div></td>
	<td><div>String, <span class="opt">first_name: "Thomas"  # Optional: first name.</span></div></td>
	<td><div>String, <span class="opt">gender: "male"        # Optional: "male" or "female"</span></div></td>
	<td><div>String, <span class="opt">email: "t.m@mail.com" # Optional: email address</span></div></td>
	<td><div>String, <span class="opt">mobile: "01762378232" # Optional: mobile phone number</span></div></td>
  </tr>
</table>

### Request Headers
<table class="attributes">
  <tr>
    <td>Accept<div>String, <span class="req">Required</span></div></td>
    <td>The api version to call.</td>
  </tr>
  <tr>
    <td>client_id<div>String, <span class="req">Required</span></div></td>
    <td>The client identifier</td>
  </tr>
  <tr>
    <td>client_secret<div>String, <span class="req">Required</span></div></td>
    <td>The client secret (see ENV['API_CLIENT_SECRET'])</td>
  </tr>
  <tr>
    <td>client_bundle_identifier<div>String, <span class="req">Required</span></div></td>
    <td>The unique identifier for the client (e.g. iOS bundle identifier)</td>
  </tr>
  <tr>
    <td>client_version<div>String, <span class="req">Required</span></div></td>
    <td>The version of the client.</td>
  </tr>
  <tr>
    <td>Authorization<div>String, <span class="req">Required</span></div></td>
    <td>The MerchantAccount#authentication_token of the current merchant account.</td>
  </tr>
  <tr>
    <td>service_provider_id<div>Integer, <span class="opt">Optional</span></div></td>
    <td>The id or slug of the MerchantProfile
        to use for this request as the current profile.
        Default is the first profile created.</td>
  </tr>
</table>

### Response Code
* `200` Success
* `400` Request parameters are missing or do not make a valid MerchantCustomer
* `401` Authorization token is missing or is not valid
* `401` Client headers are missing or not valid

## GET /api/merchant/customers

> Example Request

```language-curl
$ curl https://api.shore.com/api/merchant/customers \
  -H 'Accept: application/vnd.termine24.de; version=3'
  -H 'client_id: de.termine24.dienstleister.ios.1'
  -H 'client_secret: secret'
  -H 'client_bundle_identifier: de.termine24.dienstleister'
  -H 'client_version: 1.0.0'
  -H 'Authorization: Token token=jFjNCbGUs3q7wzxc7cPb'
  -H 'service_provider_id: the-merchant-id'
```

> Example Response

```language-javascript
{
  "appointment": {
    "id":1234,
    "phase": 1,                             # 0 = open, 1 = will take place, 2 = will not take place or already did take place
    "accepted_state": 10,                   # 10 (pending), 11 (accepted), 12 (cancelled)
    "action_required_by": 0,                # 0 = no action required, 1 = merchant action required, 2 = customer action required
    "human_state": "Termin findet statt"    # Localized description of the appointment's
                                            # current state from the merchant's perspective
    "datetime":"2014-07-16T19:20:00+02:00",
    "datetime_end":"2014-07-16T19:50:00+02:00",
    "duration": 30,
    "pre_duration": 0,
    "post_duration": 15,
    "service_name": "Reservierung, Anderes"   # Names of the services booked or null if none booked
    "human_price": "638,95 €",                # Price as formatted string or null if free
    "color": null,                            # Color assigned to the Appointment. "/#[0-9a-f]{6}/i" or "null".
    "attachments": ["123"],                   # JSON array of attachment ids from FSS.
    "attachments_info": [                         # Array of attachments with meta information about each attachment
      {
        "id": "123"                               # id of attachment
        "filename": "image.jpg",                  # filename
        "url": "http://fss.shore.com/image.jpg",  # url to the uploaded file (you will be redirected to amazon afterwards)
        "attachment_label": "Attachments",        # label to show for attachment
        "deletable": false                        # is it allowed to delete this attachment
      },
      ...
    ],
    "human_additional_information": {         # Mandatory. Can be {}. Keys are not predefined.
                                              # This is just an example of possible keys.
      "required_capacity": {                  # Each key always has a non-localized key
        "human_key": "Personenzahl",          # Each key always has the localized key
        "value": "5 Personen"               # Each key always has the string value
      },
      "subject": {                            # This is a commonly defined key. It
        "human_key": "Betreff",               # is optionally set when the merchant
        "value=>"the subject"                 # creates an appointment
      }
      "reason_for_booking": {                 # This is a commonly defined key. It
        "human_key": "Termindetails",         # is optionally set when the customer
        "value": "I want to celebrate party." # creates an appointment
      },
      ... Different keys or additional keys may be present ...
    },
    "customer": {
      "id":1234,                  # MerchantCustomer#id
      "uuid":1234,                # MerchantCustomer#uuid
      "title":"Herr",             # localized title (e.g. Herr/Frau) or null if title not known
      "first_name":"Max",         # first name or null if not known
      "last_name":"Mustermann",   # last name
      "email":"max@mail.de",      # contact email or null if not known
      "mobile":"017623648372",    # contact mobile phone number or null if they have none
      "deleted":false             # true if the customer has been deleted, otherwise false
    }
    "services": [                                 # Optional. Can be empty though. Exists since V3.2.
      {
        "id":"the-service-id",      # Service#id
        "name":"Bikini Waxing",     # Service#name
      }
    ],
    "resources": [                                # Optional. Can be empty though. Exists since V3.1.
      {
        "id":1234,        # Resource#id
        "name":"Table 2", # Resource#name
      }
    ],
    "steps": [                                    # Optional. Can be empty though or can also be null when
                                                  # appointment was created without steps. Exists since V3.2.
      {
        "type": 'work',                   # There are 4 types of steps:
                                          #   1) 'pre_processing'. Must comes first. Preparation step to start 'work'.
                                          #   2) [default] 'work'. If no types specified than 'work' used as default.
                                          #                        Marks the start and end of the appointment for the customer.
                                          #   3) 'break'. Doesn't change the availability of the resource.
                                          #   4) 'post_processing' Must comes last. Step after 'work' finished.
        "duration": 30,                   # ServiceStep#duration in minutes. Default value is 30 minutes.
        "name": 'Step Name',              # ServiceStep#name. Default value is empty string.
        "resource_ids": ['1234', '5678']  # Default value is empty array.
                                          # '1234' and '5678' are Resource#id. step#resource_ids are no such as ids in appointment#resources

      }
    ]
  }
}
```

List this merchant's MerchantCustomers sorted alphabetically by first name then last name.

### Request URL
`https://api.shore.com/api/merchant/customers`

### Request parameters
<table class="attributes">
  <tr>
	<td><div>String, <span class="opt">updated_since: "2013-07-16T19:20+00:00"  # Optional. Only return customers updated after</span></div></td>
    <td>#  this datetime in ISO8601 format</td>
    <td>#  (see http://www.w3.org/TR/NOTE-datetime). Default</td>
    <td>#  is "" so all customers are returned. Pass the</td>
    <td>#  meta/pagination/last_page_requested_at value here</td>
    <td>#  when it exists from the last call to only get</td>
    <td>#  the customers that have been updated since the</td>
    <td>#  last time the client retrieved the entire customer list.</td>
	<td><div>String, <span class="opt">page: "1"           # Optional. The current page of the results. Default is "1"</span></div></td>
	<td><div>String, <span class="opt">per_page: "10"      # Optional. The number of results per page. Default is "10". Set</span></div></td>
    <td>#  to "0" for unlimited results per page.</td>
	<td><div>String, <span class="opt">with_deleted: "false" # Optional. If "true" AND "updated_since" is not blank, then deleted</span></div></td>
    <td>#  MerchantCustomers are also returned by this call. This is useful</td>
    <td>#  if the client is maintaining a cache of the customers. This makes</td>
    <td>#  it possible for the client to keep their cache in sync with the</td>
    <td>#  server. New clients should always set this to true and handle</td>
    <td>#  the new "deleted" flag on the CustomerSerializer. Since this</td>
    <td>#  parameter only makes sense for updating a client cache, it only</td>
    <td>#  only works when updated_since is not blank. Default is "false" and</td>
    <td>#  value forced to "false" if "updated_since" is blank.</td>
  </tr>
</table>

### Request Headers
<table class="attributes">
  <tr>
    <td>Accept<div>String, <span class="req">Required</span></div></td>
    <td>The api version to call.</td>
  </tr>
  <tr>
    <td>client_id<div>String, <span class="req">Required</span></div></td>
    <td>The client identifier</td>
  </tr>
  <tr>
    <td>client_secret<div>String, <span class="req">Required</span></div></td>
    <td>The client secret (see ENV['API_CLIENT_SECRET'])</td>
  </tr>
  <tr>
    <td>client_bundle_identifier<div>String, <span class="req">Required</span></div></td>
    <td>The unique identifier for the client (e.g. iOS bundle identifier)</td>
  </tr>
  <tr>
    <td>client_version<div>String, <span class="req">Required</span></div></td>
    <td>The version of the client.</td>
  </tr>
  <tr>
    <td>Authorization<div>String, <span class="req">Required</span></div></td>
    <td>The MerchantAccount#authentication_token of the current merchant account.</td>
  </tr>
  <tr>
    <td>service_provider_id<div>Integer, <span class="opt">Optional</span></div></td>
    <td>The id or slug of the MerchantProfile
        to use for this request as the current profile.
        Default is the first profile created.</td>
  </tr>
</table>

### Response Code
* `200` Success
* `400` Given parameters are not valid (e.g. update_since not a valid ISO8601 datetime)
* `401` Authorization token is missing or is not valid
* `401` Client headers are missing or not valid

## GET /api/merchant/customers/:id

> Example Request

```language-curl
$ curl https://api.shore.com/api/merchant/customers/:id \
  -H 'Accept: application/vnd.termine24.de; version=3'
  -H 'client_id: de.termine24.dienstleister.ios.1'
  -H 'client_secret: secret'
  -H 'client_bundle_identifier: de.termine24.dienstleister'
  -H 'client_version: 1.0.0'
  -H 'Authorization: Token token=jFjNCbGUs3q7wzxc7cPb'
  -H 'service_provider_id: the-merchant-id'
```

> Example Response

```language-javascript
{
  "appointment": {
    "id":1234,
    "phase": 1,                             # 0 = open, 1 = will take place, 2 = will not take place or already did take place
    "accepted_state": 10,                   # 10 (pending), 11 (accepted), 12 (cancelled)
    "action_required_by": 0,                # 0 = no action required, 1 = merchant action required, 2 = customer action required
    "human_state": "Termin findet statt"    # Localized description of the appointment's
                                            # current state from the merchant's perspective
    "datetime":"2014-07-16T19:20:00+02:00",
    "datetime_end":"2014-07-16T19:50:00+02:00",
    "duration": 30,
    "pre_duration": 0,
    "post_duration": 15,
    "service_name": "Reservierung, Anderes"   # Names of the services booked or null if none booked
    "human_price": "638,95 €",                # Price as formatted string or null if free
    "color": null,                            # Color assigned to the Appointment. "/#[0-9a-f]{6}/i" or "null".
    "attachments": ["123"],                   # JSON array of attachment ids from FSS.
    "attachments_info": [                         # Array of attachments with meta information about each attachment
      {
        "id": "123"                               # id of attachment
        "filename": "image.jpg",                  # filename
        "url": "http://fss.shore.com/image.jpg",  # url to the uploaded file (you will be redirected to amazon afterwards)
        "attachment_label": "Attachments",        # label to show for attachment
        "deletable": false                        # is it allowed to delete this attachment
      },
      ...
    ],
    "human_additional_information": {         # Mandatory. Can be {}. Keys are not predefined.
                                              # This is just an example of possible keys.
      "required_capacity": {                  # Each key always has a non-localized key
        "human_key": "Personenzahl",          # Each key always has the localized key
        "value": "5 Personen"               # Each key always has the string value
      },
      "subject": {                            # This is a commonly defined key. It
        "human_key": "Betreff",               # is optionally set when the merchant
        "value=>"the subject"                 # creates an appointment
      }
      "reason_for_booking": {                 # This is a commonly defined key. It
        "human_key": "Termindetails",         # is optionally set when the customer
        "value": "I want to celebrate party." # creates an appointment
      },
      ... Different keys or additional keys may be present ...
    },
    "customer": {
      "id":1234,                  # MerchantCustomer#id
      "uuid":1234,                # MerchantCustomer#uuid
      "title":"Herr",             # localized title (e.g. Herr/Frau) or null if title not known
      "first_name":"Max",         # first name or null if not known
      "last_name":"Mustermann",   # last name
      "email":"max@mail.de",      # contact email or null if not known
      "mobile":"017623648372",    # contact mobile phone number or null if they have none
      "deleted":false             # true if the customer has been deleted, otherwise false
    }
    "services": [                                 # Optional. Can be empty though. Exists since V3.2.
      {
        "id":"the-service-id",      # Service#id
        "name":"Bikini Waxing",     # Service#name
      }
    ],
    "resources": [                                # Optional. Can be empty though. Exists since V3.1.
      {
        "id":1234,        # Resource#id
        "name":"Table 2", # Resource#name
      }
    ],
    "steps": [                                    # Optional. Can be empty though or can also be null when
                                                  # appointment was created without steps. Exists since V3.2.
      {
        "type": 'work',                   # There are 4 types of steps:
                                          #   1) 'pre_processing'. Must comes first. Preparation step to start 'work'.
                                          #   2) [default] 'work'. If no types specified than 'work' used as default.
                                          #                        Marks the start and end of the appointment for the customer.
                                          #   3) 'break'. Doesn't change the availability of the resource.
                                          #   4) 'post_processing' Must comes last. Step after 'work' finished.
        "duration": 30,                   # ServiceStep#duration in minutes. Default value is 30 minutes.
        "name": 'Step Name',              # ServiceStep#name. Default value is empty string.
        "resource_ids": ['1234', '5678']  # Default value is empty array.
                                          # '1234' and '5678' are Resource#id. step#resource_ids are no such as ids in appointment#resources

      }
    ]
  }
}
```

Show the merchant's MerchantCustomer with the given MerchantCustomer#id.

### Request URL
`https://api.shore.com/api/merchant/customers/:id`

### Request parameters
<table class="attributes">
  <tr>
	<td>id=1234<div>String, <span class="req"># Mandatory. MerchantCustomer#id. Customer must belong to the current merchant!</span></div></td>
	<td><div>String, <span class="opt">with_deleted: "false" # Optional. If "true", then a deleted MerchantCustomer is also</span></div></td>
    <td>#  returned by this call. Default is "false".</td>
  </tr>
</table>

### Request Headers
<table class="attributes">
  <tr>
    <td>Accept<div>String, <span class="req">Required</span></div></td>
    <td>The api version to call.</td>
  </tr>
  <tr>
    <td>client_id<div>String, <span class="req">Required</span></div></td>
    <td>The client identifier</td>
  </tr>
  <tr>
    <td>client_secret<div>String, <span class="req">Required</span></div></td>
    <td>The client secret (see ENV['API_CLIENT_SECRET'])</td>
  </tr>
  <tr>
    <td>client_bundle_identifier<div>String, <span class="req">Required</span></div></td>
    <td>The unique identifier for the client (e.g. iOS bundle identifier)</td>
  </tr>
  <tr>
    <td>client_version<div>String, <span class="req">Required</span></div></td>
    <td>The version of the client.</td>
  </tr>
  <tr>
    <td>Authorization<div>String, <span class="req">Required</span></div></td>
    <td>The MerchantAccount#authentication_token of the current merchant account.</td>
  </tr>
  <tr>
    <td>service_provider_id<div>Integer, <span class="opt">Optional</span></div></td>
    <td>The id or slug of the MerchantProfile
        to use for this request as the current profile.
        Default is the first profile created.</td>
  </tr>
</table>

### Response Code
* `200` Success
* `401` Authorization token is missing or is not valid
* `401` Client headers are missing or not valid
* `404` MerchantCustomer with the given ID not found

## PUT /api/merchant/customers/:id

> Example Request

```language-curl
$ curl https://api.shore.com/api/merchant/customers/:id \
  -H 'Accept: application/vnd.termine24.de; version=3'
  -H 'client_id: de.termine24.dienstleister.ios.1'
  -H 'client_secret: secret'
  -H 'client_bundle_identifier: de.termine24.dienstleister'
  -H 'client_version: 1.0.0'
  -H 'Authorization: Token token=jFjNCbGUs3q7wzxc7cPb'
  -H 'service_provider_id: the-merchant-id'
```

> Example Response

```language-javascript
{
  "appointment": {
    "id":1234,
    "phase": 1,                             # 0 = open, 1 = will take place, 2 = will not take place or already did take place
    "accepted_state": 10,                   # 10 (pending), 11 (accepted), 12 (cancelled)
    "action_required_by": 0,                # 0 = no action required, 1 = merchant action required, 2 = customer action required
    "human_state": "Termin findet statt"    # Localized description of the appointment's
                                            # current state from the merchant's perspective
    "datetime":"2014-07-16T19:20:00+02:00",
    "datetime_end":"2014-07-16T19:50:00+02:00",
    "duration": 30,
    "pre_duration": 0,
    "post_duration": 15,
    "service_name": "Reservierung, Anderes"   # Names of the services booked or null if none booked
    "human_price": "638,95 €",                # Price as formatted string or null if free
    "color": null,                            # Color assigned to the Appointment. "/#[0-9a-f]{6}/i" or "null".
    "attachments": ["123"],                   # JSON array of attachment ids from FSS.
    "attachments_info": [                         # Array of attachments with meta information about each attachment
      {
        "id": "123"                               # id of attachment
        "filename": "image.jpg",                  # filename
        "url": "http://fss.shore.com/image.jpg",  # url to the uploaded file (you will be redirected to amazon afterwards)
        "attachment_label": "Attachments",        # label to show for attachment
        "deletable": false                        # is it allowed to delete this attachment
      },
      ...
    ],
    "human_additional_information": {         # Mandatory. Can be {}. Keys are not predefined.
                                              # This is just an example of possible keys.
      "required_capacity": {                  # Each key always has a non-localized key
        "human_key": "Personenzahl",          # Each key always has the localized key
        "value": "5 Personen"               # Each key always has the string value
      },
      "subject": {                            # This is a commonly defined key. It
        "human_key": "Betreff",               # is optionally set when the merchant
        "value=>"the subject"                 # creates an appointment
      }
      "reason_for_booking": {                 # This is a commonly defined key. It
        "human_key": "Termindetails",         # is optionally set when the customer
        "value": "I want to celebrate party." # creates an appointment
      },
      ... Different keys or additional keys may be present ...
    },
    "customer": {
      "id":1234,                  # MerchantCustomer#id
      "uuid":1234,                # MerchantCustomer#uuid
      "title":"Herr",             # localized title (e.g. Herr/Frau) or null if title not known
      "first_name":"Max",         # first name or null if not known
      "last_name":"Mustermann",   # last name
      "email":"max@mail.de",      # contact email or null if not known
      "mobile":"017623648372",    # contact mobile phone number or null if they have none
      "deleted":false             # true if the customer has been deleted, otherwise false
    }
    "services": [                                 # Optional. Can be empty though. Exists since V3.2.
      {
        "id":"the-service-id",      # Service#id
        "name":"Bikini Waxing",     # Service#name
      }
    ],
    "resources": [                                # Optional. Can be empty though. Exists since V3.1.
      {
        "id":1234,        # Resource#id
        "name":"Table 2", # Resource#name
      }
    ],
    "steps": [                                    # Optional. Can be empty though or can also be null when
                                                  # appointment was created without steps. Exists since V3.2.
      {
        "type": 'work',                   # There are 4 types of steps:
                                          #   1) 'pre_processing'. Must comes first. Preparation step to start 'work'.
                                          #   2) [default] 'work'. If no types specified than 'work' used as default.
                                          #                        Marks the start and end of the appointment for the customer.
                                          #   3) 'break'. Doesn't change the availability of the resource.
                                          #   4) 'post_processing' Must comes last. Step after 'work' finished.
        "duration": 30,                   # ServiceStep#duration in minutes. Default value is 30 minutes.
        "name": 'Step Name',              # ServiceStep#name. Default value is empty string.
        "resource_ids": ['1234', '5678']  # Default value is empty array.
                                          # '1234' and '5678' are Resource#id. step#resource_ids are no such as ids in appointment#resources

      }
    ]
  }
}
```

Updates an existing customer with the given parameters.

### Request URL
`https://api.shore.com/api/merchant/customers/:id`

### Request parameters
<table class="attributes">
  <tr>
	<td>id=1234<div>String, <span class="req"># Mandatory. MerchantCustomer#id. Customer must belong to the current merchant!</span></div></td>
	<td><div>String, <span class="opt">last_name: "Mueller"  # Optional: last name</span></div></td>
	<td><div>String, <span class="opt">first_name: "Thomas"  # Optional: first name</span></div></td>
	<td><div>String, <span class="opt">gender: "male"        # Optional: "male" or "female"</span></div></td>
	<td><div>String, <span class="opt">email: "t.m@mail.com" # Optional: email address</span></div></td>
	<td><div>String, <span class="opt">mobile: "01762378232" # Optional: mobile phone number</span></div></td>
  </tr>
</table>

### Request Headers
<table class="attributes">
  <tr>
    <td>Accept<div>String, <span class="req">Required</span></div></td>
    <td>The api version to call.</td>
  </tr>
  <tr>
    <td>client_id<div>String, <span class="req">Required</span></div></td>
    <td>The client identifier</td>
  </tr>
  <tr>
    <td>client_secret<div>String, <span class="req">Required</span></div></td>
    <td>The client secret (see ENV['API_CLIENT_SECRET'])</td>
  </tr>
  <tr>
    <td>client_bundle_identifier<div>String, <span class="req">Required</span></div></td>
    <td>The unique identifier for the client (e.g. iOS bundle identifier)</td>
  </tr>
  <tr>
    <td>client_version<div>String, <span class="req">Required</span></div></td>
    <td>The version of the client.</td>
  </tr>
  <tr>
    <td>Authorization<div>String, <span class="req">Required</span></div></td>
    <td>The MerchantAccount#authentication_token of the current merchant account.</td>
  </tr>
  <tr>
    <td>service_provider_id<div>Integer, <span class="opt">Optional</span></div></td>
    <td>The id or slug of the MerchantProfile
        to use for this request as the current profile.
        Default is the first profile created.</td>
  </tr>
</table>

### Response Code
* `200` Success
* `400` Request parameters are missing or do not make a valid MerchantCustomer
* `401` Authorization token is missing or is not valid
* `401` Client headers are missing or not valid