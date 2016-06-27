IIA Create endpoint
===================

* [What is the status of this document?][statuses]
* [See the index of all other EWP Specifications][develhub]


Summary
-------

This endpoint allows clients to create new IIAs to be hosted on this provider.


Request method
--------------

 * Requests MUST be made with HTTP POST method. Servers SHOULD reject all other
   request methods.


Request parameters
------------------

Parameters MUST be in the `application/x-www-form-urlencoded` format (for POST
requests).


### `partner_hei_id` (required, repeatable)

A list of HEI IDs. **At least one** of these HEIs MUST be covered by EWP Host
who is making *this* request (servers are REQUIRED to validate that).

This list will be used by the server to create the first, mostly empty, version
of the IIA. The client MUST re-fetch this draft version via the [get endpoint]
(get.md) before attempting to insert the actual content (via the [update
endpoint](update.md)).

Note, that this design encourages you to create a new draft IIA even before the
end-user is allowed to put any reasonable contents inside. Participating
partners will be notified about this new draft though. So, it's quite important
(from the user experience perspective) for the clients to notify their users
that such drafts have been created or edited recently. WRTODO: Move this
paragraph to the get-response.xsd file, once "last-modified" element is placed
there.


Permissions
-----------

 * All EWP Host who cover at least a single HEI MUST be allowed access to this
   endpoint.


Handling of invalid parameters
------------------------------

 * General [error handling rules][error-handling] apply.

 * Unknown `partner_hei_id` values MUST be allowed. (IIAs are allowed to be
   related to partner HEIs which are not yet connected to the EWP Network).

 * If the requesting EWP Host doesn't cover any of the `partner_hei_id`
   parameters, the server MUST respond with HTTP 400 error.


Response
--------

Servers MUST respond with a valid XML document described by the
[create-response.xsd](create-response.xsd) schema. See the schema annotations
for further information.


[develhub]: http://developers.erasmuswithoutpaper.eu/
[statuses]: https://github.com/erasmus-without-paper/ewp-specs-management#statuses
[registry-spec]: https://github.com/erasmus-without-paper/ewp-specs-api-registry
[discovery-api]: https://github.com/erasmus-without-paper/ewp-specs-api-discovery
[echo]: https://github.com/erasmus-without-paper/ewp-specs-api-echo
[error-handling]: https://github.com/erasmus-without-paper/ewp-specs-architecture#error-handling
[institutions-api]: https://github.com/erasmus-without-paper/ewp-specs-api-institutions
