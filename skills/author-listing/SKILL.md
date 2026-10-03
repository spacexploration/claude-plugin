---
name: author-listing
description: Create, update, and publish a commercial real estate listing on SPACEXPLORATION from a flyer, offering memorandum, or details the user provides. Use when a broker or owner wants to list a property for sale or lease, turn a flyer or OM into a listing, add photos, edit or publish a listing they own, check publish progress, or take a listing off the market. Requires a SPACEXPLORATION account.
---

# Author a listing on SPACEXPLORATION

The user's explicit instructions take precedence over this skill. When the user has already asked for a specific action, such as publishing or withdrawing a specific listing, carry it out without asking again.

Listing on SPACEXPLORATION is free. Authoring tools act on the user's own account. The first one to run asks the user to sign in through the browser. The account also needs:

- **A verified email address and a completed onboarding** to create or edit listings.
- **A verified phone number** to publish.

When a tool reports one of these as missing, pass the message on, including the link it contains, and stop. These checks are completed on spacexploration.com, not through tools.

## Honesty comes first

A listing is a public, broker-attributed record. **Enter only what the user or their document states.** Leave a field empty when the source is silent; don't estimate, round, or infer. When publishing, SPACEXPLORATION fills computed facts such as flood zone, demographics, public records, and market data from authoritative sources. A guessed value in a broker-stated field is worse than a blank. When something is ambiguous, such as price per square foot versus a total price, ask.

## Flow

Every step after creation uses the listing's `hashid`.

1. **`geocode_address`** turns the property's address into the coordinates, county, and `place_id` that `create_listing` requires. Use its output as-is; don't geocode some other way.
2. **`create_listing`** takes the geocoded fields plus `property_type` (a value from the `get_registry` property_type attribute) and `listing_type` (`sale` or `lease`). Include `apn` when the document gives a parcel number. Pass an `idempotency_key` (a UUID) so a retry can't create a duplicate. It returns a draft with its `hashid`.
3. **`update_listing`** sets the core groups. Send each group whole, not just the field that changed:
   - `pricing`: `sale_price` for a sale, or `lease_rate` plus `lease_rate_period` for a lease. Set `price_on_request: true` when no price is published.
   - `marketing`: `headline`, `summary`, `highlights` (an array of short strings). Draw these from the source's own wording.
   - `parcel`: `lot_size` is **integer square feet**. Convert acres × 43,560 and set `lot_size_unit` to the unit the source used.
4. **`set_listing_attributes`** sets the detailed fields: a map from registry `field` to value, e.g. `{"attr_industrial_clear_height": 32, "attr_industrial_dock_doors": 4}`. Only fields `get_registry` marks `"writable": true` are accepted. Send `null` to clear a field.
5. **Photos.** `attach_listing_photo` takes one public `https` image URL per call. The server fetches the image and runs content moderation on it. A photo whose `moderation_status` is `rejected` or `escalated` stays hidden and blocks publishing until it is removed with `delete_attachment` or replaced. For a local file, `sign_attachment_upload` returns a presigned URL to `PUT` the bytes to, then `confirm_attachment_upload` finalizes it. This path only works where you can make HTTP requests yourself, such as a coding agent with shell access.
6. **Review with the user.** Call `get_listing`, summarize what will go public, and get explicit approval before publishing.
7. **`publish_listing`** starts an asynchronous pipeline that enriches the listing and then makes it public. If the listing isn't ready, the tool returns a checklist of what is missing; resolve those items and try again.
8. **`get_publish_status`** with the `hashid` reports progress. Poll it until `state` is `completed` or `failed`. On `failed`, report the reason it gives. Once published, the listing is live at `https://spacexploration.com/listing/{hashid}/{address_slug}`.

## Manage existing listings

- `list_my_listings` returns the user's listings, optionally filtered by `status` (`draft`, `active`, `sold`, `leased`, `withdrawn`, …).
- `withdraw_listing` removes a listing from public search **permanently**: a withdrawn listing can't be reactivated, and relisting means creating a new listing. Explain this and get the user's confirmation before calling it.
