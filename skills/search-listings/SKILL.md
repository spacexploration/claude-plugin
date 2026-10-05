---
name: search-listings
description: Find and compare commercial real estate on SPACEXPLORATION — industrial, office, retail, multifamily, and land, for sale or lease, anywhere in the US. Use when the user wants to search for commercial property, filter by price, size, cap rate, location, or building features, look up a specific listing, or compare listings side by side. No account needed.
---

# Search SPACEXPLORATION listings

The user's explicit instructions take precedence over this skill.

SPACEXPLORATION is a free MLS for commercial property. Every read tool works without signing in.

## Choose a starting point

- **The user asks in plain language** ("warehouses near Denver under $5M with dock doors"): call `interpret_search` with their sentence as `prompt`. Pass the `filters` it returns to `search_listings`. If it also returns `bounds`, pass those as well. Bounds hold the "near <place>" part, and without them the search covers the whole country.
- **You need precise or repeated control:** call `get_registry` once and build `filters` yourself. Anonymous callers can make about 6 `interpret_search` calls per minute, so do this for follow-up refinements too.

## Build filters from the registry

`get_registry` returns `attributes` and `sort_options`. Each attribute's `field` is the only valid filter key. Shape each value by the attribute's `filter`:

| `filter` | Filter value |
| --- | --- |
| `range` | `{"min": …, "max": …}`. Either bound is optional. Dates take ISO dates, e.g. `{"min": "2027-01-01"}` |
| `any_of` | array of the attribute's `values`, e.g. `["industrial", "office"]`. Where `values` are `{value, label}` pairs, send the `value` |
| `boolean` | `true` or `false` |
| `equals` | a single exact value |

- `applies_to` and `applies_to_listing_type` say which property and listing types a field is meaningful for. For example, cap rate applies to sale listings and lease structure to lease listings. Filtering on a field that doesn't apply silently excludes every other type.
- Location: `state` takes two-letter codes (`["CO"]`), and `city` and `county` take exact names. Use them when the user names a place and you have no `bounds`; without a location filter or `bounds` the search covers the whole country.
- `sort` must be one of `sort_options`, e.g. `price:asc`, `attr_cap_rate:desc`, or `views_30d:desc` for popular listings.
- `query` is free text matched against address, city, and keywords. Use it for a street address or a term that isn't a registry field.

```json
{
  "filters": {
    "property_type": ["industrial"],
    "listing_type": ["sale"],
    "price": { "max": 5000000 },
    "attr_industrial_dock_doors": { "min": 1 }
  },
  "bounds": { "sw_lat": 39.4, "sw_lng": -105.3, "ne_lat": 40.1, "ne_lng": -104.6 },
  "sort": "price:asc"
}
```

## Read results

`search_listings` returns `hits`, `facets` (counts per value, useful for suggesting refinements), `found`, `page`, and `last_page`. Pass `page` to page through results; there are 24 hits per page.

Each hit carries a `hashid`, the listing page `url`, and two timestamps: `listed_at` (when it went live) and `updated_at` (when it last changed). Call `get_listing` with the `hashid` for the full record: pricing, every populated attribute with its label and unit, parcel and public-record data, photos, and documents. Broker contact details appear only when the caller is allowed to see them.

Link the user to a listing with its `url`. Don't build the link yourself.

## Report honestly

- Say how many listings matched (`found`), and when a search came back empty, say which filter was most restrictive.
- When recency matters, say when a listing was listed or last updated.
- Present only values the tools returned. A missing attribute means the listing doesn't state it. Don't fill it in.
- Some attributes are computed from public sources: flood zone, census demographics, walk and transit scores, proximity, market medians, and `attr_record_*` assessor data. When those matter to the decision, say they come from public data, not from the broker.
