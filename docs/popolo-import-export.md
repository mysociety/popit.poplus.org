---
layout: docs
title: Popolo import and export
---

- This will be replaced by a Table of contents
{:toc}

## JSON format

When exporting or import Popolo JSON the file that you produce will need to follow a certain format. The JSON must be formatted as an object where each key is a valid collection name, and the value for each collection is an array of Popolo-compliant objects.

You can optionally provide an `id` property on each object in the collection array, if you don't provide one then PopIt will generate one for you.

### Valid collections

- `organizations`
- `posts`
- `people` (or `persons`)
- `memberships`

### Example

This is an example of a valid Popolo import JSON object.

{% highlight json %}
{
  "organizations": [
    {
      "id": "acme-corp",
      "name": "ACME Corporation"
    }
  ],
  "people": [
    {
      "id": "john-q-public",
      "name": "Mr. John Q. Public, Esq.",
      "email": "jqpublic@xyz.example.com",
      "contact_details": [
        {
          "type": "cell",
          "label": "Mobile number",
          "value": "+1-555-555-0100",
          "note": "Free evenings and weekends"
        }
      ]
    }
  ]
}
{% endhighlight %}

## Export

TODO How to export data from PopIt

## Import

TODO How to import data into PopIt
