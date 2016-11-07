# Rails Best Practices

* [Models](#models)
* Controllers
* Decorators
* [Services](#services)
* [Tests](#tests)

## MODELS

define single model's structure for whole team and add it when you create new model. Each member of team have to wright code according to this structure, for example

```
class User < SuperClass
  # CONSTANTS

  # ENUMs

  # CALLBACKS

  # foreign includes, like has_secure_password, uploaders etc

  # VALIDATORS

  # ASSOCIATIONS

  ## belongs_to
  ## has_one
  ## has_many
  ## has_and_belongs_to_many

  # SCOPES

  # CLASS METHODSDS

  # INSTANCE METHODS
end
```

## SERVICES

## TESTS
