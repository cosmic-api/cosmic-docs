Actions
=======

.. warning::
  This page is a work in progress.

Action
  A function provided by a Cosmic API.

Like APIs, *actions* are implemented as a model. Here is the JSON schema of
the action model:

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
                "name": "accepts",
                "schema": {"type": "schema"},
                "required": false
            },
            {
                "name": "returns",
                "schema": {"type": "schema"},
                "required": false
            }
        ]
    }
