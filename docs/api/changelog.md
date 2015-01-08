---
layout: docs
title: API Change Log
description: Changes to the API by version
---

## API Change Log

This page lists the changes made to the API by version. It pays specific attention to documenting and incompatible changes between versions.

## v0.2

The following backward incompatible changes were made in the 0.2 version
of the API. These may cause a change in behaviour to your code if you
were relying on the existing behaviour.

### PUT semantics

Despite the documentation saying that the whole document would be
replaced in PUT requests it would in fact only update or add the fields present
in the PUT. This means that you could add fields to a document by
only sending the new fields and the rest of the document would remain
unchanged.

In version 0.2 the entire document is replaced so any fields ommited will be deleted.

### Inline memberships

If you include a memberships key in a PUT or POST this will now create, update or delete memberships for the associated record. Previously this key was ignored.

This is particularly relevant if you PUT a document with memberships set to [] as this will delete all existing memberships.

See [Inline Memberships](/docs/api/writing#inline-memberships) for details.

### {} and [] and no longer converted to null

Previously a key that included an empty object (`{}`) or array (`[]`) would be converted to `null`. This no longer happens.

See [Issue 710](https://github.com/mysociety/popit/issues/710) for details.

## v0.1

This was the initial version.
