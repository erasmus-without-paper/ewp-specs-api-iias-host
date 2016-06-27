IIA Search endpoint
===================

* [What is the status of this document?][statuses]
* [See the index of all other EWP Specifications][develhub]


Summary
-------

This endpoint allows clients to see the list of all agreements (IIAs) stored in
this IIA Host, and search though them, with help of some simple filters.


Request method
--------------

 * Requests MUST be made with either HTTP GET or HTTP POST method. Servers MUST
   support both these methods. Servers SHOULD reject all other request methods.

 * Clients are advised to use POST when passing large number of parameters
   (servers MAY set a limit on their GET query string length).


Request parameters
------------------

Parameters MUST be provided either in a query string (for GET requests), or in
the `application/x-www-form-urlencoded` format (for POST requests).


### `partner_hei_matching_mode` (optional)

This parameters controls how multiple `partner_hei_id` parameters will be
interpreted. It has two possible values (**case-sensitive**):

 * `off` (default) - in this mode, `partner_hei_id` parameters SHOULD NOT be
   allowed. The server SHOULD respond with a HTTP 400 error if `partner_hei_id`
   parameter was given, and remind the requester to set proper
   `partner_hei_matching_mode`.

 * `and` - in this mode, the results returned MUST contain only these IIAs
   for which **all** of the HEIs passed via the `partner_hei_id` parameters are
   the partners of (but necessarily only these).

   For example, if the server hosts two IIAs:

    1. Between HEIs A, B and C.
    2. Between HEIs B, C and D.

   Then:

    * When called with `hei_id=B&hei_id=C`, both IIAs will be returned.
    * When called with `hei_id=C&hei_id=D`, only the second IIA will be
      returned.

 * `or` - in this mode, the results return MUST contain only these IIAs for
   which **at least one** of its partners matches **at least one** of the
   HEIs passed via the `partner_hei_id` parameters.

   If applied for the same example as above:

    * When called with `hei_id=B&hei_id=C`, both IIAs will be returned.
    * When called with `hei_id=C&hei_id=D`, **still both** IIAs will be
      returned.


### `partner_hei_id` (repeatable, optional)

A list of institution identifiers. Allowed only in conjunction with
`partner_hei_matching_mode` (see description above for details).

This parameter is *repeatable*, so the request MAY contain multiple occurrences
of it. The server is REQUIRED to process all of them.


Permissions
-----------

All requests from the EWP Network MUST be allowed access to this API. Consult
the [Echo API][echo] specs for details on handling unprivileged requests.


Handling of invalid parameters
------------------------------

 * General [error handling rules][error-handling] apply.

 * If invalid (or unknown) `partner_hei_id` values are passed, then server MUST
   ignore such values.


Response
--------

Servers MUST respond with a valid XML document described by the
[search-response.xsd](search-response.xsd) schema. See the schema annotations
for further information.


[develhub]: http://developers.erasmuswithoutpaper.eu/
[statuses]: https://github.com/erasmus-without-paper/ewp-specs-management#statuses
[registry-spec]: https://github.com/erasmus-without-paper/ewp-specs-api-registry
[discovery-api]: https://github.com/erasmus-without-paper/ewp-specs-api-discovery
[echo]: https://github.com/erasmus-without-paper/ewp-specs-api-echo
[error-handling]: https://github.com/erasmus-without-paper/ewp-specs-architecture#error-handling
[institutions-api]: https://github.com/erasmus-without-paper/ewp-specs-api-institutions
