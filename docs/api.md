---
layout: docs
title: Using the API
---

This guide will take you through getting data from the API, authentication with the API and creating new data.

## Overview

Most applications will use an existing wrapper library in the language of your choice. However it's important to be familiar with the underlying API HTTP methods first.

Here we'll be using [cURL](http://curl.haxx.se/) to interact with the API directly.

### Entry point

Let's test everything is working correctly first. Open your command prompt and enter the following (without the `$`).

    $ curl https://za-peoples-assembly.popit.mysociety.org/api/v0.1/

    {
      "meta": {
        "image_proxy_url": "https://za-peoples-assembly.popit.mysociety.org/image-proxy/",
        "memberships_api_url": "https://za-peoples-assembly.popit.mysociety.org/api/v0.1/memberships",
        "organizations_api_url": "https://za-peoples-assembly.popit.mysociety.org/api/v0.1/organizations",
        "persons_api_url": "https://za-peoples-assembly.popit.mysociety.org/api/v0.1/persons"
      },
      "note": "This is the API entry point - use a '*_api_url' link in 'meta' to search a collection."
    }

This is the API entry point, it shows you the various resource types that you can access via the API.

### Retrieve a person's record

Next we're going to retrieve some existing data from the API.

    $ curl https://za-peoples-assembly.popit.mysociety.org/api/v0.1/persons/org.mysociety.za/person/1

    {
      "result": {
        "html_url": "https://za-peoples-assembly.popit.mysociety.org/persons/org.mysociety.za/person/1",
        "url": "https://za-peoples-assembly.popit.mysociety.org/api/v0.1/persons/org.mysociety.za/person/1",
        "other_names": [],
        "identifiers": [
          {
            "identifier": "875",
            "scheme": "za.gov.parliament/person"
          },
          {
            "identifier": "5453",
            "scheme": "myreps_person_id"
          },
          {
            "identifier": "8222",
            "scheme": "myreps_id"
          }
        ],
        "contact_details": [
          {
            "value": "sswart@parliament.gov.za",
            "type": "email"
          }
        ],
        "links": [],
        "id": "org.mysociety.za/person/1",
        "family_name": "Swart",
        "given_names": "Steven Nicholas",
        "image": "http://www.parliament.gov.za/content/SWART%20STEVEN%20NICHOLAS.jpg",
        "slug": "steven-nicholas-swart",
        "honorific_prefix": "Mr",
        "name": "Steven Nicholas Swart",
        ...
    }

This is a person record. The fields definitions come from the [Popolo project's Person spec](http://popoloproject.com/specs/person.html).

## Authentication

Unauthenticated users can read resources from the API, but to create new resources you'll need to have a popit login with an API key and editor access to the instance.

Once you have an account select the 'Get API key' option from the user menu ( click on your user name in the top right ). Click the 'Request API key' button on the page and note down the key and head back here.

You can copy your api-base-url from the homepage of the instance.

OK! We're going to create a new [Organization](http://popoloproject.com/specs/organization.html) record. When you specify your email address to cURL with the `--user` option it will prompt you for your password.

In this example our API key is **2a0abbefe6aefb67ba5302a12570c2af2a7f4433** and our instance is called **walkthrough**.

Your API key needs to be given in the `Apikey` header.

    $ curl --header 'Apikey: 2a0abbefe6aefb67ba5302a12570c2af2a7f4433'  --header 'Content-Type: application/json' --data '{"name": "Widgets Inc"}' https://walkthrough.popit.mysociety.org/api/v0.1/organizations

    {
      "result": {
        "html_url": "https://walkthrough.popit.mysociety.org/organizations/5373a60c657d191a1c3ff44e",
        "url": "https://walkthrough.popit.mysociety.org/api/v0.1/organizations/5373a60c657d191a1c3ff44e",
        "id": "5373a60c657d191a1c3ff44e",
        "name": "Widgets Inc",
        "posts": [],
        "memberships": [],
        "links": [],
        "contact_details": [],
        "identifiers": [],
        "other_names": []
      }
    }

Alternatively you can add an `apikey` property to the JSON body and provide your API key there.

    $ curl --header 'Content-Type: application/json' --data '{"apikey": "2a0abbefe6aefb67ba5302a12570c2af2a7f4433", "name": "Widgets Inc"}' https://walkthrough.popit.mysociety.org/api/v0.1/organizations

Congratulations! You've now created a new Organization record using the API. You can access this record at the `url` property of the record.

## Further reading

This guide has only skimmed the surface of the API, for more details check out the [API reference]({{ site.baseurl }}/docs/api/reference/).
