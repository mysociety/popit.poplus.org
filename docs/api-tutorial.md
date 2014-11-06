---
layout: docs
title: API tutorial
---

This tutorial aims to show you the basics of using the API. For simplicity we're going to use the command line tool `curl` although for use as part of a larger project you'll probably use either the appropriate PopIt client library or an HTTP client.

We're going to assume that you've got an account and an API key, as described in the Authentication section of [Using the API ](/docs/api/#authentication).

### Getting the API URL ###

Firstly we need the URL for the API for your instance. If you go to the front page of your instance you should see a textbox next to the description:

<img src="{{ site.baseurl }}/assets/img/walkthrough-api-url-location.png" />

The URL in this textbox is the base URL for your instance's API.

For these examples we're going to assume our instance is called walkthrough and its API is accessed at `https://walkthrough.popit.mysociety.org/api/v0.1`.

We can check this works by calling it:

{% highlight bash %}

$ curl https://walkthrough.popit.mysociety.org/api/v0.1/

{
  "note": "This is the API entry point - use a '*_api_url' link in 'meta' to search a collection.",
  "meta": {
    "persons_api_url": "https://walkthrough.popit.mysociety.org/api/v0.1/persons",
    "organizations_api_url": "https://walkthrough.popit.mysociety.org/api/v0.1/organizations",
    "memberships_api_url": "https://walkthrough.popit.mysociety.org/api/v0.1/memberships",
    "posts_api_url": "https://walkthrough.popit.mysociety.org/api/v0.1/posts",
    "image_proxy_url": "https://walkthrough.popit.mysociety.org/image-proxy/"
  }

{% endhighlight %}

As you can see it returns a list of the API URLs used to access the various different objects that PopIt uses. For more details about what each of these are see the guide to [using the Popolo data model](/docs/using-the-popolo-data-model/).

### Getting data ###

Getting data out of PopIt using the API is simple. To get a list of all the items of a certain type you just call the relevant API end point we saw in the call above. So, to get details of all the people stored in our instance we call `https://walkthrough.popit.mysociety.org/api/v0.1/persons`:

{% highlight bash %}

$ curl https://walkthrough.popit.mysociety.org/api/v0.1/persons/

{
  "total": 0,
  "page": 1,
  "per_page": 30,
  "has_more": false,
  "result": []
}

{% endhighlight %}

Only we don't actually have any people stored there at the moment. If we look at the results you can see the following fields:

 * `total` - This is the total number of results returned, 0 in this case.
 * `page` - This is the page number of results we are currently on.
 * `per_page` - How many results are on each page
 * `has_more` - If there are any more pages
 * `result` - The actual results. As there are none this is an empty array

The `page`, `per_page` and `has_more` fields are there because results from the API can be split over multiple pages.

### Adding a Person ###

As PopIt is for storing information about people we should probably add one to our instance. To do this we need to make a POST request to the person end point with the JSON representation of a person.

{% highlight bash %}

$ curl --header 'Apikey: 2a0abbefe6aefb67ba5302a12570c2af2a7f4433' \
       --header 'Accept: application/json' \
       --header 'Content-Type: application/json' \
       --data '{"name": "Joe Bloggs"}' \
       https://walkthrough.popit.mysociety.org/api/v0.1/persons

    {
      "result": {
        "html_url": "https://walkthrough.popit.mysociety.org/persons/5373a60c657d191a1c3ff44e",
        "url": "https://walkthrough.popit.mysociety.org/api/v0.1/persons/5373a60c657d191a1c3ff44e",
        "id": "5373a60c657d191a1c3ff44e",
        "name": "Joe Bloggs",
        "memberships": [],
        "links": [],
        "contact_details": [],
        "identifiers": [],
        "other_names": []
      }
    }

{% endhighlight %}

There's a few things going on here so we'll walk through the options we passed to `curl` so you can see what they're all for:

 * `--header 'Apikey: 2a0abbefe6aefb67ba5302a12570c2af2a7f4433'`

 This is our apikey. You need this so that PopIt knows who you are and that you have permission to add data to your instance.

 * `--header 'Accept: application/json'`

 This tells PopIt that we'd like information back formatted as a JSON object

 * `--header 'Content-Type: application/json'`

 This tells PopIt that we're sending it data in the JSON format so it knows how to process it.

 * `--data '{"name": "Joe Bloggs"}'`

 This is the data we're uploading. This is the minimum information you need to add a person in PopIt

 * `https://walkthrough.popit.mysociety.org/api/v0.1/persons`

 And finally the API URL for persons.

In the information returned by the API you'll notice that it's provided a URL for both the web page of your popit instance for the person - the `html_url` field - and also the API URL for the person - the `url` field. We'll use the second one later for updating the details.

You can also see that PopIt generates an ID for the new person. If you want to use your own IDs then you can supply them and PopIt will use those instead:

{% highlight bash %}

$ curl --header 'Apikey: 2a0abbefe6aefb67ba5302a12570c2af2a7f4433' \
       --header 'Accept: application/json' \
       --header 'Content-Type: application/json' \
       --data '{"id": "joe-bloggs", "name": "Joe Bloggs"}' \
       https://walkthrough.popit.mysociety.org/api/v0.1/persons

    {
      "result": {
        "html_url": "https://walkthrough.popit.mysociety.org/persons/joe-bloggs",
        "url": "https://walkthrough.popit.mysociety.org/api/v0.1/persons/joe-bloggs",
        "id": "joe-bloggs",
        "name": "Joe Bloggs",
        "memberships": [],
        "links": [],
        "contact_details": [],
        "identifiers": [],
        "other_names": []
      }
    }

{% endhighlight %}

Note that the `--data` line is now

       --data '{"id": "joe-bloggs", "name": "Joe Bloggs"}'

and that the `id` field and the two URLs are different. If we'd tried to use an existing ID PopIt would have returned an error.

{% highlight bash %}
  $ curl --header 'Apikey: 2a0abbefe6aefb67ba5302a12570c2af2a7f4433' \
         --header 'Accept: application/json' \
         --header 'Content-Type: application/json' \
         --data '{"id": "joe-bloggs", "name": "Joe Bloggs"}' \
         https://walkthrough.popit.mysociety.org/api/v0.1/persons

  {
    "error":{
      "message":"insertDocument :: caused by :: 11000 E11000 duplicate key error index: popitdev_walkthrough.persons.$_id_  dup key: { : \"joe-bloggs\" }",
      "stack":"MongoError: Omitted for space",
      "name":"MongoError",
      "connectionId":229,
      "err":"insertDocument :: caused by :: 11000 E11000 duplicate key error index: popitdev_walkthrough.persons.$_id_  dup key: { : \"joe-bloggs\" }",
      "code":11000,
      "n":0,
      "ok":1
    }
  }

{% endhighlight %}

( The error message above has been slightly edited to make it easier to read )

Of course, a list of people on their own is only so useful, we need to add organizations and link those with people.

### Adding an Organization ###

This is essentially the same as adding a person, only with a different URL for the end point.

{% highlight bash %}

$ curl --header 'Apikey: 2a0abbefe6aefb67ba5302a12570c2af2a7f4433' \
       --header 'Accept: application/json' \
       --header 'Content-Type: application/json' \
       --data '{ "id": "walkthrough-parliament", "name": "Walkthrough Parliament"}' \
       https://walkthrough.popit.mysociety.org/api/v0.1/organizations

    {
      "result": {
        "html_url": "https://walkthrough.popit.mysociety.org/organizations/walkthrough-parliament",
        "url": "https://walkthrough.popit.mysociety.org/api/v0.1/organizations/walkthrough-parliament",
        "id": "walkthrough-parliament",
        "name": "Walkthrough Parliament",
        "posts": [],
        "memberships": [],
        "links": [],
        "contact_details": [],
        "identifiers": [],
        "other_names": []
      }
    }

{% endhighlight %}

### Adding a membership ###

Finally, we want to link the people to the organizations.

{% highlight bash %}

$ curl --header 'Apikey: 2a0abbefe6aefb67ba5302a12570c2af2a7f4433' \
       --header 'Accept: application/json' \
       --header 'Content-Type: application/json' \
       --data '{ "organization_id": "walkthrough-parliament", "person_id": "joe-bloggs"}' \
       https://walkthrough.popit.mysociety.org/api/v0.1/memberships

    {
      "result": {
        "id": "545b8e21530d390308dd298c",
        "organization_id": "walkthrough-parliament",
        "person_id": "joe-bloggs",
        "links": [],
        "contact_details": [],
        "url": "https://walkthrough.popit.mysociety.org/api/v0.1/memberships/545b8e21530d390308dd298c",
        "html_url": "https://walkthrough.popit.mysociety.org/memberships/545b8e21530d390308dd298c"
      }
    }

{% endhighlight %}

So now if we request details of the person we added we can see the membership we added too:

{% highlight json %}
  {
    "result": {
      "id": "joe-bloggs",
      "name": "Joe Bloggs",
      "memberships": [
        {
          "id": "545b8e21530d390308dd298c",
          "organization_id": "walkthrough-parliament",
          "person_id": "joe-bloggs",
          "links": [],
          "contact_details": [],
          "url": "https://walkthrough.popit.mysociety.org/api/v0.1/memberships/545b8e21530d390308dd298c",
          "html_url": "https://walkthrough.popit.mysociety.org/memberships/545b8e21530d390308dd298c"
        }
      ],
      "links": [],
      "contact_details": [],
      "identifiers": [],
      "other_names": [],
      "url": "https://walkthrough.popit.mysociety.org/api/v0.1/persons/joe-bloggs",
      "html_url": "https://walkthrough.popit.mysociety.org/persons/joe-bloggs"
    }
  }
{% endhighlight %}

### Updating a Person ###

Now that we've stored details about someone in PopIt we're probably going to want to update them at some point. To do this we make a PUT request to the URL of the person we're updating with the details we want to change or add:

{% highlight bash %}
  curl --request PUT \
      --header "Accept: application/json" \
      --header "Content-Type: application/json" \
      --header "apikey: 2a0abbefe6aefb67ba5302a12570c2af2a7f4433" \
      --data '{ "birth_date": "1970-01-01", "name": "Joe Bloggs" }' \
      https://walkthrough.popit.mysociety.org/api/v0.1/persons/joe-bloggs

  {
    "result": {
      "id": "joe-bloggs",
      "birth_date": "1970-01-01",
      "name": "Joe Bloggs",
      "memberships": [],
      "links": [],
      "contact_details": [],
      "identifiers": [],
      "other_names": [],
      "url": "https://walkthrough.popit.mysociety.org/api/v0.1/persons/joe-bloggs",
      "html_url": "https://walkthrough.popit.mysociety.org/persons/joe-bloggs"
    }
  }
{% endhighlight %}

Note that we have to include the name in each request, but we only need to include the information we want to change. If you don't include a field in the update then it will remain unchanged:

{% highlight bash %}
  curl --request PUT \
      --header "Accept: application/json" \
      --header "Content-Type: application/json" \
      --header "apikey: 2a0abbefe6aefb67ba5302a12570c2af2a7f4433" \
      --data '{ "summary": "An example person for the tutorial", "name": "Joe Bloggs" }' \
      https://walkthrough.popit.mysociety.org/api/v0.1/persons/joe-bloggs

  {
    "result": {
      "id": "joe-bloggs",
      "birth_date": "1970-01-01",
      "summary": "An example person for the tutorial",
      "name": "Joe Bloggs",
      "memberships": [],
      "links": [],
      "contact_details": [],
      "identifiers": [],
      "other_names": [],
      "url": "https://walkthrough.popit.mysociety.org/api/v0.1/persons/joe-bloggs",
      "html_url": "https://walkthrough.popit.mysociety.org/persons/joe-bloggs"
    }
  }
{% endhighlight %}

### Further reading and help ###

That's just a quick guide to the basics of using the PopIt API. There's more details on the available methods in the [API reference](/docs/api/reference/) and as always you can ask questions about PopIt on the [Poplus mailing list](https://groups.google.com/forum/#!forum/poplus).
