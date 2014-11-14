---
layout: docs
title: API Reference
description: The PopIt API allows you to easily access all the data stored in PopIt from your code or other websites.
---

## Introduction
{:.no_toc}

The PopIt API offers a JSON interface for accessing and manipulating People, Organizations, Memberships and Posts that are stored in an instance. Every PopIt instance has an API that makes almost all the data stored in it accessible programatically. You can access the API by visiting `/api/v0.1` in your instance, e.g. [https://za-peoples-assembly.popit.mysociety.org/api/v0.1/](https://za-peoples-assembly.popit.mysociety.org/api/v0.1/).

### Contents
{:.no_toc}

* This will be replaced with the ToC, excluding the "Introduction" and "Contents" headers above
{:toc}

## Versioning

There is a version string in the API url, currently `v0.1`. This will be updated if the API undergoes significant changes that breaks backwards compatibility.

## Data schema

All textual entries stored in PopIt follow the [Popolo](http://popoloproject.com/) schema. You can also add additional fields on top of this using the admin interface or the API.

## API collections

The API allows you to query four types of collection, **persons**, **organizations**, **memberships** and **posts**. Anywhere in the documentation you see `:collection` you can replace it with one of those four types.

- https://za-peoples-assembly.popit.mysociety.org/api/v0.1/persons
- https://za-peoples-assembly.popit.mysociety.org/api/v0.1/organizations
- https://za-peoples-assembly.popit.mysociety.org/api/v0.1/memberships
- https://za-peoples-assembly.popit.mysociety.org/api/v0.1/posts

**Note**: the South Africa People's Assembly instance doesn't currently use the **posts** collection.

## Responses

JSON objects are always returned in the response. These will have the form:

### For a collection

{% highlight json %}
{
  "result": [
    // list of {} results here
  ]
}
{% endhighlight %}

### For a single document

{% highlight json %}
{
  "result": {
    // The data stored for the person
  }
}
{% endhighlight %}

### Errors

If something goes wrong there will be an `errors` array that will explain the error. Often an HTTP error code will also be used (eg **404** if a result cannot be found).

## Record ids

You can provide an `id` field for a record when creating it. If the dataset you're adding to PopIt already has a natural primary key then it makes sense to use that as the `id` attribute. If you don't have a primary key that you want to use then simply omit the `id` property when creating the record and one will be generated for you and returned in the response.

## Pagination

Result listings default to 30 items per page.  When results are paginated there will be extra metadata properties exposed on the body of the response:

- **page** - Integer representing current page number, defaulting to 1. Pass a `?page` parameter to access further pages (maximum page number is 1000)
- **per_page** - Integer representing number of items to return per page of results. Pass `?per_page` parameter to change this (maximum items per page is 100).
- **total** - Integer representing the total number of results in this listing.
- **has_more** - A boolean indicating whether there is another page of results after this one.
- **prev_url** - The url to access the previous page of results. Will not be present on the first page of results.
- **next_url** - The url to access the next page of results. Will not be present on the last page of results.

{% highlight json %}
{
  "total": 90,
  "page": 2,
  "per_page": 30,
  "has_more": true,
  "prev_url": "https://za-peoples-assembly.popit.mysociety.org/api/v0.1/persons?page=1",
  "next_url": "https://za-peoples-assembly.popit.mysociety.org/api/v0.1/persons?page=3",
  "result": [...]
}
{% endhighlight %}


## Storing content in multiple languages

**Note**: If you don't want to store content in multiple languages then you can safely ignore this section.

Any value that can be a string can instead be an language object where each key is a valid language code (see **Language keys** section below for details). For example if you wanted to store a person's name in English and Russian, you would use the following JSON:

{% highlight json %}
{
  "name": {
    "en": "John Smith",
    "ru": "Джон Смит"
  }
}
{% endhighlight %}

When retrieving records the `Accept-Language` header is checked to see which language the user wants the record in. For example if it's set to `Accept-Language: ru` then the response will contain the record translated to Russian:

{% highlight json %}
{
  "name": "Джон Смит"
}
{% endhighlight %}

If there is no match for the requested language, or if the `Accept-Language` header is absent then it will fall back to the default language for the instance, which is currently English (en) but will be configurable in the future.

### Language keys

An object is determined to be a translation object if all of the keys are valid language codes. This is checked against IANA's [official repository](http://www.iana.org/assignments/language-subtag-registry/language-subtag-registry) of language tags. If one of the keys of the object is not a valid language code then it won't be detected as a translation. If your translations aren't being detected them check your language codes against the repository above.

### Searching

When indexing multiple languages each translation is stored in a separate string key. So for the example above the English and Russian translations would be stored in `name_en` and `name_ru` respectively. So to search for a name in Russian you can use a query like `name_ru:Джон`. The `name` key will still exist, but will only contain the default language.

## Embedding memberships

When retrieving a person, organization or post from the API you can include the record's memberships by using the `?embed` parameter.

### Skip embed

By default an objects memberships are included in API responses, you can suppress this by setting the embed parameter to a blank string:

    https://za-peoples-assembly.popit.mysociety.org/api/v0.1/persons/org.mysociety.za/person/1?embed=

### Embed related people/organizations

If you want to include all the related organizations for memberships then you can set the `?embed=membership.organization`. Similarly if you want to include the person in each membership then you can set `?embed=membership.person`.

    https://za-peoples-assembly.popit.mysociety.org/api/v0.1/persons/org.mysociety.za/person/1?embed=membership.organization

### Multi-level embeds

Embedding can be nested up to three levels deep, so you can embed memberships with the related people and their memberships with the related organizations and their memberships with their related people. Note though that queries like this can be very slow, especially on collection endpoints.

    https://za-peoples-assembly.popit.mysociety.org/api/v0.1/persons/org.mysociety.za/person/1?embed=membership.person.membership.organization.membership.person

This works on collections of records, individual records and search results.

## Examples

To view a list of all the people (**persons**) in the database:

https://za-peoples-assembly.popit.mysociety.org/api/v0.1/persons

{% highlight json %}
{
  "result": [
    {
      "html_url": "https://za-peoples-assembly.popit.mysociety.org/persons/steven-nicholas-swart",
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
      "memberships": [
        {
          "url": "https://za-peoples-assembly.popit.mysociety.org/api/v0.1/memberships/org.mysociety.za/membership/1",
          "contact_details": [],
          "links": [],
          "person_id": "org.mysociety.za/person/1",
          "organization_id": "org.mysociety.za/party/acdp",
          "id": "org.mysociety.za/membership/1"
        },
        {
          "url": "https://za-peoples-assembly.popit.mysociety.org/api/v0.1/memberships/org.mysociety.za/membership/1484",
          "contact_details": [],
          "links": [],
          "person_id": "org.mysociety.za/person/1",
          "organization_id": "org.mysociety.za/committee/74",
          "id": "org.mysociety.za/membership/1484"
        },
        {
          "url": "https://za-peoples-assembly.popit.mysociety.org/api/v0.1/memberships/org.mysociety.za/membership/1485",
          "contact_details": [],
          "links": [],
          "person_id": "org.mysociety.za/person/1",
          "organization_id": "org.mysociety.za/committee/18",
          "id": "org.mysociety.za/membership/1485"
        },
        {
          "url": "https://za-peoples-assembly.popit.mysociety.org/api/v0.1/memberships/org.mysociety.za/membership/1620",
          "contact_details": [],
          "links": [],
          "role": "Alternate Member",
          "person_id": "org.mysociety.za/person/1",
          "organization_id": "org.mysociety.za/committee_pmg/8",
          "id": "org.mysociety.za/membership/1620"
        },
        {
          "url": "https://za-peoples-assembly.popit.mysociety.org/api/v0.1/memberships/org.mysociety.za/membership/1727",
          "contact_details": [],
          "links": [],
          "role": "Alternate Member",
          "person_id": "org.mysociety.za/person/1",
          "organization_id": "org.mysociety.za/committee_pmg/16",
          "id": "org.mysociety.za/membership/1727"
        },
        {
          "url": "https://za-peoples-assembly.popit.mysociety.org/api/v0.1/memberships/org.mysociety.za/membership/966",
          "contact_details": [],
          "id": "org.mysociety.za/membership/966",
          "area": {
            "id": "org.mysociety.za/mapit/code/p/WC",
            "name": "Western Cape"
          },
          "label": "Member for Western Cape",
          "organization_id": "org.mysociety.za/house/national-assembly",
          "role": "Member",
          "start_date": "2009-05-06",
          "person_id": "org.mysociety.za/person/1",
          "links": []
        },
        {
          "url": "https://za-peoples-assembly.popit.mysociety.org/api/v0.1/memberships/org.mysociety.za/membership/1644",
          "contact_details": [],
          "links": [],
          "role": "Alternate Member",
          "person_id": "org.mysociety.za/person/1",
          "organization_id": "org.mysociety.za/committee_pmg/10",
          "id": "org.mysociety.za/membership/1644"
        },
        {
          "url": "https://za-peoples-assembly.popit.mysociety.org/api/v0.1/memberships/org.mysociety.za/membership/2222",
          "contact_details": [],
          "links": [],
          "role": "Alternate Member",
          "person_id": "org.mysociety.za/person/1",
          "organization_id": "org.mysociety.za/committee_pmg/53",
          "id": "org.mysociety.za/membership/2222"
        }
      ]
    },
    // More person objects...
  ]
}
{% endhighlight %}

Or view the record for an individual person in the database:

https://za-peoples-assembly.popit.mysociety.org/api/v0.1/persons/org.mysociety.za/person/104

{% highlight json %}
{
  "result": {
    "html_url": "https://za-peoples-assembly.popit.mysociety.org/persons/max-vuyisile-sisulu",
    "url": "https://za-peoples-assembly.popit.mysociety.org/api/v0.1/persons/org.mysociety.za/person/104",
    "other_names": [],
    "identifiers": [
      {
        "identifier": "629",
        "scheme": "za.gov.parliament/person"
      },
      {
        "identifier": "5428",
        "scheme": "myreps_person_id"
      },
      {
        "identifier": "8172",
        "scheme": "myreps_id"
      }
    ],
    "contact_details": [
      {
        "value": "speaker@parliament.gov.za",
        "type": "email"
      },
      {
        "value": "(021) 461 9462",
        "type": "fax",
        "note": "Session Fax Number"
      },
      {
        "value": "(021) 403 2595/3812",
        "type": "voice",
        "note": "Session Phone Number"
      }
    ],
    "links": [],
    "id": "org.mysociety.za/person/104",
    "family_name": "Sisulu",
    "given_names": "Max Vuyisile",
    "image": "http://www.parliament.gov.za/content/SISULU%20MAX%20VUYISILE.jpg",
    "slug": "max-vuyisile-sisulu",
    "honorific_prefix": "Mr",
    "name": "Max Vuyisile Sisulu",
    "memberships": [
      {
        "url": "https://za-peoples-assembly.popit.mysociety.org/api/v0.1/memberships/org.mysociety.za/membership/1005",
        "contact_details": [],
        "links": [],
        "role": "Speaker",
        "person_id": "org.mysociety.za/person/104",
        "organization_id": "org.mysociety.za/house/national-assembly",
        "id": "org.mysociety.za/membership/1005"
      },
      {
        "url": "https://za-peoples-assembly.popit.mysociety.org/api/v0.1/memberships/org.mysociety.za/membership/104",
        "contact_details": [],
        "links": [],
        "person_id": "org.mysociety.za/person/104",
        "organization_id": "org.mysociety.za/party/anc",
        "id": "org.mysociety.za/membership/104"
      },
      {
        "url": "https://za-peoples-assembly.popit.mysociety.org/api/v0.1/memberships/org.mysociety.za/membership/743",
        "id": "org.mysociety.za/membership/743",
        "organization_id": "org.mysociety.za/house/national-assembly",
        "role": "Member",
        "person_id": "org.mysociety.za/person/104",
        "label": "Member",
        "start_date": "2009-05-06",
        "links": [],
        "contact_details": []
      }
    ]
  }
}
{% endhighlight %}

You can expand the contents of the memberships and inline persons or organizations using the embed query parameter.

https://za-peoples-assembly.popit.mysociety.org/api/v0.1/persons/org.mysociety.za/person/104?embed=membership.organization

{% highlight json %}
{
  "result": {
    "html_url": "https://za-peoples-assembly.popit.mysociety.org/persons/max-vuyisile-sisulu",
    "url": "https://za-peoples-assembly.popit.mysociety.org/api/v0.1/persons/org.mysociety.za/person/104",
    "other_names": [],
    "identifiers": [
      {
        "identifier": "629",
        "scheme": "za.gov.parliament/person"
      },
      {
        "identifier": "5428",
        "scheme": "myreps_person_id"
      },
      {
        "identifier": "8172",
        "scheme": "myreps_id"
      }
    ],
    "contact_details": [
      {
        "value": "speaker@parliament.gov.za",
        "type": "email"
      },
      {
        "value": "(021) 461 9462",
        "type": "fax",
        "note": "Session Fax Number"
      },
      {
        "value": "(021) 403 2595/3812",
        "type": "voice",
        "note": "Session Phone Number"
      }
    ],
    "links": [],
    "id": "org.mysociety.za/person/104",
    "family_name": "Sisulu",
    "given_names": "Max Vuyisile",
    "image": "http://www.parliament.gov.za/content/SISULU%20MAX%20VUYISILE.jpg",
    "slug": "max-vuyisile-sisulu",
    "honorific_prefix": "Mr",
    "name": "Max Vuyisile Sisulu",
    "memberships": [
      {
        "id": "org.mysociety.za/membership/1005"
        "url": "https://za-peoples-assembly.popit.mysociety.org/api/v0.1/memberships/org.mysociety.za/membership/1005",
        "contact_details": [],
        "links": [],
        "role": "Speaker",
        "person_id": "org.mysociety.za/person/104",
        "organization_id": {
          "id: "org.mysociety.za/house/national-assembly",
          "slug": "national-assembly",
          "classification": "house",
          "name": "National Assembly",
          "posts": [],
          "memberships": [ ] // empty for brevity but this would be populated
          "links": [],
          "contact_details": [],
          "identifiers": [],
          "other_names": [],
          "url": "https://za-peoples-assembly.popit.mysociety.org/api/v0.1/organizations/org.mysociety.za/house/national-assembly",
          "html_url": "https://za-peoples-assembly.popit.mysociety.org/organizations/org.mysociety.za/house/national-assembly"
        },
      },
    ]
  }
}
{% endhighlight %}

Multiple levels of embedding can be specified:

https://za-peoples-assembly.popit.mysociety.org/api/v0.1/persons/org.mysociety.za/person/104?embed=membership.organization.membership.person

Only three levels of embedding are permitted.

If an organization has both person and organization memberships then it's only possible to join one at a time presently.

If a blank embed parameter is used then the results will exclude memberships. This is useful for situations where only
the basic details about a person or organization are required.

#### Viewing API responses in the browser.

The API returns [JSON](http://en.wikipedia.org/wiki/JSON) - a data format that is easily usable in all programming languages. It is possible to view JSON in your browser (and easily follow embedded links) if you have one of the following extensions installed:

  * [JSONView](https://chrome.google.com/webstore/detail/chklaanhfefbnpoihckbnefhakgolnmc) for Chrome
  * [JSONView](https://addons.mozilla.org/en-US/firefox/addon/jsonview/) for FireFox
  * [Not sure it can be done easily](http://stackoverflow.com/questions/2483771) for Internet Explorer :(
  * Try a search for "JSON Viewer <your browser name>" for other browsers.
