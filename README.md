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

***

use `scope`s only for getting ` ActiveRecord::Relation`, not for single record

```
# bad

scope :last_active_user, -> { where(active: true).last }
```

```
# good

scope :active, -> { where(active: true) }

def self.last_active_user
  active.last
end
```

## SERVICES

## TESTS

[Better Specs](http://betterspecs.org/)

***

use [FactoryGirl](https://github.com/thoughtbot/factory_girl) for build instances

***

if you use stable value when you define attributes in FactoryGirl don't use `{}`

```
# bad

FactoryGirl.define do
  factory :article do
    published { true }
  end
end
```

```
# good

FactoryGirl.define do
  factory :article do
    published true
  end
end
```

***

use [Faker](https://github.com/stympy/faker) for generate rundom attributes

***

use [DatabaseCleaner](https://github.com/DatabaseCleaner/database_cleaner) for clean database around tests

***

write test in single string (isolate test)

```
# bad

it 'some description' do
  expect(...).to .....
end
```

```
# good

it { expect(...).to ... }
```

test should be clear and understandable without description

***

don't use instance variables, instead use `let` or `let!`

***

if you test function with time use [TimeCop](https://github.com/travisjeffery/timecop) for stubing time


```
# bad
before do
  @user = User.create(first_name: 'John', age: 30)
end

```

```
# good
let(:user) { FactoryGirl.create(:user) }
```

***

when you write tests use same order as methods defined

***

skip callbacks in `rails_helper.rb`

```
RSpec.configure do |config|
  config.before(:suite) do
    User.skip_callback(:create, :before, :build_profile)
  end
end
```

if you need this callback in test just set it in `before` block and skip it in `after` block

***

don't save object to database if it hasn't any relations through database

```
# bad

let(:user) { create(:user) }

it { expect(user.full_name).to eq(user.first_name + ' ' + user.last_name) }
```

```
# good

let(:user) { build(:user) }

it { expect(user.full_name).to eq(user.first_name + ' ' + user.last_name) }
```

if you can, use `double`

```
let(:service) { double('service') }

allow(MyService).to receive(:new) { service }
```

***
