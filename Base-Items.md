*"Organization" and "diaperbank" are used interchangeably in this document*

# Overview
Base Items were added as a significant refactor in Issue #314. There is a model, `BaseItem`, that all `Item`s derive from (though it is not a subclass). There are two endpoints: a read-only global public one, and a read/write Super Admin endpoint. 

When an organization is created, all the existing `BaseItem` records are replicated as `Item` instances for that organization. Organizations have read/write access to their own `Item`s. Practically speaking, this allows each Diaperbank's inventory offerings to be siloed and customized to their needs while still allowing a sensible set of default items which all organizations and partners have access to. 

Each `BaseItem` has a "Partner Key" (a short string `/[a-z0-9_]{4,}/`) that is used as a contextual lookup index (effectively equivalent to an `:id` field, that can be used meaningfully from Partner requests.)

## Purpose
The Base Item subsystem is so that organizations have the ability to customize the kind of inventory offered by their Diaperbank: not all organizations may carry the same kinds of products. Feedback from some Diaperbanks indicated that they would like to name or classify their inventory slightly differently, or to omit some items entirely if they aren't ever carried. Not all Diaperbanks run exactly the same, and it would be far more bloating to make the system satisfy all of them *and* it would create a potential hardship (particularly for smaller diaperbanks or those with narrower need) to force all diaperbanks to adhere to certain inventory sets in order to use our software.

# Architecture
A `BaseItem` instance serves two primary purposes, architecturally:

 1. It serves as a template for the `Item`s that an organization begins with when it is created
 1. It serves as a "parent" to new `Item`s created by an organization

## As a Template
When an organization is created, a method runs that iterates over all `BaseItem`s and replicates them into `Item` records, using the same name. This allows a generic basis for a new organization to begin using the application, similar to "seeding" data. An organization can then edit, delete, or just use those records as it sees fit, without any worry that it will be disrupting other organizations.

## As a Parent
Every `BaseItem` `has_many :items`, and every `Item` `belongs_to :base_item`. This allows for queries like:
```
> all_3t_diapers = BaseItem.find_by(name: "3T Diapers").items
> all_3t_diapers.pluck(:name).uniq
["3T Diapers", "3T Diapers (boys)", "Huggies 3T", "Luvs 3T", "3T Blue Diapers", "Toddler-size (3) Diapers" ...]
> all_3t_diapers.pluck(:organization_id).uniq
[23, 24, 25, 27, 30]
```

When an organization wishes to add a new `Item` type to their inventory, they are required to pick an existing `BaseItem` as a super-type. This super-type is often listed alongside the `Item.name` in result sets. 

Organizations are never *required* to rename their `Item` records, nor even create alternate subtypes, but the possibility is there, at least, and the `BaseItem` basis ensures that the data does not degenerate too dramatically.

# Current List
See [`db/base_items.json`](https://github.com/rubyforgood/human-essentials/blob/main/db/base_items.json)

# History

## PartnerBase

Since Partners were originally in a separate PartnerBase app and communicated via API to DiaperBase, using a "partner key" field was necessary.

As we developed plans for externalizing the Partner app, it became clear that there needed to be a "common language" that was shared across all diaperbanks, so that communication with the Partner app would be consistent.

## Problems Addressed
The following issues were related to the original problem:
 * [#314 - Item Refactor](https://github.com/rubyforgood/diaper/issues/314)
 * [#250 - Identify models that have side effects problems when their records are deleted](https://github.com/rubyforgood/diaper/issues/250)
 * [#239 - Add ability to "hide" item rows with zero-quantities](https://github.com/rubyforgood/diaper/issues/239)
 * [#229 - Deleting Inventory Items](https://github.com/rubyforgood/diaper/issues/229)
 * [#345 - Add Category Drop-down Menu for creating New Inventory items](https://github.com/rubyforgood/diaper/issues/345) {sorta}

Diaperbanks were editing / deleting Item types because it was different than their offerings. The scale page was breaking because there was mismatch between the Items available and the ones it was expecting (types were hardcoded into the scale page). 