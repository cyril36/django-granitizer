=====
Django-Granitizer
=====

Granitizer add flexibility in the GraphL mutation input to define filter input and payload input
To be used aside of graphene-python
Detailed documentation is in the "docs" directory.

Quick start
-----------

1. Add "django-granitizer" to your INSTALLED_APPS setting like this::

    INSTALLED_APPS = [
        ...
        'django-granitizer',
    ]


from django-granitizer.granitizer import Granitizer
class ObjectGranitizer(Granitizer):
    class Meta:
        graphene_class = ObjectNode
        serializer_class = ObjectSerializer
	#if using relay.Node
        relay_id_fields = {
            "type" : BTypeNode,
            "target" : BTargetNode,
            "id" : BusinessNode
        }
        filter_key = {
            "dict": [{"key":"filter"}],
            "uniq":[{"key":"id"}]
        }
        payload_key = {"dict": [{"key":"data"}]}


ObjectType and ObjectInputType 
==============================

- Model        
class Object(models.Model):
    name = models.CharField(max_length=100)
    description = models.TextField()

- Graphene Object Type
class ObjectDataInput(DjangoInputObjectType):
    class Meta:
        model = Object

class ObjectFilter(InputObjectType):
    name = String()

class ObjectInput(InputObjectType):
    id = graphene.ID()
    filter = ObjectFilter()
    data = ObjectDataInput()



Example
=======

Add Query :
mutation add_object($add:AddObjectInput!){
  updateObject(input:$add){
    object{
      id,
      name,
      description,
      }
  }
}
variable:
{
  "add": {
    "object": {
      "data": {
        "name": "my first object"
        "description": "object will be created",
      }
    }
  },
result:
{
  "data": {
    "object": {
      "id": "xxxxx",
      "name": "my first object",
      "description": "object will be created""
     }
  }
}


Update Query with ID:
mutation update_object($update:UpdateObjectInput!){
  updateObject(input:$update){
    object{
      id,
      name,
      description,
      }
  }
}
variable:
{
  "update": {
    "object": {
      "id": "xxxxx",
      "data": {
        "description": "description will be updated filtered by id",
      }
    }
  },
result:
{
  "data": {
    "object": {
      "id": "xxxxx",
      "name": "my first object",
      "description": "description will be updated filtered by id"
     }
  }
}

Update Query with filter (setup in the META data):
mutation update_object($update:UpdateObjectInput!){
  updateObject(input:$update){
    object{
      id,
      name,
      description,
      }
  }
}
variable:
{
  "update": {
    "object": {
      "filter": {
        "name": "my first object"
      },
      "data": {
        "description": "description will be updated with the 'filter' field"
      }
    }
  },
result:
{
  "data": {
    "object": {
      "id": "xxxxx",
      "name": "my first object",
      "description": "description will be updated with the 'filter' field"
     }
  }
}



Delete Query with ID:
mutation delete_object($delete:DeleteObjectInput!){
  deleteObject(input:$delete){
    object{
      id,
      name,
      description,
      }
  }
}
variable:
{
  "update": {
    "object": {
      "filter": {
        "name": "my first object"
      },
    }
  },
result:
{
  "data": {
    "object": {
      "message": "Object deleted"
     }
  }
}

