.. _actions:

Actions
=======

Action
  A function provided by a Cosmic API.

Actions are functions provided by the API. They are serialized by
``cosmic.Function``, a type with the following schema::

    Struct {
        optional accepts :: Schema
        optional returns :: Schema
        optional doc     :: String
    }

An action name is unique per API. The URL for an action called ``hello`` would
be ``/actions/hello``. This URL will be accessible with a POST request with
Content-Type set to ``application/json``. The body of this request must be
JSON data that will be deserialized against the *accepts* schema specified by
the action. If the data is valid, the action function will run and the
resulting native data will be serialized with the *returns* schema.

Any error, including a validation error, must result in a 4XX or 5XX response
with the body containing a message like so: ``{"error": "Something went wrong"}``.

A locally-created action will contain a native user-specified function. This
function will be executed when the user calls the action. An action that has
been serialized, passed over the wire, then deserialized will be similar to
the original action object, but naturally it will be missing the raw function.
Failing to find the function, the API will instead make an HTTP call
(effectively running this function on a remote machine) and return the result
in its native form.

It is the goal of Cosmic to make local and remote actions act as similar as
possible. For instance, when the HTTP request comes back with an error, a
native exception should be thrown by the implementation. Currently, the Python
implementation throws a generic exception in such cases. In the future we plan
to serialize and normalize such exceptions the same way we do all other
objects.
