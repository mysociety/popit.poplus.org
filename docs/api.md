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

    $ curl http://za-peoples-assembly.popit.mysociety.org/api/v0.1/

    {
      "meta": {
        "image_proxy_url": "http://za-peoples-assembly.popit.mysociety.org/image-proxy/",
        "memberships_api_url": "http://za-peoples-assembly.popit.mysociety.org/api/v0.1/memberships",
        "organizations_api_url": "http://za-peoples-assembly.popit.mysociety.org/api/v0.1/organizations",
        "persons_api_url": "http://za-peoples-assembly.popit.mysociety.org/api/v0.1/persons"
      },
      "note": "This is the API entry point - use a '*_api_url' link in 'meta' to search a collection."
    }

This is the API entry point, it shows you the various resource types that you can access via the API.

### Retrieve a person's record

Next we're going to retrieve some existing data from the API.

    $ curl http://za-peoples-assembly.popit.mysociety.org/api/v0.1/persons/org.mysociety.za/person/1

    {
      "result": {
        "html_url": "http://za-peoples-assembly.popit.mysociety.org/persons/org.mysociety.za/person/1",
        "url": "http://za-peoples-assembly.popit.mysociety.org/api/v0.1/persons/org.mysociety.za/person/1",
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

Unauthenticated users can read resources from the API, but to create new resources you'll need to have your own instance and know your username and password.

Before going through this section go and [create a PopIt instance]({{ site.baseurl }}/docs/getting-started/#create-an-instance), then make a note of your email, password and instance API url and head back here.

You can copy your api-base-url from the homepage of your instance.

OK! We're going to create a new [Organization](http://popoloproject.com/specs/organization.html) record. When you specify your email address to cURL with the `--user` option it will prompt you for your password.

In this example our account email address is **bob@example.com** and our instance is called **walkthrough**.

    $ curl --user bob@example.com --header 'Content-Type: application/json' --data '{"name": "Widgets Inc"}' http://walkthrough.popit.mysociety.org/api/v0.1/organizations

    Enter host password for user 'bob@example.com':

    {
      "result": {
        "html_url": "http://walkthrough.popit.mysociety.org/organizations/5373a60c657d191a1c3ff44e",
        "url": "http://walkthrough.popit.mysociety.org/api/v0.1/organizations/5373a60c657d191a1c3ff44e",
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

Congratulations! You've now created a new Organization record using the API. You can access this record at the `url` property of the record.

## Further reading

This guide has only skimmed the surface of the API, for more details check out the [API reference]({{ site.baseurl }}/docs/api/reference/).
