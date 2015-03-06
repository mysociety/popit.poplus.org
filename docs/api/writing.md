---
layout: docs
title: Writing to the API
description: 
---

It should be possible to manage all the public data stored in a PopIt instance using the API. This includes adding, updating and deleting records.

For details on editing images see [Images and the API]({{ site.baseurl }}/docs/api/images/).

## Authorization

No authentication is required for read access. For write operations on the API you need to be authenticated. This can  either be done using [Basic Authentication](http://en.wikipedia.org/wiki/Basic_access_authentication) or by cookie auth. Basic is probably best for scripts and programatic access, cookie based for Ajax calls etc.

## Method summary

PopIt closely follows the suggested REST method behaviours, but with a few deviations:

<table>
  <tr>
    <td>&nbsp;</td>
    <th>GET</th>
    <th>PUT</th>
    <th>POST</th>
    <th>DELETE</th>
  </tr>
  <tr>
    <th>Collection endpoint:</th>
    <td>List documents</td>
    <td><i>Not implemented</i></td>
    <td>Create document</td>
    <td>Delete documents</td>
  </tr>
  <tr>
    <th>Document endpoint:</th>
    <td>Retrieve document</td>
    <td>Update document</td>
    <td><i>Not implemented</i></td>
    <td>Delete document</td>
  </tr>
</table>

### Creating a new document in a collection

If you `POST` to a collection you will create a new entry.

This is an example of sending a request using [cURL](http://curl.haxx.se/) (a command line tool). The command is first, the response received from the server is printed afterwards.

{% highlight bash %}

$ curl                                                          \
    --request POST                                              \
    --header "Apikey: 2a0abbefe6aefb67ba5302a12570c2af2a7f4433" \
    --header "Accept: application/json"                         \
    --header "Content-Type: application/json"                   \
    --data '{ "name": "Joe Bloggs" }'                           \
    https://test.popit.mysociety.org/api/v0.1/persons

{
  "result": {
    "id": "50d1f2e1c03858f9f6000006",
    "name": "Joe Bloggs"
  }
}
{% endhighlight %}

Note that the request was authenticated using the email address and password of the user.

The `Content-Type` is set to `application/json`. This allows nested values to be correctly sent. The `Accept` header is also set to JSON, but this is not strictly necessary as PopIt will default to JSON responses (and it is currently the only format supported).

### Updating an existing document

Updating a document is done by sending the whole document in the `PUT` request. Any fields that are omitted in the request will be removed, and replaced with the default.

When you put the order of items in arrays (like images) will be changed as well if needed.

### Deleting a document

Sending a `DELETE` request to a document url will cause that document to be
deleted. An empty 204 response will be returned.

{% highlight bash %}
$ curl                                                          \
    --request DELETE                                            \
    --header 'Apikey: 2a0abbefe6aefb67ba5302a12570c2af2a7f4433' \
    https://test.popit.mysociety.org/api/v0.1/persons/50d1f2e1c03858f9f6000006

{}
{% endhighlight %}

### Not Implemented Methods

`PUT` on collections is not implemented. Implementing this would mean that you could replace an entire collection in one operation. It is not likely that this is something that you'll want to do often, and the penalty for doing it by mistake is high.

A `POST` to a document endpoint means that you should treat the document as a collection and add an entry to it. For PopIt this makes no sense and so it is not implemented.

All the not implemented methods will return the status code `405 Method Not Allowed`.
