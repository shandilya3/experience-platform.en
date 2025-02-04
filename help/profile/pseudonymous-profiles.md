---
keywords: Experience Platform;home;popular topics;dataset;Dataset;time to live;ttl;time-to-live;pseudonymous;pseudonymous profiles;data expiry;expiry;
solution: Experience Platform
title: Pseudonymous Profile data expiration
description: This document provides general guidance on configuring data expiration for Pseudonymous Profiles within Adobe Experience Platform.
hide: true
hidefromtoc: true
---

# Pseudonymous profiles data expiration [!BADGE Limited release]

In Adobe Experience Platform, a profile is considered for Pseudonymous data expiration if it meets the following conditions: 

- The stitched profile's identity types match what the customer has specified as a pseudonymous or unknown identity type. 
  - For example, if the profile's identity type is `ECID`, `GAID`, or `AAID`. The stitched profile has no IDs from any other identity type. In this example, a stitched profile does **not** have either an email or CRM identity. 
- No activity has taken place in a user-defined amount of time. Activity is defined either by any Experience Events being ingested or customer-initiated updates to the profile attributes. 
  - For example, a new page view event or age attribute update is considered as an activity. However, a non-user-initiated segment membership update is **not** considered as an activity. Currently, to compute data expiration, the tracking at a profile level is based on the time of ingestion.

## Access {#access}

Pseudonymous Profile data expiration cannot be configured through the Platform UI or APIs. Instead, you must contact support in order to enable this feature. When contacting support, please include the following information: 

- The identity types to be considered for Pseudonymous profile deletes. 
  - For example: `ECID` only, `AAID` only, or a combination of `ECID` and `AAID`.
- The amount of time to wait before deleting a pseudonymous profile. The default recommendation for customers is 14 days. However, this value may differ based on your use case.
- The current profile count compared to the license profile count.

## Frequently asked questions {#faq}

The following section lists frequently asked questions regarding Pseudonymous profiles data expiration:

### What users should be using Pseudonymous profiles data expiration?

- If you are using a streaming source that directly sends data to Platform.
- If you have a website that serves unauthenticated customers en masse.
- If you have excessive profile counts in your datasets and have confirmed that this excessive profile count is because of anonymous cookie-based identity type.
  - To determine this, you should use the identity type overlap report. More information about this report can be found in the [identity overlap report section](./api/preview-sample-status.md#identity-overlap-report) of the preview sample status API guide.

### What are some caveats you should be aware of before using Pseudonymous profiles data expiration?

- Pseudonymous profile data expiration will run on the **production** sandbox.
- Once you have activated this feature, the deletion of profiles is **permanent**. There is **no** way to roll back or restore the deleted profiles.
- This is **not** a one-time cleanup job. Pseudonymous profile data expiry will continually run once per day and delete profiles that match the customer's input.
- **All** profiles that are defined as Pseudonymous profiles will be affected by the Pseudonymous profile data expiration. It does **not** matter if the profile is Experience Event only or if it only contains profile attributes.
- This cleanup will **only** occur in Profile. Identity Service may continue to show the deleted identities within the graph after the cleanup in cases where the profile has two or more associated pseudonymous identities (such as `AAID` and `ECID`). This discrepancy will be addressed in the near future.

### How does Pseudonymous Profile data expiry differ from the existing Experience Event data expiry?

Pseudonymous Profile data expiry and Experience Event data expiry are complementary features.

#### Granularity

Experience Event data expiration works on a **dataset** level. As a result, each dataset can have a different data expiry setting.

Pseudonymous Profile data expiration works on a **sandbox** level. As a result, the data expiration will affect all profiles in the sandbox.

#### Identity types

Experience Event data expiration removes events **only** based on the event record's timestamp. The identity types included are **ignored** for expiration purposes.

Pseudonymous Profile data expiration **only** considers profiles that have identity graphs which contain identity types that were selected by the customer, such as `ECID`, `AAID`, or other types of cookies. If the profile contains **any** additional identity type that was **not** in the customer's selected list, the profile will **not** be deleted.

#### Removed items

Experience Event data expiration **only** removes events and does **not** remove profile class data. The profile class data is only removed when all the data is removed across **all** datasets and there are **no** profile class records remaining for the profile.

Pseudonymous Profile data expiration removes **both** event and profile records. As a result, the profile class data will also be removed.

### How can Pseudonymous Profile data expiry be used in conjunction with Experience Event data expiry?

Pseudonymous Profile data expiry and Experience Event data expiry can be used to complement each other.

You should **always** set up Experience Event data expiry in your datasets, based on your needs of retaining data about your known customers. Once Experience Event data expiry is set up, you can use Pseudonymous Profile data expiry to automatically remove Pseudonymous Profiles. Typically, the data expiry period for Pseudonymous Profiles is less than the data expiry period for Experience Events.

For a typical use case, you can set your Experience Event data expiry based on the values of your known user data and you can set your Pseudonymous Profile data expiry to a much shorter duration to limit the impact of Pseudonymous profiles on your Platform license compliance.
