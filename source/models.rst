Models
------

``{"type": "cosmic.Model"}`` references a serializer whose job is to serialize
and deserialize API models. When serialized, a model consists of a name and a
schema. Here is the JSON schema for Model::

    Struct {
        required name    :: String
        optional schema  :: Schema
    }

When you deserialize an API model, an object will be created (a class in most
object-oriented languages) that will mimic the original model specified in the
API author's code. Like the original, it should provide method to serialize
and normalize the model data. Of course, custom validation cannot be performed
because its code cannot be passed over the wire as easily as the schema.

