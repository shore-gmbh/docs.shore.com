# CalDAV

Shore acts as a CalDAV server and provides a subset of the [CalDAV protocol](
https://tools.ietf.org/html/rfc4791) and thereby also a subset of the
[WebDAV protocol](https://tools.ietf.org/html/rfc4918#section-9.1).
CalDAV conform clients can request the following endpoints. Login to your
[Shore account](https://secure.shore.com/merchant/account) to obtain your
CalDAV calendar urls and for instructions on configuring supported
3rd-party CalDAV clients.

## OPTIONS /caldav/user

> Example Request

```language-curl
curl https://sync.shore.com/caldav/user \
  -X OPTIONS
  -u my@email.com:secret
```

[CalDAV Calendar Access Support]
(https://tools.ietf.org/html/rfc4791#section-5.1) on the user (i.e.
Shore account) level.

## PROPFIND /caldav/user

> Example Request

```language-curl
curl https://sync.shore.com/caldav/user \
  -X PROPFIND
  -u my@email.com:secret
```

[WebDAV PROPFIND Method]
(https://tools.ietf.org/html/rfc4918#section-9.1) on the user (i.e.
Shore account) level.

## OPTIONS /caldav/calendars

> Example Request

```language-curl
curl https://sync.shore.com/caldav/calendars \
  -X OPTIONS
  -u my@email.com:secret
```

[CalDAV Calendar Access Support]
(https://tools.ietf.org/html/rfc4791#section-5.1) on the home set (i.e.
list of all Shore branch calendars) level.

## PROPFIND /caldav/calendars

> Example Request

```language-curl
curl https://sync.shore.com/caldav/calendars \
  -X PROPFIND
  -u my@email.com:secret
```

[WebDAV PROPFIND Method]
(https://tools.ietf.org/html/rfc4918#section-9.1) on the home set (i.e.
list of all Shore branch calendars) level.

## OPTIONS /caldav/calendars/:calendar_id

> Example Request

```language-curl
curl https://sync.shore.com/caldav/calendars/e969915e-f558-4c09-b8dc-04b8099c633f \
  -X OPTIONS
  -u my@email.com:secret
```

[CalDAV Calendar Access Support]
(https://tools.ietf.org/html/rfc4791#section-5.1) on the calendar (i.e.
single Shore branch) level.

## PROPFIND /caldav/calendars/:calendar_id

> Example Request

```language-curl
curl https://sync.shore.com/caldav/calendars/e969915e-f558-4c09-b8dc-04b8099c633f \
  -X PROPFIND
  -u my@email.com:secret
```

[WebDAV PROPFIND Method]
(https://tools.ietf.org/html/rfc4918#section-9.1) on the calendar (i.e.
single Shore branch) level.

## REPORT /caldav/calendars/:calendar_id

> Example Request

```language-curl
curl https://sync.shore.com/caldav/calendars/e969915e-f558-4c09-b8dc-04b8099c633f \
  -X REPORT
  -u my@email.com:secret
```

[CalDAV Calendaring Reports]
(https://tools.ietf.org/html/rfc4918#section-9.1) on the calendar (i.e.
single Shore branch) level. Specifically, the [CALDAV:calendar-query REPORT](
https://tools.ietf.org/html/rfc4791#section-7.8) and the
[CALDAV:calendar-multiget REPORT](
https://tools.ietf.org/html/rfc4791#section-7.9) are supported.

## GET /caldav/calendars/:calendar_id/:event_id

> Example Request

```language-curl
curl https://sync.shore.com/caldav/calendars/e969915e-f558-4c09-b8dc-04b8099c633f/the-event-uid \
  -u my@email.com:secret
```

[Getting](https://tools.ietf.org/html/rfc2616#section-9.3) a single
[calendar event](https://tools.ietf.org/html/rfc5545#section-3.4).

## PUT /caldav/calendars/:calendar_id/:event_id

> Example Request

```language-curl
curl https://sync.shore.com/caldav/calendars/e969915e-f558-4c09-b8dc-04b8099c633f/the-event-uid \
  -X PUT
  -u my@email.com:secret
```

[Creating or updating](https://tools.ietf.org/html/rfc4918#section-9.7.1)
a single [calendar event](https://tools.ietf.org/html/rfc5545#section-3.4).

## DELETE /caldav/calendars/:calendar_id/:event_id

> Example Request

```language-curl
curl https://sync.shore.com/caldav/calendars/e969915e-f558-4c09-b8dc-04b8099c633f/the-event-uid \
  -X DELETE
  -u my@email.com:secret
```

[Deleting](https://tools.ietf.org/html/rfc4918#section-9.6)
a single [calendar event](https://tools.ietf.org/html/rfc5545#section-3.4).