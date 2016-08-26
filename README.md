OBSOLETE - Interinstitutional Agreements Host API
=================================================

* [What is the status of this document?][statuses]
* [See the index of all other EWP Specifications][develhub]


Summary
-------

**OBSOLETE: This proposal was replaced by
[IIAs API](https://github.com/erasmus-without-paper/ewp-specs-api-iias).**

This document describes the **Interinstitutional Agreements (IIAs) Host API**.
It allows the clients to read, create and update IIAs stored on the EWP Host
which has implemented this API.

**WRTODO:** Once this new distributed approach is decided upon, this document
will replace [Interinstitutional Agreements API]
(https://github.com/erasmus-without-paper/ewp-specs-api-iias)
and [Interinstitutional Agreement Search API]
(https://github.com/erasmus-without-paper/ewp-specs-api-iia-search). All
references regarding the central IIA server in other documents will also need
to be updated.


Choose your strategy
--------------------

As it has been decided
[here](https://github.com/erasmus-without-paper/general-issues/issues/12) - EWP
API designers were required to allow both **distributed** and **centralized**
IIA hosting in the EWP Network.

The centralized solution is much simpler (and we do recommend choosing this
one, at least at a start). However, regardless of what you choose, you MUST
have some insight into the complexities of the decentralized architecture too.


### The basics

In order for any IIA to be hosted in a distributed environment, a single
dedicated server must be selected which will become the IIA's "master" (aka
"IIA's host").

*Side note:* [Master-slave concept][master-slave-wiki] used in IIAs is a bit
similar to the master-slave concept used in the [Outgoing Mobilities
API](mobilities-api), however, in the latter one, the master is always clearly
defined (*sending HEI* will always be the master), whereas **in case of IIAs,
partners may freely choose where *each* of their IIAs will be hosted**.

It's worth noting that IIAs need to be in place before you will be able to use
many other EWP APIs. This means that **you will need to pick one of the
following strategies** before you will be able to implement EWP
mobility-related APIs.


### Distributed strategy

You are allowed to host your own IIAs on your servers. However, there are some
complex properties of this architecture you must understand first:

 * The more IIA providers there are, the more costly it will be to find IIAs
   in the network. When a new IIA is created, its master is REQUIRED to attempt
   to notify all related partners of its creation (via [IIA CNR API]
   [iia-cnr-api]). However, such notifications MAY get lost. In order to
   counteract this, all EWP Hosts will need to periodically scan the entire EWP
   network in search of IIAs related to institutions they cover.

 * Once an EWP Host X implements the *IIAs Host API*, it becomes capable of
   storing IIAs (or, in other words, it becomes an IIA hosting provider). This
   enables HEIs covered by X to be masters of their own IIAs.

   **But there's a twist:** X is also REQUIRED to allow **other partners**
   (other EWP Hosts) to create and edit IIAs **on X's servers**. Usually, such
   externally created IIAs will end up to be bound with at least one of the
   HEIs covered by X, but this is **not** strictly required (so, X may end up
   hosting IIAs it doesn't "want" to host).

 * Partners may choose the provider which will be the master of each individual
   IIA. It is advised to choose either the central provider (explained in the
   next chapter), or a provider related to one of the HEIs which will be the
   future partners of this IIA, but this recommendation is not enforced (and -
   due to the changing nature of the IIAs themselves - it cannot be enforced).

   It is also RECOMMENDED that the choice is made automatically, without direct
   user input on the topic. Since everyone can edit and sign his IIAs in the
   network (regardless of who's the master of this IIA), therefore there seems
   to be no need to expose this information (about who's the master) to the
   end-users. Users SHOULD NOT be bothered with the complexity of distributed
   systems - that's the task for us, developers.

 * Once you implement this API, it becomes quite difficult to "undo" this
   commitment, so we recommend caution. You cannot simply "resign" and drop
   your IIAs Host API later on, as this would cause other HEIs to loose the
   ability to access and edit **their** IIAs **you've** been the master of. You
   would need to ask some other provider to become the new master for your IIAs
   first, then migrate your data and propagate information about the changed
   master throughout the network (which would require you to keep serving your
   API at least for some time).

 * Note, that you cannot *always* be the master of your IIAs. Sometimes, you
   will need to allow your partner to choose the master. It shouldn't really
   matter to you who is the master, as long as you trust the master to keep
   hosting the service indefinitely.

 * Every IIA will contain a reference to its provider. In order to facilitate
   this, every IIA Provider will need to have an unique ID (see
   [manifest-entry.xsd](manifest-entry.xsd)) - preferably based on your domain
   name (and HEI IDs are). Once selected, this ID cannot be changed in the
   future. It is however allowed to change the location (URLs) of your service
   since clients attempting to fetch IIAs from you will retrieve proper URLs
   via the Registry Service, based on your unique ID.


### Centralized strategy

You are not required to implement neither the *service provider* nor the
*client editor* for this API. You can use *EWP's Agreements Website* instead:

 * It implements the same IIAs Host API which is described in this document.
   This means that if you choose to design your own IIA editor client, then you
   can remotely edit IIAs stored on the central server.

 * Additionally, it also provides a web user interface for editing IIAs.

   It's important to note, that this feature is **not** limited to the IIAs
   hosted by itself - this web interface allows to edit IIAs hosted on **any
   IIA provider**.

   You will need to **opt-in** in order for your IRO staff members to be able
   to access *EWP's Agreements Website*. You can do that by setting an
   appropriate flag in your Institutions API (WRTODO: info). Once you do
   opt-in, your IRO staff members will receive email messages (to addresses you
   provide in your Institutions API) with account activation links and they
   will be allowed to sign in, edit, and approve their IIAs there.

 * You are still allowed to implement your own, distributed IIA provider in the
   future.

WRTODO: Decide on the name - *IIA Central Repository Service*, or simply *EWP's
Agreements Website*? Then, find all occurrences of the term and replace them
properly.

WRTODO: a link to agreements.erasmuswithoutpaper.eu.


### Provider, client, or both?

You are allowed to choose any combination:

1. You may implement **just the client**, and use it to create and edit IIAs
   hosted on external providers (i.e. the central provider). If you already
   have a mature system for your IRO staff members, then implementing your own
   IIA client allows your users to use familiar user interface (instead of
   having to use the UI provided by *EWP's Agreements Website*).

2. You may implement **just the provider**, and use the web-client provided by
   *EWP's Agreements Website* (that would seem to be a little uncommon setup
   though).

3. You may implement **both the provider and the client**.

4. You can also implement **none of them**, and stick with using *EWP's
   Agreements Website* for both serving and editing your IIAs.

It's easiest to start the development with option 4, implement some other EWP
APIs based on that, and then - perhaps - migrate towards option 1, or option 3.


Endpoints to be implemented
---------------------------

Server implementers MUST:

 * Implement the [`get` endpoint](endpoints/get.md).
 * Implement the [`search` endpoint](endpoints/search.md).
 * Implement the [`create` endpoint](endpoints/create.md).
 * Implement the [`update` endpoint](endpoints/update.md).
 * Put the URLs of these endpoints in your [manifest file][discovery-api], as
   described in [manifest-entry.xsd](manifest-entry.xsd).

The details on each of these endpoints are described on separate pages of this
API specification.


[develhub]: http://developers.erasmuswithoutpaper.eu/
[statuses]: https://github.com/erasmus-without-paper/ewp-specs-management#statuses
[registry-spec]: https://github.com/erasmus-without-paper/ewp-specs-api-registry
[discovery-api]: https://github.com/erasmus-without-paper/ewp-specs-api-discovery
[echo]: https://github.com/erasmus-without-paper/ewp-specs-api-echo
[error-handling]: https://github.com/erasmus-without-paper/ewp-specs-architecture#error-handling
[institutions-api]: https://github.com/erasmus-without-paper/ewp-specs-api-institutions
[mobilities-api]: https://github.com/erasmus-without-paper/ewp-specs-api-mobilities
[master-slave-wiki]: https://en.wikipedia.org/wiki/Master/slave_(technology)
[iia-cnr-api]: https://github.com/erasmus-without-paper/ewp-specs-api-iia-cnr
