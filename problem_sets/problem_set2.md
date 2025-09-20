# Concepts for URL Shortening

The contexts guarantee that any two namespaces, or URL generations, can generate short URLs without producing the same result. In a URL shortening app, the context will be the short URLs (domain/host) that are used to register links.

Having a set of used strings enforces that each nonce is new and unique within its context. For each context, the counter would hold the same value as the length of the set of used strings. Increasing the counter would extend the used strings by one new element, ensuring that no elements would be repeated.

An advantage is that this implementation is easy for the user to understand and navigate through. A disadvantage is that it lacks privacy, since it’s easier for an outside user to guess the shortened URL if it is a simple key word.

## Synchronizations for URL Shortening

Only the `shortUrlBase` is needed to generate and align with the correct nonce context. `targetUrl` is necessary when we are actually registering and creating the URL mapping.

There are cases where you may want to use a different variable name or prevent two arguments from having the same field name.

At this stage, the program already has the information it needs to use the URL data. Including a request would be irrelevant in the post-registration phase.

You would remove the `shortUrlBase` variable (since it is not unnecessary), pass in a constant through the context instead, and register using this constant to get a fixed base.

sync expire
    when ExpiringResource.expireResource (): (resource: shortUrl)
    then UrlShortening.delete (shortUrl)

## Extending the Design

**concept** UrlShorteningAnalytics [Resource]

purpose: track the amount of accesses per shortening and assemble reports

principle: each time a shortening is accessed, the count increments by one

state:

a set of Counters with
    a count Number
    a resource Resource

actions:

initialize (resource: Resource)
requires no counter already exists for resource
effect creates a new counter set to 0

recordAccess (resource: Resource)
requires a counter already exists for resource
effect increments the counter by one

readCount (resource: Resource)
requires a counter already exists for resource
effect returns the count associated with resource

deliverAnalytics (resource: Resource, recipient: Owner, count: Number)
requires true
effect delivers the count of resource to the recipient

**concept** ResourceOwnership [Resource, Owner]

purpose: ensures that only the owner of a shortening may view its analytics

principle: a resource can only have a single owner, only the owner is allowed to view the state of shortening’s analytics

state:

a set of Ownerships with
    a resource Resource
    an owner Owner

actions:

claimOwnership (resource: Resource, claimer: Owner)
requires no ownership already exists for the resource
effect records that the owner now owns the resource

removeOwnership (resource: Resource)
requires ownership already exists for the resource
effect removes the ownership from resource

authorize (resource: Resource, requester: Owner): (isTrue: boolean)
requires ownership already exists for the resource
effect returns true if the requester is actually the owner

**sync** create

when:

Request.shortenUrl (owner)
UrlShortening.register (): (shortUrl)

then:

ResourceOwnership.claimOwnership (resource: shortUrl, owner)
UrlShorteningAnalytics.initialize (resource: shortUrl)

**sync** access

when: UrlShortening.lookup (shortUrl)

then: UrlShorteningAnalytics.recordAccess (resource: shortUrl)

**sync** viewAnalytics

when:

Request.viewAnalytics (requester, shortUrl)
ResourceOwnership.authorize (resource: shortUrl, requester)
UrlShorteningAnalytics.readCount (resource: shortUrl): (count)

then:

UrlShorteningAnalytics.deliverAnalytics (resource: shortUrl, requester, count)

## Feature Requests

- **Users choose their own short URLs:** We could add a new request action that allows users to choose their own `shortUrlBase`, `shortUrlSuffix`, etc. This would be similar to `Request.shortenUrl()` but with extra parameters. We could reuse the `UrlShortening.register()` with the provided `shortUrlSuffix`.

- **“Word as nonce” strategy:** We could introduce a new concept (like `WordNonceGeneration`) that works in parallel with our current concept. It uses the `NonceGeneration` schema instead of passing in the standard `shortUrlSuffix`.

- **Including target URL in analytics:** We could also track the count analytics by `targetUrl` so we can see how many clicks went to the destination in total. This would be useful if we’re running more than one campaign to the same page.

- **Hard to guess short URLs:** Use the `WordNonceGeneration` strategy but increase the number of characters generated in a single nonce. No changes are really needed besides longer nonces.

- **Reporting analytics to non-registered users:** This feature would be undesirable. It has the ability to create privacy issues, as it would essentially make our analytics delivering system public and not private.
