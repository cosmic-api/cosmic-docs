APIs
====

Because APIs need to be passed over the wire, they are implemented as a model,
referenced as ``cosmic.API``.

API spec
  A piece of data that describes an API. The JSON form of the API model.

Every API built with Cosmic provides a ``/spec.json`` endpoint. When accessed
by a GET request, it will return the spec of the API. This URL is all the
information that Cosmic needs to generate documentation and build native API
clients.

Here is the JSON schema of the API model:

.. code:: json

    {
        "type": "object",
        "properties": [
            {
                "name": "name",
                "schema": {"type": "string"},
                "required": true
            },
            {
                "name": "homepage",
                "schema": {"type": "string"},
                "required": false
            },
            {
                "name": "actions",
                "required": true,
                "schema": {
                    "type": "array",
                    "items": {"type": "cosmic.Action"}
                }
            },
            {
                "name": "models",
                "required": true,
                "schema": {
                    "type": "array",
                    "items": {"type": "cosmic.APIModel"}
                }
            }
        ]
    }

The API *name* will be used as a namespace for referencing the API's models.
Thus, it should be unique, though Cosmic does not yet provide a way to enforce
that (see :ref:`registry`). Optionally, you can specify a *homepage* as a URL.
Both *actions* and *models* are arrays of objects associated with the API.
For actions, see the :ref:`actions` section.

API Models
----------

``{"type": "cosmic.APIModel"}`` references a special model whose job is to
serialize and normalize API models. When serialized, a model consists of a
name and a schema. Here is the JSON schema for APIModel:

.. code:: json

    {
        "type": "object",
        "properties": [
            {
                "name": "name",
                "required": true,
                "schema": {"type": "string"}
            },
            {
                "name": "schema",
                "required": true,
                "schema": {"type": "schema"}
            }
        ]
    }

When you normalize an API model, an object will be created (a class in most
object-oriented languages) that will mimic the original model specified in the
API author's code. Like the original, it should provide method to serialize
and normalize the model data. Of course, custom validation cannot be performed
because its code cannot be passed over the wire as easily as the schema.

Auto-generated API Clients
--------------------------

Because APIs are implemented as a models, building an API client simply
involves normalizing an API from its JSON form, the API spec.
