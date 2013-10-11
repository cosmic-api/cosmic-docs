APIs
====

Because APIs need to be passed over the wire, they are implemented as
serializable types, referenced as ``cosmic.API``.

API spec
  A piece of data that describes an API. The JSON form of the API type.

Every API built with Cosmic provides a ``/spec.json`` endpoint. When accessed
by a GET request, it will return the spec of the API. This URL is all the
information that Cosmic needs to generate documentation and build native API
clients.

Here is the schema of the API type::

    Struct {
        required name :: String
        optional homepage :: String
        required models :: OrderedMap(Struct {
            required schema :: Schema
            required required :: Boolean
            optional doc :: String
        })
        required query_fields :: OrderedMap(Struct {
            required schema :: Schema
            required required :: Boolean
            optional doc :: String
        })
        required actions :: OrderedMap(cosmic.Function)
    }

The API *name* will be used as a namespace for referencing the API's models.
Thus, it should be unique, though Cosmic does not yet provide a way to enforce
that (see :ref:`registry`). Optionally, you can specify a *homepage* as a URL.
Both *actions* and *models* are arrays of objects associated with the API.
For actions, see the :ref:`actions` section.

