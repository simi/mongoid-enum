# Mongoid::Enum

Heavily inspired by [DHH's
ActiveRecord::Enum](https://github.com/rails/rails/commit/db41eb8a6ea88b854bf5cd11070ea4245e1639c5), this little library is
just there to help you cut down the cruft in your models and make the
world a happier place at the same time.

A single line will get you fields, accessors, validations and scopes,
and a few other bits-and-bobs.


# Installation

Add this to your Gemfile:

    gem "mongoid-enum"

And then run `bundle install`.


# Usage

````
class Payment
  include Mongoid::Document
  include Mongoid::Enum

  enum :status, [:pending, :approved, :declined], 
end
````

Aaaaaaand then you get things like:

````
payment = Payment.create

payment.status
# => :pending

payment.approved!
# => :approved

payment.pending?
# => :false

Payment.approved
# => Mongoid::Criteria for payments with status == :approved

````

# Features

## Field

Your enum value is stored as either a Symbol, or an Array (when storing
multiple values). The actual field name has a leading underscore (e.g.:
`_status`), but is also aliased with its actual name for you
convenience.


## Accessors

Your enums will get getters-and-setters with the same name. So using the
'Payment' example above:

````
payment.status = :declined
payment.status
# => :declined
````

And you also get bang(!) and query(?) methods for each of the values in
your enum (see [this example](#Usage).


## Constants

For each enum, you'll also get a constant named after it. This is to
help you elsewhere in your app, should you need to display, or leverage
the list of values. Using the above example:

````
Payment::STATUS
# => [:pending, :approved, :declined]
````


## Validations

Enum values are automatically validated against the list. You can
disable this behaviour (see (below)[#Options]).


## Scopes

A scope added for each of your enum's values. Using the example above,
you'd automatically get:

````
Payment.pending # => Mongoid::Criteria
Payment.approved # => Mongoid::Criteria
Payment.declined # => Mongoid::Criteria
````


# Options

## Default value

If not specified, the default will be the first in your list of values
(`:pending` in the example above). You can override this with the
`:default` option:

    enum :roles, [:manager, :administrator], :default => ""


## Multiple values

Sometimes you'll need to store multiple values from your list, this
couldn't be easier:

    enum, :roles => [:basic, :manager, :administrator], :multiple => true

    user = User.create
    user.roles << :basic
    user.roles << :manager
    user.save!

    user.manager? # => true
    user.administrator? # => false
    user.roles # => [:basic, :manager]


## Validations

Validations are baked in by default, and ensure that the value(s) set in
your field are always from your list of options. If you need more
complex validations, or you just want to throw caution to the wind, you
can turn them off:

    enum :status => [:up, :down], :validation => false
