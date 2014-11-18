---
layout: docs
title: Merging records
description: How to merge people using the PopIt API
---

**Note**: Currently it's only possible to merge people using the API. If there is enough interest then we may extend this to other record types in the future.

## Merge one person into another

Merges one person records into another person record, updating the **primary** person and removing the **secondary** person.

If the two records have different `name` properties then the **secondary** record's name is added to the **primary** person's `other_names` array. If the two records have other non-array fields which are different then an error will be returned explaining the problem. All array fields will be merged together after removing duplicates.

    POST /api/v0.1/persons/:primary_id/merge/:secondary_id

### Example

The example responses below use this **primary** person record:

{% highlight json %}
{
  "id": "david-cameron",
  "name": "David Cameron",
  "links": [
    {
      "url": "http://en.wikipedia.org/wiki/David_Cameron",
      "note": "Wikipedia page"
    }
  ],
  "contact_details": [
    {
      "label": "Twitter Account",
      "type": "twitter",
      "value": "david_cameron"
    }
  ]
}

{% endhighlight %}

and this **secondary** person record:

{% highlight json %}
{
  "id": "david-cameron-2",
  "name": "David Cameron",
  "links": [
    {
      "url": "https://www.conservatives.com/OurTeam/David_Cameron.aspx",
      "note": "Conservative Party page"
    }
  ],
  "contact_details": [
    {
      "label": "Facebook Account",
      "type": "facebook",
      "value": "DavidCameronOfficial"
    }
  ]
}
{% endhighlight %}

#### Success response

    POST /api/v0.1/persons/david-cameron/merge/david-cameron-2

{% highlight json %}
{
  "result": {
    "id": "david-cameron",
    "name": "David Cameron",
    "links": [
      {
        "url": "http://en.wikipedia.org/wiki/David_Cameron",
        "note": "Wikipedia page"
      },
      {
        "url": "https://www.conservatives.com/OurTeam/David_Cameron.aspx",
        "note": "Conservative Party page"
      }
    ],
    "contact_details": [
      {
        "label": "Twitter Account",
        "type": "twitter",
        "value": "david_cameron"
      },
      {
        "label": "Facebook Account",
        "type": "facebook",
        "value": "DavidCameronOfficial"
      }
    ]
  }
}
{% endhighlight %}

**Note**: Some fields have been omitted for brevity.

#### Error response

If the documents have non-array properties that differ, for instance if `david-cameron` had `{"gender": "male"}` and `david-cameron-2` had `{"gender": "m"}` then you would get an error like the following. Note that this doesn't apply to array properties, which will be merged together.

    POST /api/v0.1/persons/david-cameron/merge/david-cameron-2

{% highlight json %}
{
  "errors": [
    "Unresolvable merge conflict",
    "Please resolve conflict with gender: male doesn't match m"
  ]
}
{% endhighlight %}
