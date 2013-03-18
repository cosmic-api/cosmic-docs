APIs
====

.. warning::
  This page is a work in progress

Because APIs need to be passed over the wire, they are implemented as a model,
referenced as ``cosmic.API``. Here is the JSON schema of the API model:

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
                "name": "url",
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
