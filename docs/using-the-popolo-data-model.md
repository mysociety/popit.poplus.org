---
layout: docs
title: Using the Popolo data model
---

PopIt uses the [Popolo specification](http://www.popoloproject.com/) to model people, organizations and their relationships. Understanding the best way to use this to model your needs is key to getting the best out of PopIt. This page aims to explain to you the basic concepts of Popolo and how to use them in some common scenarios that come up when modeling parliaments.

### Basic concepts ###

Popolo, and hence PopIt, data models are based on a few key objects:

   * Persons
   * Organizations
   * Memberships
   * Posts

#### Persons ####

A Person in PopIt, exactly as you'd imagine, stores data about a person.

#### Organizations ####

An organization is used to represent something to which people belong such as a parliament, political party or a company.

#### Memberships ####

A membership represents an association between two things. Mostly you'll use this to indicate that a person is a member of an organization.

#### Posts ####

A post is for the sort of position that it's useful to represent independent of the organization it's part of or the person who holds it. Unless you are modeling a parliament that has constituency seats ( e.g. Member of Parliament for Oxford East ) then you probably won't need posts.

### But what about roles? ###

It's possible for a membership, or a post, to have a role. A role is used to indicate something about the nature of the membership. E.g. for people in a party you could set the role of the membership to be member. Or for people on a partly list the role could be candidate.

### How to use them together ###

As we envisage that Popit will mostly be used to model people in parliaments we're going to provide a set of examples of how to model common parliamentary types.

#### Party lists, no areas ####

We're going to start with the simple example of a parliament where the members are selected proportionally from party lists. In this case you would have an organization for the Parliament, and one for each party. You would then add each person who was a member of parliament and create a membership for parliament and one for their party.

For a typical member of parliament this would look something like:

{% highlight json %}
{
    "id": "org.mysociety.za/person/104",
    "family_name": "Sisulu",
    "given_names": "Max Vuyisile",
    "name": "Max Vuyisile Sisulu",
    "memberships": [
      {
        "url": "http://za-peoples-assembly.popit.mysociety.org/api/v0.1/memberships/org.mysociety.za/membership/104",
        "contact_details": [],
        "links": [],
        "person_id": "org.mysociety.za/person/104",
        "organization_id": "org.mysociety.za/party/anc",
        "id": "org.mysociety.za/membership/104"
      },
      {
        "url": "http://za-peoples-assembly.popit.mysociety.org/api/v0.1/memberships/org.mysociety.za/membership/743",
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

#### Party lists for geographic areas ####

In this case candidates are still elected proportionally from party lists but the lists are specified per area. E.g. There would be a set of party Lists for South West England and multiple people would be elected for this area. In this case the model is the same are previously with the addition that when creating the memberships you assign an area to the membership.

In this case the membership for the parliament would look something like this:

{% highlight json %}
{
  "url": "http://za-peoples-assembly.popit.mysociety.org/api/v0.1/memberships/org.mysociety.za/membership/743",
  "id": "org.mysociety.za/membership/743",
  "organization_id": "org.mysociety.za/house/national-assembly",
  "role": "Member",
  "person_id": "org.mysociety.za/person/104",
  "label": "Member",
  "start_date": "2009-05-06",
  "area": {
    "name": "Area Name"
  },
  "links": [],
  "contact_details": []
}
{% endhighlight %}

#### Constituency based parliament ####

In this model there are a number of constituencies in which one member is elected. In this case we create a post for each constituency in the parliament. The elected representatives are then associated with the post using a membership.

In this case a member would look something like this:

{% highlight json %}
{
  "id": "david-cameron",
  "name": "David Cameron",
  "gender": "male",
  "birth_date": "1966-10-09",
  "memberships": [
    {
      "id": "5448edf57f292c7a67b28621",
      "person_id": "david-cameron",
      "organization_id": "conservative-party",
      "links": [],
      "contact_details": [],
    },
    {
      "id": "5448efe57f292c7a67b292ab",
      "person_id": "david-cameron",
      "post_id": "65622",
      "role": "Member",
      "start_date": "2005-05-06",
      "end_date": "2010-05-06",
      "links": [],
      "contact_details": [],
    }
  ],
}
{% endhighlight %}

As you can see the second membership is not for an organization but a post. The post looks something like this:

{% highlight json %}
{
  "id": "65622",
  "area": {
    "identifier": "http://mapit.mysociety.org/area/65622",
    "id": "mapit:65622",
    "name": "Witney"
  },
  "organization_id": "commons",
  "role": "Member of Parliament",
  "label": "Member of Parliament for Witney",
  "start_date": "2005-05-06",
  "memberships": [
    {
      "id": "5448efe57f292c7a67b292ab",
      "person_id": "david-cameron",
      "post_id": "65622",
      "role": "Member",
      "start_date": "2005-05-06",
      "end_date": "2010-05-06",
      "links": [],
      "contact_details": [],
    },
  ],
  "links": [],
  "contact_details": [],
}
{% endhighlight %}

As you can see the post itself is associated with the organization for the House of Commons and it is this that provides the link from the person through to the organization.

### Further help ###

There's lots of documentation on the [Popolo site](http://www.popoloproject.com/) but if you're not sure of the best way to set up the model for your intended use of PopIt then you can ask on the [Poplus mailing list](https://groups.google.com/forum/#!forum/poplus).
