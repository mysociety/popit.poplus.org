---
layout: docs
title: Using the Popolo data model
---

PopIt uses the [Popolo specification](http://www.popoloproject.com/) to model people, organizations and their relationships. Understanding the best way to use this to model your needs is key to getting the best out of PopIt. This page aims to explain to you the basic concepts of Popolo and how to use them in some common scenarios that come up when modeling parliaments.

### Basic concepts ###

Popolo, and hence PopIt, data models are based on a few key objects:

   * [Persons](http://www.popoloproject.com/specs/person.html)
   * [Organizations](http://www.popoloproject.com/specs/organization.html)
   * [Memberships](http://www.popoloproject.com/specs/membership.html)
   * [Posts](http://www.popoloproject.com/specs/post.html)

#### Persons ####

A Person in PopIt, exactly as you'd imagine, stores data about a person.

#### Organizations ####

An organization is used to represent something to which people belong such as a parliament, political party or a company.

#### Memberships ####

A membership represents an association between two things. Mostly you'll use this to indicate that a person is a member of an organization.

#### Posts ####

A post is for the sort of position that you want to be able to refer to even if no one is currently occupying it. So, for example, if you are modelling a parliament that has constituency seats ( e.g Member of Parliament for Oxford East ) the you could add a post for each constituency which would allow you to see a list of everyone who'd represented a particular constituency. If you don't have these sort of positions, or only need to refer to positions in the context of organizations or persons, then you probably don't need posts.

### But what about roles? ###

When you add a membership you may want to include some extra information about how the person is related to an organization, e.g. if they are a candidate for a political party. Roles are used for adding this information. So, in our example of a candidate for a political party, having added the person and organization you would then add a membership linking the person to the party with the role set to candidate.

### How to use them together ###

As we envisage that PopIt will mostly be used to model people in parliaments we're going to provide a set of examples of how we'd use PopIt to model common parliamentary types. They should give you an idea of how the objects above can be used in practice.

#### Party lists, no areas ####

We're going to start with the simple example of a parliament where the members are selected proportionally from party lists. In this case you would have an organization for the Parliament, and one for each party. You would then add each person who was a member of parliament and add a membership for parliament and one for their party.

For a typical member of parliament this would look something like:

{% highlight json %}
{
    "id": "org.mysociety.za/person/104",
    "family_name": "Sisulu",
    "given_names": "Max Vuyisile",
    "name": "Max Vuyisile Sisulu",
    "memberships": [
      {
        "person_id": "org.mysociety.za/person/104",
        "organization_id": "org.mysociety.za/party/anc",
        "id": "org.mysociety.za/membership/104"
      },
      {
        "id": "org.mysociety.za/membership/743",
        "organization_id": "org.mysociety.za/house/national-assembly",
        "role": "Member",
        "person_id": "org.mysociety.za/person/104",
      }
    ]
  }
}
{% endhighlight %}

#### Party lists for geographic areas ####

In this case candidates are still elected proportionally from party lists but the lists are specified per area. E.g. There would be a set of party lists for South West England and multiple people would be elected for this area. In this case the model is the same as previously with the addition that when creating the memberships you assign an area to the membership.

In this case the membership for the parliament would look something like this:

{% highlight json %}
{
  "id": "org.mysociety.za/membership/743",
  "organization_id": "org.mysociety.za/house/national-assembly",
  "role": "Member",
  "person_id": "org.mysociety.za/person/104",
  "area": {
    "name": "South West"
  }
}
{% endhighlight %}

#### Constituency based parliament ####

In this model there are a number of constituencies in which one member is elected. In this case we add a post for each constituency in the parliament. The elected representatives are then associated with the post using a membership.

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
    },
    {
      "id": "5448efe57f292c7a67b292ab",
      "person_id": "david-cameron",
      "post_id": "65622",
      "role": "Member",
    }
  ],
}
{% endhighlight %}

As you can see there are two memberships, one for the conservative party and a second membership which is of a post rather than an organization. The post looks something like this:

{% highlight json %}
{
  "id": "65622",
  "area": {
    "name": "Witney"
  },
  "organization_id": "commons",
  "role": "Member of Parliament",
}
{% endhighlight %}

It is the post that is associated with the organization for the House of Commons and so provides the link from the person through to the organization.

### Further help ###

There's lots of documentation on the [Popolo site](http://www.popoloproject.com/) but if you're not sure of the best way to set up the model for your intended use of PopIt then you can ask on the [Poplus mailing list](https://groups.google.com/forum/#!forum/poplus).
