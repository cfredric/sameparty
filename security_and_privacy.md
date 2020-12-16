# `SameParty` Security & Privacy Self-review

## What information might this feature expose to Web sites or other parties, and for what purposes is that exposure necessary?

This feature relies on [First-Party Sets](https://github.com/privacycg/first-party-sets) to define a new privacy boundary for cookies. Traditionally, the term "third-party cookies" has been used to denote cookies that are sent in **cross-registrable domain** contexts ([recent updates](https://tools.ietf.org/html/draft-west-cookie-incrementalism-01) to cookie semantics modify this to **cross-site** contexts, where site is a tuple of the URL scheme and registrable domain). This change seeks to re-define the term "third-party cookies" as cookies sent in contexts that do not belong to the same First-Party Set. It is necessary because modern websites are often distributed over multiple registrable domains (e.g. for security or deployment reasons), so using the domain as the privacy boundary is not practical. Note that it is important that the user be able to understand/discover the relationship between the registrable domains, which is within purview of the First-Party Sets proposal.

Relative to the traditional definition of "third-party cookies', the `SameParty` cookie attribute seeks to widen the privacy boundary from a single site/domain, to a collection of sites (subject to an acceptance policy).

When no restrictions are in place against third-party cookies, the `SameParty` cookie attribute is an improvement in privacy capabilities, allowing a developer to restrict access to cookies within contexts across a small set of domains.

In all cases, the information that may be shared between sites is any information that may be stored in a cookie, including user identifiers.

## Is this specification exposing the minimum amount of information necessary to power the feature?

Yes. The cross-site, same-party information exposed by this feature is the express purpose of this specification.

## How does this specification deal with personal information or personally-identifiable information or information derived thereof?

This specification does not deal with PI or PII or information derived thereof, since it does not change the sources of such information available in the browser. This specification is merely a change of how such information may be propagated between sites. See the following discussion of sensitive information for a discussion of this information propagation.

## How does this specification deal with sensitive information?

This specification does not introduce new types of sensitive information, and only modifies an existing information-sharing avenue. However, the `SameParty` attribute is not intended as a security boundary, but as a privacy boundary. Authors should still follow best practices to prevent attacks such as CSRF.

The `SameParty` attribute requires the `Secure` attribute, to prevent attackers from reading sensitive cookies by snooping on HTTP traffic.

`SameParty` cookies will not be sent in cross-party contexts. I.e., `SameParty` cookies will only be sent if all ancestor frames in the frame hierarchy are members of a given First-Party Set, and the target URL is also on a site that belongs to that set. Note that top-level navigations are always considered same-party.

## Does this specification introduce new state for an origin that persists across browsing sessions?

This does not introduce new state, since it is a modification of an existing state mechanism.

## What information from the underlying platform, e.g. configuration data, is exposed by this specification to an origin?

N/A.

## Does this specification allow an origin access to sensors on a user’s device

No.

## What data does this specification expose to an origin? Please also document what data is identical to data exposed by other features, in the same or different contexts.

This specification establishes an opt-in mechanism for a site to access its cookies in a first-party context on a different site. Both origins must be in the same First-Party Set; the cookie must be annotated with the `SameParty` and `Secure` attributes, and not have the `SameSite=Strict` attribute.

## Does this specification enable new script execution/loading mechanisms?

No.

## Does this specification allow an origin to access other devices?

No.

## Does this specification allow an origin some measure of control over a user agent’s native UI?

No.

## What temporary identifiers might this specification create or expose to the web?

None.

## How does this specification distinguish between behavior in first-party and third-party contexts?

This specification makes no distinction between behavior in first-party and third-party contexts, where "first-party" refers to URLs on the same registrable domain, and "third-party" refers to members of different registrable domains. This specification does make a distinction between behavior in same-party and cross-party contexts, where "same-party" refers to a set of URLs on sites which all belong to a particular given First-Party Set, and "cross-party" refers to a set of URLs on sites which do not all belong to the same First-Party Set (and may not belong to a First-Party Set at all).

Today's browsers use the `SameSite` cookie attribute to dictate different behavior in same-site and cross-site contexts. Since the `SameParty` attribute changes the boundary from one of sites to one of parties, use of the `SameParty` attribute has some interactions with use of the `SameSite` attribute.

Cookies set with the `SameParty` attribute as well as `SameSite=Strict` are considered invalid, since `SameSite=Strict` indicates that the author intends the cookie to be sent only on same-site requests (including for top-level navigations, i.e. not send the cookie on cross-site top-level navigations), but `SameParty` semantics would negate that by widening it to same-party requests (where top-level navigations are always same-party).

(Note that in a browser that recognizes the `SameParty` attribute, the `SameSite` attribute is ignored unless it is `SameSite=Strict`, in which case the cookie is invalid. That is, `SameParty` always takes precedence over `SameSite` for valid cookies in browsers that implement `SameParty`. So, the only reason to specify both `SameParty` and `SameSite` is to provide a fallback behavior in browsers which do not implement `SameParty`. In such situations, allowing a fallback of `SameSite=None` makes sense, since that behavior is less restrictive than `SameParty`. Similarly, fallback behavior of `SameSite=Lax` is reasonable, since both `SameSite=Lax` and `SameParty` allow cookies to be sent in cross-site navigation requests, and block cookies in cross-party contexts.)

## How does this specification work in the context of a user agent’s Private Browsing or "incognito" mode?

This specification changes the behavior of cookies in private browsing mode in the same way that it changes cookie behavior in normal mode. That is, the user agent may choose to treat `SameParty` cookies as "first-party" and not block them where third-party cookie blocking is enabled in private browsing mode, but should continue to use a separate cookie jar than when the browser is in normal mode.

## Does this specification have a "Security Considerations" and "Privacy Considerations" section?

N/A

## Does this specification allow downgrading default security characteristics?

No. This feature does allow downgrading default security characteristics ([of `SameSite`](https://tools.ietf.org/html/draft-west-cookie-incrementalism-01#section-3.1)), but requires opt-in from the involved entities, and requires security measures (e.g. use of the `Secure` attribute).

## What should this questionnaire have asked?

N/A
