[![Build Status](https://travis-ci.org/lsst-sqre/uservice-ltdstatus.svg?branch=master)](https://travis-ci.org/lsst-sqre/uservice-ltdstatus)

# sqre-uservice-ltdstatus

LSST DM SQuaRE microservice wrapper for determining whether LSST The
Docs products listed at keeper.lsst.codes are responding to their
published endpoints.

## Usage

`sqre-uservice-ltdstatus` will run standalone on port 5000 or under
`uwsgi`.  It responds to the following routes:

### Routes

* `/`: returns `OK` (used by GCE Ingress healthcheck)

* `/ltdstatus`: returns a check of each published edition of each product

* `/ltdstatus/{{product}}`: returns a check for each published edition of
the specified product.

### Returned Structure

The returned structure is a JSON object.  Its keys are the names of
either the requested product, or all LTD products.  If the product name
cannot be found, the URL tried is substituted.  Each top-level product
has two keys, `editions`, and `url`.  `url` is the URL for the published
product if the `main` edition has ever been built, and `null` otherwise.

`editions` is a JSON object where the keys are edition names (or the
attempted URL if the edition name cannot be found).  The value of each
edition is a JSON object with three keys, `url`, `status_code`, and
`url_type`.

`url` is the URL that was attempted to be fetched, as a string.
`status_code` is the status code of the HTTP request.
`url_type` is a string, and is one of `product`, `product_editions`,
`product_edition`, or `product_edition_published_url`.  This represents
what sort of URL was being fetched.

In a normal run, over all products, `url_type` should always be
`product_edition_published_url`, and `status_code` should always be 200.

The HTTP status of the response containing the returned structure will
be the largest status code returned by any of the individual requests.
