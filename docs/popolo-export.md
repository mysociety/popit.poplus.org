---
layout: docs
title: Popolo export
---

### Export all data

To export all data from your instance, just visit `/api/v0.1/export.json`. This will give you a copy of the data in your instance in [Popolo][] format. See the [JSON Format](#json-format) section for more details on how this file is formatted. If your data is in multiple languages then this will only export the default language. To export other languages see the [next section](#export-data-in-a-particular-language).

    GET /api/v0.1/export.json

If you'd like this data in compressed format then simply add a `.gz` extension.

    GET /api/v0.1/export.json.gz

### Export data in a particular language

If you've specified an alternative language for your instance in the settings then you can separately download each language.

    GET /api/v0.1/export-:language.json

As above, if you'd like a compressed version of this data, just add a `.gz` extension.

    GET /api/v0.1/export-:language.json.gz

For example if you've set za-Hant as an alternative language, you could use this url to download just the za-Hant version of your data.

    GET /api/v0.1/export-za-Hant.json

## JSON format

When exporting [Popolo][] JSON the file that is output follows a certain format. The JSON is formatted as an object where each key is a valid collection name, and the value for each collection is an array of [Popolo][]-compliant objects.

### Valid collections

- `organizations`
- `posts`
- `persons`
- `memberships`

### Example

This is an example of the [Popolo][] JSON that you might get from an export.

{% highlight json %}
{
  "organizations": [
    {
      "id": "acme-corp",
      "name": "ACME Corporation"
    }
  ],
  "persons": [
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

[popolo]: http://popoloproject.com/
