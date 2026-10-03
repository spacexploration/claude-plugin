---
name: saved-searches
description: Set up and manage SPACEXPLORATION saved searches that email the user when new commercial listings match. Use when the user wants to be alerted, notified, or kept posted about new commercial property that fits their criteria, or wants to review or remove their existing alerts. Requires signing in to SPACEXPLORATION.
---

# Saved searches on SPACEXPLORATION

A saved search stores a set of filters on the user's SPACEXPLORATION account. When new listings that match are published, SPACEXPLORATION emails the user a daily digest. The user can also manage their saved searches on the web at https://spacexploration.com/my/saved-searches.

These tools need the user's account. The first time one runs, the user is asked to sign in to SPACEXPLORATION in the browser. If they decline, say that alerts need an account and offer to keep searching without one.

## Create one

1. **Agree on the criteria first.** Run the search with the `search-listings` skill and show the user what currently matches, so they confirm the filters are right before anything is saved.
2. **Check for duplicates.** Call `list_my_saved_searches`. If a saved search already covers the same criteria, tell the user. There is no update tool, so changing one means deleting the old search and saving a new one, and only with the user's go-ahead.
3. **Encode the location as filters.** `save_search` stores only `query` and `filters`, not map `bounds`. A search that relied on `bounds` for "near Denver" has to be saved with `city` or `county` filters instead, such as `"city": ["Denver", "Aurora", "Lakewood"]`. Tell the user which places the alert covers.
4. **Save it.** Call `save_search` with a short descriptive `name`, the `filters`, and `query` if the search used free text:

```json
{
  "name": "Denver-area industrial under $5M",
  "filters": {
    "property_type": ["industrial"],
    "listing_type": ["sale"],
    "price": { "max": 5000000 },
    "city": ["Denver", "Aurora", "Commerce City"]
  }
}
```

Confirm what you saved and that alerts go to the email address on their account.

## Review and remove

- `list_my_saved_searches` returns each saved search with its numeric `id`, filters, and alert schedule.
- `delete_saved_search` takes that `id`. Deleting can't be undone, so name the search you're about to delete and get the user's confirmation first.
