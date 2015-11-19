# Merchant API

Shore provides an API specifically for the merchants so that
they can manage their accounts, branches, appointments,
customers, etc..

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

POST /api/merchant/appointments/

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

List this merchant's appointments sorted by the Appointment#datetime oldest first.

### Request URL
`https://api.shore.com/api/merchant/appointments`

### Request parameters


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

## PUT /api/merchant/appointments/:id/reject

> Example Request

```language-curl
$ curl https://api.shore.com/api/merchant/appointments/:id/reject \
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

## PUT /api/merchant/appointments/:id

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

Update an appointment with the given parameters.

### Request URL
`https://api.shore.com/api/merchant/appointments/:id`

### Request parameters


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