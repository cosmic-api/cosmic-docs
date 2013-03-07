Model System
============

Cosmic ships with a simple JSON-based schema and model system. A JSON schema
is a way of describing JSON data for validation and generating documentation.
A model is a type definition that may have a schema of its own as well as
custom validation functionality. Once a model is created, any JSON schema can
reference it by its name.

JSON Schema Basics
------------------

A *schema* is a recursive JSON structure that mirrors the structure of the
data it is meant to validate.

.. note::

    *Why invent our own JSON schema system?*
    
    Before deciding to go with our own system, we took a good look at some
    existing options. Our best candidates were `JSON Schema <http://json-
    schema.org/>`_ and `Apache Avro <http://avro.apache.org/>`_. JSON Schema
    has a significant limitation: the order of object attributes is not
    preserved. Apache Avro had a different problem: because an attribute can
    be defined as allowing multiple types, objects needed to be wrapped in an
    annotation layer to avoid ambiguity. Instead of ``{"name": "Jenn"}`` we
    would have to output ``{"Person": {"name": "Jenn"}}``. In the context of
    REST APIs, this is uncommon and potentially confusing.

    Because Cosmic must be extremely portable, it is essential that we keep
    the feature list to a minimum. In this instance, the minimum is generating
    documentation and basic validation of data structure and types. Instead of
    making you learn a new `DSL <http://en.wikipedia.org/wiki/Domain-
    specific_language>`_ for obscure validation, we encourage you to use the
    power of your language. The benefits of describing schemas in minute
    detail are greatly outweighed by the costs of growing the amount of code
    that needs to be ported.

A schema is always a JSON object. It must always contain the *type* attribute.
Here is a list of types and their JSON representation:

+-------------------------+---------------------+
|         Schema          |  JSON type          |
+=========================+=====================+
| ``{"type": "integer"}`` | ``number``          |
+-------------------------+---------------------+
| ``{"type": "float"}``   | ``number``          |
+-------------------------+---------------------+
| ``{"type": "string"}``  | ``string``          |
+-------------------------+---------------------+
| ``{"type": "boolean"}`` | ``boolean``         |
+-------------------------+---------------------+
| ``{"type": "binary"}``  | ``string`` (base64) |
+-------------------------+---------------------+
| ``{"type": "array"}``   | ``array``           |
+-------------------------+---------------------+
| ``{"type": "object"}``  | ``object``          |
+-------------------------+---------------------+
| ``{"type": "json"}``    | N/A                 |
+-------------------------+---------------------+
| ``{"type": "schema"}``  | N/A                 |
+-------------------------+---------------------+

An object schema must always contain a *properties* attribute, which will be
an array of property objects:

.. code:: json

    {
        "type": "object",
        "properties": [
            {
                "name": "id",
                "schema": {"type": "integer"},
                "required": true
            },
            {
                "name": "title",
                "schema": {"type": "string"},
                "required": false
            }
        ]
    }

An array schema must always contain an *items* property, which must be a
schema that describes every item in the array. Here is a schema describing an
array or strings:

.. code:: json

    {
        "type": "array",
        "items": {"type": "string"}
    }

Of course, these schemas can be nested as deep as you like. For example, to
validate ``[{"name": "Rose"}, {"name": "Lily"}]``, you could use the following
schema:

.. code:: json

    {
        "type": "array",
        "items": {
            "type": "object",
            "properties": [
                {
                    "name": "name",
                    "schema": {"type": "string"},
                    "required": true
                }
            ]
        }
    }

Models
------

A *model* is a data type definition that consists of a schema and custom
validation code. A model must be able to *normalize* and *serialize* data. A
model's normalize procedure takes data as presented by the JSON parser and
either returns the *normalized data* or throws a validation error. The 
serialize procedure does the opposite, but without validation.

For primitive schema types, the normalized data will usually be a language
primitive: if you normalize an integer against ``{"type": "integer"}`` you
will get an integer in return. Sometimes, the normalization procedure may cast
one primitive to another. For example, the model responsible for ``{"type":
"float"}`` will cast an integer into a float.

In object-oriented languages, a model is best represented by a class. For
simple types, this class is merely a namespace holding the corresponding
normalization and serialization functions. For most user-defined models, the
class has a bigger purpose: it will be instantiated at the end of the model's
normalization procedure and the instance will be returned as the normalized
data.

Cosmic will normalize all incoming data and serialize all outgoing data for
you. This means that your function can always operate on rich native data,
leaving JSON in the model system, where it belongs.

If you define a model as part of an API, it will become accessible via
``{"type": "<api>.<model>"}``.

Raw JSON Data
~~~~~~~~~~~~~

A few words need to be said about ``{"type": "json"}``. This type represents
arbitrary JSON data. No validation is performed. You may want to use this type
as a wildcard when you don't know in advance what the data will look like, or
if you expect a separate system to deal with it.

Do not use it as a way of allowing multiple types for a property. Each
property should have just one type.

Schema Models
~~~~~~~~~~~~~

Schemas, the objects that normalize and serialize data, need to be normalized
and serialized themselves. In order to enable this, they are implemented as
models, validated against ``{"type": "schema"}``.

When you normalize ``{"type": "integer"}`` against ``{"type": "schema"}``,
the result will be an integer model that you can then use to normalize actual
integers. Of course ``{"type": "integr"}`` will result in a validation error.

All schemas except for ``object`` and ``array`` are represented by an object
with a single attribute *type*. To validate such a schema, the model uses the
following meta-schema:

.. code:: json

    {
        "type": "object",
        "properties": [
            {
                "name": "type",
                "required": true,
                "schema": {"type": "string"}
            }
        ]
    }

An array schema needs more than just *type*. It also needs *items*:

.. code:: json

    {
        "type": "object",
        "properties": [
            {
                "name": "type",
                "required": true,
                "schema": {"type": "string"}
            },
            {
                "name": "items",
                "required": true,
                "schema": {"type": "schema"}
            }
        ]
    }

An ``object`` schema requires *properties* (note that it also checks to make
sure there are no duplicate properties):

.. code:: json

    {
        "type": "object",
        "properties": [
            {
                "name": "type",
                "required": true,
                "schema": {"type": "string"}
            },
            {
                "name": "properties",
                "required": true,
                "schema": {
                    "type": "array",
                    "items": {
                        "type": "object",
                        "properties": [
                            {
                                "name": "name",
                                "required": true,
                                "schema": {"type": "string"}
                            },
                            {
                                "name": "required",
                                "required": true,
                                "schema": {"type": "boolean"}
                            },
                            {
                                "name": "schema",
                                "required": true,
                                "schema": {"type": "schema"}
                            }
                        ]
                    }
                }
            }
        ]
    }

As you can see, the ``schema`` type is quite handy. Not only is it used by the
model system internally but also by other modules in Cosmic. It allows such
things as actions to be implemented as simple models.

A Word About Null
-----------------

The only place where ``null`` is allowed within our JSON schema system is in a
``json`` model. Trying to pass a ``null`` as the value of a property, even if
it is optional, will result in a validation error. Such a property should
instead be omitted from the payload.

The reason for this is to avoid ambiguity between ``null`` as an explicit
value and the absense of value. In JavaScript, these are represented by
``null`` and ``undefined`` respectively.

