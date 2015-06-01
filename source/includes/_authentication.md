# Authentication

> Example Request

```language-curl
$ curl https://api.shore.com/v1/customers \
  -u Mzs6i13SUB16xfyGFAm4:
```

> curl uses the -u flag to pass basic auth credentials (adding a colon after your API key will prevent it from asking you for a password).

You authenticate to the Shore API by providing one of your API key in the request. 

TODO: You can manage your API keys from your account. You can have multiple API keys active at one time. Your API keys carry many privileges, so be sure to keep them secret!

Authentication to the API occurs via HTTP Basic Auth. Provide your API key as the basic auth username. You do not need to provide a password. All API requests must be made over HTTPS. Calls made over plain HTTP will fail. You must authenticate for all requests.


