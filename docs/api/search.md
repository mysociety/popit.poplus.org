---
layout: docs
title: Search
---

PopIt provides a full-featured search API that allows you to find the documents you're looking for within a collection. It supports wildcard queries, date ranges and more.

## Search collections

Find records via various criteria.

    GET /api/v0.1/search/:collection

### Parameters

- `q` - Your search query, you can also specify which fields to search - see below.
- `page` - Access further pages by specifying this parameter. (Max 1000)
- `per_page` - Number of results to return per-page. (Max 100)

The `q` parameter can also include any valid popolo field for the collection being searched as a prefix.

### Examples

#### Specifying fields

If you wanted to search for people named `John` then your query might look like:

    /api/v0.1/search/persons?q=name:John

In this url the `q` parameter is requesting records where the `name` field contains `John`.

#### Wildcards

Wildcard searches can be run on individual terms, using `?` to replace a single character, and `*` to replace zero or more characters:

    /api/v0.1/search/persons?q=qu?ck+brown*jumps

#### Exact match

If you want an exact match then you can surround your query with quotation marks:

    /api/v0.1/search/persons?q="John+Smith"

This will only match the exact string specified.

#### Ranges

You can match date ranges using the API, e.g. to find all memberships which started in 2013:

    /api/v0.1/search/memberships?q=start_date:[2013-01-01+TO+2013-12-31]

#### Searching Dates

There is a specific end point for searching dates.

    GET /api/v0.1/search_dates/:collection

You specify the date fields as separate query parameters and it handles documents where the date you are searching for is missing. e.g to search for all memberships of an organization between 2010 and 2012:

    /api/v0.1/search_dates/memberships?q=organization_id=2000&start_date=<2010-01-01&end_date=>2012-12-31

The results for this will also include memberships with no `start_date` or no `end_date`. It's possible to construct a query that does this using the normal search interface, this is just a slightly more convenient way to do so.


### Further reading

The underlying search engine is elasticsearch. For more details about the kinds of query you can perform, check out the [query string syntax guide](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/query-dsl-query-string-query.html#query-string-syntax).
