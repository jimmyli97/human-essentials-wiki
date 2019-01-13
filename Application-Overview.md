This document is intended for general reference, but primarily for facilitating on-boarding of developers that are new to Diaperbase. Please feel free to contribute to this document, but bear in mind:

 - The purpose of this document is to provide a preliminary understanding of how this app functions
 - The audience is "Rails developers", so it's ok to use Rails jargon, but concepts related to diaperbanking may need to be explained
 - Aim for a level of specificity that is functional: it should explain how data flows through the app, but avoid being overly technical about implementation, unless it's particularly noteworthy or a potential danger area

# Background
Diaperbanks are similar to Foodbanks, if you've heard of those before. They are small repositories of sanitary materials primarily used by infants and children, and the typical recipients are underprivileged or lower-income families. According to the National Diaperbank Network (NBDN), there are ~120 registered diaperbanks, and likely many others that are not registered with the NBDN. Diaperbanks also occasionally provide other sanitary materials such as menstrual supplies, car seats, and other items that can be dispensed to defray the costs of child-rearing.

One of the biggest problems consistently faced by Diaperbanks is the means to track their inventory, which sometimes sprawls across multiple storage locations. Most of them typically come up with some spreadsheet-based solution (Excel, Google Sheets, etc.), though some have used free or low-cost inventory software applications. Both solutions present challenges, either lack of data integrity and high labor cost in maintenance (the former) or lack of extensibility for adapting to new features (the latter).

# Purpose
This application is a solution that is custom-tailored around the specific needs of Diaperbanks. We initially worked directly with several diaperbanks to ascertain exactly how we could best meet their needs with software, and we offer it to them for free, since many diaperbanks are volunteer-driven and/or grant-funded, and any money they would spend on an application is money diverted from swaddling children.

The scope of this application (Diaperbase) is:

 - tracking incoming, outgoing, and retained inventory in a robust manner
 - providing reports and status of the inventory to the diaperbanks
 - facilitating their reporting needs (to both the NBDN as well as any Grantors and community partners)
 - providing fulfillment for incoming requests from community partners (via PartnerBase, see Appendix)

Things that are out of scope include:
 - Directly tracking data of families, children, and recipients of sanitary supplies
 - Directly working with community partners (see Appendix)
 - Any eCommerce functions (including the purchasing or vending of inventory)
 - Performing Human Resource functions

# Introduction
Before we delve into the inner-workings of how this application functions, we'll begin with a 10,000 foot view of how inventory moves through the application. This will include some key terms in **bold**, those terms will be further defined in the Appendix.

## The Flow of Data
![diaperbase-diagram](https://user-images.githubusercontent.com/502363/51081485-5eb49100-16be-11e9-9d10-c8bfa6572968.jpg)

### Intake
A Diaperbank will acquire inventory primarily through one of two methods: a **Donation** or a **Purchase**. Purchases are straightforward; the Diaperbank spends its own money to purchase inventory. Donations can be received through a few different means.

**Diaperdrives** are like food drives -- a campaign, typically with advertisement to the community, for the general public to provide needed items to the diaperbank. Sometimes people will also donate inventory at a local **Donation Site**, outside of a Diaperdrive. Other than those two primary methods, there is a miscellaneous classification for diapers that are received (but not purchased) through other means.

# Appendix

## Definitions & Terms

**Diaperdrive** - This is similar to a fund-raiser or food-drive. It is an often advertised campaign to the community with a declared intention to encourage donations from community members. Sometimes the Diaperdrive is held by individuals or organizations that are not the Diaperbank themselves. Diaperbanks like to track data on how successful their diaperdrives are, so we provide a means to track it. Internally, this is represented by the `DiaperDriveParticipant` model.

**Donation** - This is one of the two ways that inventory is added to a Diaperbank. Donations are inventory that is provided at no cost to the Diaperbank. Internally, they are represented by the `Donation` model.

**Donation Site** - These are physical locations where the general public can bring needed inventory to be donated to the Diaperbank. They have a geographical address and are generally named. Internally, they are represented by the `DonationSite` model.

**Partner** - This refers to a "Community Partner", an individual or organization in the surrounding community that works directly with the families, tracks their data, and schedules distributions of diapers and sanitary supplies. Sometimes this will be referred to as a "Partner Organization" or "Community Partner". Partners request disbursements of inventory from a Diaperbank, the Diaperbank fulfills those requests, and then the Partner provides them to the families.

**Purchase** - This is inventory that was purchased for cash by the diaperbank, directly. We track these so that the diaperbanks can report how much money they spent directly on inventory. Internally, these are represented by the `Purchase` model.

## Partnerbase
[Partnerbase](/rubyforgood/partner) is a companion application built in-tandem with this application. It interfaces directly with the Community Partners that work with the recipients of the sanitary supplies. Both applications are dependent on one another, but each address separate needs and so are maintained separately. They connect via an API. Please see the Partnerbase repository for more information.