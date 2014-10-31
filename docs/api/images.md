---
layout: docs
title: Images and the API
description: 
---

The API can be used to upload image files to be stored on the popit instance or to set remote images for persons and organizations.

### Adding a remote image

This is as simple as including a URL in the image property in a `PUT` request.

{% highlight bash %}

$ curl                                                          \
    --request PUT                                               \
    --header "Apikey: 2a0abbefe6aefb67ba5302a12570c2af2a7f4433" \
    --header "Accept: application/json"                         \
    --header "Content-Type: application/json"                   \
    --data '{ "name": "Joe Bloggs",
              "image": "http://example.org/image.jpg" }'        \
https://test.popit.mysociety.org/api/v0.1/persons/50d1f2e1c03858f9f6000006

{
  "result": {
    "id": "50d1f2e1c03858f9f6000006",
    "name": "Joe Bloggs"
    "image": "http://example.org/image.jpg",
    "images": [
        {
            "url": "http://example.org/image.jpg",
        }
    ]
  }
}

{% endhighlight %}

As you can see this also populates the `images` property which contains extra details about the image.

You can also use `images` property to set multiple images, and add extra properties such as `notes` and `source`. The first image in the array will be used as the value for the `image` property.

{% highlight bash %}

$ curl                                                          \
    --request PUT                                               \
    --header "Apikey: 2a0abbefe6aefb67ba5302a12570c2af2a7f4433" \
    --header "Accept: application/json"                         \
    --header "Content-Type: application/json"                   \
    --data '{ "name": "Joe Bloggs"
              "images": [
                {
                  "url": "http://example.org/image.jpg",
                  "notes": "Official publicity shot",
                  "source": "Example.org PR office"
                }
              ]
            }'                                                  \
https://test.popit.mysociety.org/api/v0.1/persons/50d1f2e1c03858f9f6000006

{
  "result": {
    "id": "50d1f2e1c03858f9f6000006",
    "name": "Joe Bloggs"
    "image": "http://example.org/image.jpg",
    "images": [
        {
            "url": "http://example.org/image.jpg",
            "notes": "official publicity shot",
            "source": "example.org pr office"
        }
    ]
  }
}

{% endhighlight %}

If you are updating an existing image which has an `_id` property you should preseve it.

### Uploading an image file

This is done by a `POST` request to the `/image` path of the document you want to add the image to.

{% highlight bash %}

curl -v                                                             \
    --request POST                                                  \
    --header "apikey: 2a0abbefe6aefb67ba5302a12570c2af2a7f4433"     \
    -F notes='Official Portrait'                                    \
    -F source='PR office'                                           \
    -F "image=@portrait.jpg"                                        \
https://test.popit.mysociety.org/api/v0.1/persons/50d1f2e1c03858f9f6000006/image

{
  "result": {
    "id": "50d1f2e1c03858f9f6000006",
    "name": "Joe Bloggs"
    "image": "https://test.popit.mysociety.org/api/v0.1/persons/images/53b14f5bb4f7834e56083bb7/50d1f2e1c03858f9f6000006",
    "images": [
        {
            "_id": "53b14f5bb4f7834e56083bb7",
            "url": "https://test.popit.mysociety.org/persons/50d1f2e1c03858f9f6000006/image/53b14f5bb4f7834e56083bb7",
            "created": "2014-06-30t11:51:55.251z",
            "notes": "Official Portrait",
            "mime_type": "image/jpeg",
            "source": "PR office"
        }
    ]
  }
}

{% endhighlight %}

As you can see it generates the URL of the image an passes it back to you as both the `image` property and in the `images` property.

If we'd had an existing image in place then the result would have looked like this:

{% highlight json %}

{
  "result": {
    "id": "50d1f2e1c03858f9f6000006",
    "name": "Joe Bloggs"
    "image": "http://example.org/image.jpg",
    "images": [
        {
            "url": "http://example.org/image.jpg",
            "notes": "official publicity shot",
            "source": "example.org pr office"
        },
        {
            "_id": "53b14f5bb4f7834e56083bb7",
            "url": "https://test.popit.mysociety.org/persons/50d1f2e1c03858f9f6000006/image/53b14f5bb4f7834e56083bb7",
            "created": "2014-06-30t11:51:55.251z",
            "notes": "Official Portrait",
            "mime_type": "image/jpeg",
            "source": "PR office"
        }
    ]
  }
}

{% endhighlight %}

If you need to replace an uploaded image then you can perform a `PUT` request to the image URL:

{% highlight bash %}

curl -v                                                               \
    --request PUT                                                     \
    --header "apikey: 2a0abbefe6aefb67ba5302a12570c2af2a7f4433"       \
    -F notes='Unofficial Portrait'                                    \
    -F source='Paparazzi'                                             \
    -F "image=@portrait2.jpg"                                         \
https://test.popit.mysociety.org/api/v0.1/persons/50d1f2e1c03858f9f6000006/image/53b14f5bb4f7834e56083bb7

{
  "result": {
    "id": "50d1f2e1c03858f9f6000006",
    "name": "Joe Bloggs"
    "image": "https://test.popit.mysociety.org/persons/50d1f2e1c03858f9f6000006/image/53b14f5bb4f7834e56083bb7",
    "images": [
        {
            "_id": "53b14f5bb4f7834e56083bb7",
            "url": "https://test.popit.mysociety.org/persons/50d1f2e1c03858f9f6000006/image/53b14f5bb4f7834e56083bb7",
            "created": "2014-06-30t11:51:55.251z",
            "mime_type": "image/jpeg",
            "notes": "Unofficial Portrait",
            "source": "Paparazzi"
        }
    ]
  }
}

{% endhighlight %}

Not that at the moment only the first image listed in `images` will be displayed in the Popit UI.

### Deleting an Image

To delete you can either `POST` a new document with that image missing from the `images` property or you can can use a `DELETE` request:

{% highlight bash %}

$ curl                                                          \
    --request DELETE                                            \
    --header "Apikey: 2a0abbefe6aefb67ba5302a12570c2af2a7f4433" \
https://test.popit.mysociety.org/api/v0.1/persons/50d1f2e1c03858f9f6000006/image/53b14f5bb4f7834e56083bb7

{
  "result": {
    "id": "50d1f2e1c03858f9f6000006",
    "name": "Joe Bloggs",
    images: [],
    image: null
  }
}

{% endhighlight %}

To delete all images make a `DELETE` request without an image id:

{% highlight bash %}

$ curl                                                          \
    --request DELETE                                            \
    --header "Apikey: 2a0abbefe6aefb67ba5302a12570c2af2a7f4433" \
    --header "Accept: application/json"                         \
    --header "Content-Type: application/json"                   \
https://test.popit.mysociety.org/api/v0.1/persons/50d1f2e1c03858f9f6000006/image

{
  "result": {
    "id": "50d1f2e1c03858f9f6000006",
    "name": "Joe Bloggs",
    images: [],
    image: null
  }
}

{% endhighlight %}

