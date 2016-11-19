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

##### STI

use STI models only if inherited objects use same fields

if one uses more then one particular column in DB DON'T use STI

it's very usefull at very start, but when application increasing it can make many problems

***

##### Validation

if several attributes have exactly same validation requirements write them in single string

    validates :first_name, :last_name, presence: true

if attributes have different requirements create them different descriptions

    validates :first_name, presence: true, length: { minimum: 6 }
    validates :first_name, presence: true

if you need custom validation and this validation will be used only for one model write it
in private method

    validate :work_place

    ......

    private

    def work_place
      ...
      errors.add(:field_name, 'Custom error message')
      ...
    end

if your custom validator will be used for fiels in more then one model create [Custom Validators](http://guides.rubyonrails.org/active_record_validations.html#custom-validators)

    path: 'app/validators/'

    class EmailValidator < ActiveModel::EachValidator
      def validate_each(record, attribute, value)
        unless value =~ /\A.+@.+\..+\z/i
          record.errors[attribute] << (options[:message] || "is not an email")
        end
      end
    end

    class Person < ApplicationRecord
      validates :email, presence: true, email: true
    end

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
