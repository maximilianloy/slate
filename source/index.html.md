---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - ruby
  - python
  - javascript
  - java

# toc_footers:
#   - <a href='#'>Sign Up for a Developer Key</a>
#   - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the SnowSafe API!

You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

<aside class="notice">
Find the remaining development files in the Dropbox folder:
</aside>

[Dropbox folder](https://www.dropbox.com/sh/kvjnrham8ttptbp/AABwtrTEU9Goox7zjcX6hOQ3a?dl=0)

# Authentication

> To authorize, use this code:

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here"
  -H "api-key: <your-api-key>"
```

> Make sure to replace `<your-api-key>` with your API key.

SnowSafe uses API keys to allow access to the API. You can register a new SnowSafe API key by [emailing us](mailto:max@snowsafe.at).

SnowSafe expects for the API key to be included in all API requests to the server in a header that looks like the following:

`API-Key: your-api-key`

<aside class="notice">
You must replace <code>your-api-key</code> with your organisations API key.
</aside>

# Bulletins

## Get All Bulletins

```ruby
require 'uri'
require 'net/http'

url = URI("http://admin.snowsafe.at/api/bulletins")

http = Net::HTTP.new(url.host, url.port)

request = Net::HTTP::Get.new(url)
request["accept-encoding"] = 'gzip'
request["api-key"] = '<your-api-key>'

response = http.request(request)
puts response.read_body
```

```python
import requests

url = "http://admin.snowsafe.at/api/bulletins"

payload = ""
headers = {
    'accept-encoding': "gzip",
    'api-key': "<your-api-key>"
    }

response = requests.request("GET", url, data=payload, headers=headers)

print(response.text)
```

```shell
curl --request GET \
  --url http://admin.snowsafe.at/api/bulletins \
  --header 'accept-encoding: gzip' \
  --header 'api-key: <your-api-key>'
```

```javascript
var data = null;

var xhr = new XMLHttpRequest();
xhr.withCredentials = true;

xhr.addEventListener("readystatechange", function () {
  if (this.readyState === this.DONE) {
    console.log(this.responseText);
  }
});

xhr.open("GET", "http://admin.snowsafe.at/api/bulletins");
xhr.setRequestHeader("accept-encoding", "gzip");
xhr.setRequestHeader("api-key", "<your-api-key>");

xhr.send(data);
```

```java
HttpResponse<String> response = Unirest.get("http://admin.snowsafe.at/api/bulletins")
  .header("accept-encoding", "gzip")
  .header("api-key", "<your-api-key>")
  .asString();
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": "AT2",
    "language": "de",
    "author": "wertl",
    "published": "2019-02-14T17:10:00+01:00",
    "validFrom": "2019-02-15T00:00:00+01:00",
    "validTill": "2019-02-15T23:59:00+01:00",
    "regions": {
      "AT2R1": {
        "..."
      }
    }
  },
  {
    "id": "AT5",
    "language": "de",
    "author": "zenke",
    "published": "2019-02-14T17:10:00+01:00",
    "validFrom": "2019-02-15T00:00:00+01:00",
    "validTill": "2019-02-15T23:59:00+01:00",
    "regions": {
      "AT5R1": {
        "..."
      }
    }
  }
]
```

This endpoint retrieves all bulletins available to the API key. The returned bulletins are the ones valid at the time of the request.

### HTTP Request

`GET http://admin.snowsafe.at/api/bulletins`

## Bulletin Format


```json
{
  "id": "AT2",
  "language": "de",
  "author": "wertl",
  "published": "2019-02-14T17:10:00+01:00",
  "validFrom": "2019-02-15T00:00:00+01:00",
  "validTill": "2019-02-15T23:59:00+01:00",
  "regions": {
    "AT2R1": {
      "am": {
        "upperLevel": 2,
        "upperText": "über 2000m",
        "lowerLevel": 1,
        "lowerText": "unter 2000m",
        "problems": [
          {
            "type": "drifting snow",
            "lowerRange": true,
            "lowerBorder": "unter 2600m",
            "expositions": [
              "N",
              "NE",
              "E",
              "SE",
              "S",
              "SW",
              "W",
              "NW"
            ]
          },
          {
            "type": "wet snow",
            "expositions": [
              "E",
              "SE",
              "S",
              "SW",
              "W"
            ]
          }
        ]
      },
      "descriptions": [
        {
          "heading": "Gefahrenbeurteilung",
          "text": "NW: In höheren Lagen werden ..."
        },
        {
          "heading": "Schneedeckenaufbau",
          "text": "In hohen Lagen der ..."
        },
        {
          "heading": "Wetter",
          "text": "Ein Hochdruckgebiet, ..."
        },
        {
          "heading": "Tendenz",
          "text": "Auch am Samstag ist ..."
        }
      ],
      "headline": "Im Tagesverlauf steigt die Lawinengefahr"
    }
  }
}
```

Each bulletin consist of general information which is valid for all of its regions and region specific data which might be different for each region.

### Bulletin Parameters

Parameter | Optional | Description
--------- | -------- | -----------
id | No | Province identifier (ISO 3166 code). See at-v1-snowsafe-config.json for the ID <-> region name mapping.
language | No | ISO language code ("de", "en", "it", ...)
author | Yes | Bulletin author
published | No | Date when the bulletin was published by the warning service
validFrom | No | Date when the bulletin validity starts.
validTill | No | Date when the bulletin validity ends.
regions | No | Map of regions and their specific bulletin
am | No | Data for the first dayhalf (if "pm" exists) or the whole day (if no "pm")
pm | Yes | Data for the second dayhalf
lowerLevel | No | Danger level for the lower mountain half (if "upperLevel" exists) or the whole mountain (if no "upperLevel")
upperLevel | No | Danger level for the upper mountain half
problems | Yes | List of avalanche problems. Limited to two problems (per day half)
type | No | The avalanche problem type. Possible values: "drifting snow", "new snow", "wet snow", "old snow", "gliding snow", "favourable situation"
lowerRange | Yes | true if the lower mountain range is affected.
upperRange | Yes | true if the upper mountain range is affected.
lowerBorder | Yes | Short description for the lowerRange height border.
expositions | Yes | List of affected expositions for the specific problem. Possible values: "N", "NE", "E", "SE", "S", "SW", "W", "NW"
descriptions | No | A list of heading + text segments. Headings are not standardised.
headline | No | A short danger headline which should be printed at the very top.

## Format Examples

### AM/PM and upper/lower Danger Levels

```json
{
  "id": "AT2",
  "language": "de",
  "author": "wertl",
  "published": "2019-02-14T17:10:00+01:00",
  "validFrom": "2019-02-15T00:00:00+01:00",
  "validTill": "2019-02-15T23:59:00+01:00",
  "regions": {
    "AT2R1": {
      "am": {
        "upperLevel": 2,
        "upperText": "über 2000m",
        "lowerLevel": 1,
        "lowerText": "unter 2000m"
      },
      "pm": {
        "upperLevel": 3,
        "upperText": "über 2000m",
        "lowerLevel": 2,
        "lowerText": "unter 2000m"
      },
      "headline": "Im Tagesverlauf steigt die Lawinengefahr",
      "..."
    },
    "..."
  }
}
```

There can be up to 4 different danger levels in a region on a single day:

* Difference between AM and PM day halfs
* Difference between upper and lower mountain halfs

"am" is always set, whereas "pm" is only available when the conditions change in the afternoon.
