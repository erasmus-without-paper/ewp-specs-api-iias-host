IIA Update endpoint
===================

* [What is the status of this document?][statuses]
* [See the index of all other EWP Specifications][develhub]


Summary
-------

This endpoint allows clients to update IIAs hosted by this provider. (It allows
to add new entries to its history.)


Request method
--------------

 * Requests MUST be made with HTTP POST method. Servers SHOULD reject all other
   request methods.


Request parameters
------------------

Parameters MUST be in the `application/x-www-form-urlencoded` format (for POST
requests).


### `iia_id` (required)

ID of IIA the client wants to modify.

Note, that `iia_id` identifiers are unique within the entire EWP Network.


### `sync_verifier` (required)

Integer. The current length of the IIA's history, i.e. the number of child
elements of the `<timeline>` element. "Current" means "before this new change
is made".

This parameter is required in order to prevent [edit conflicts]
(https://en.wikipedia.org/wiki/Edit_conflict). Before proceeding with the
update, the server is REQUIRED to verify if the requester is in possession of
the current version of the IIA, and it does so by comparing `sync_verifier`
with the actual timeline length.


### `entries_to_append` (required)

This parameter describes XML elements to be appended to the IIA's `<timeline>`.
The value of this parameter MUST be a valid XML document described by the
[update-entries.xsd](update-entries.xsd) schema. See the schema annotations for
further information.


Permissions
-----------

 * If the calling EWP Host covers at least one HEI being a partner of this IIA,
   then he MUST be allowed access.

 * No other callers should be allowed access to IIA.


Handling of invalid parameters
------------------------------

 * General [error handling rules][error-handling] apply.

 * Invalid or unknown `iia_id` values MUST result in a HTTP 400 error response.

 * If object identified by `iia_id` exists, but the requester doesn't have
   the permission to modify it, then server MUST respond with a HTTP 403 error
   response.

 * Invalid `entries_to_append` values (e.g. when the value does not conform to
   the proper XML Schema) MUST result in a HTTP 400 error response.

 * Invalid (out-of-date) values of `sync_verifier` MUST result in HTTP 409
   error response. Clients which receive this error should refresh their stale
   IIAs and resolve all conflicts before resubmitting their requests.


Response
--------

Servers MUST respond with a valid XML document described by the
[update-response.xsd](update-response.xsd) schema. See the schema annotations
for further information.


[develhub]: http://developers.erasmuswithoutpaper.eu/
[statuses]: https://github.com/erasmus-without-paper/ewp-specs-management#statuses
[registry-spec]: https://github.com/erasmus-without-paper/ewp-specs-api-registry
[discovery-api]: https://github.com/erasmus-without-paper/ewp-specs-api-discovery
[echo]: https://github.com/erasmus-without-paper/ewp-specs-api-echo
[error-handling]: https://github.com/erasmus-without-paper/ewp-specs-architecture#error-handling
[institutions-api]: https://github.com/erasmus-without-paper/ewp-specs-api-institutions
