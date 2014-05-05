# Interchange

`Interchange` is a way to define boundary objects. The class
defines the all required methods, then delegates the work to an
implementation. Implementations are be registered and used. A null
implementation is automatically generated and set as the
default implementation. This is useful in a few use cases:

* SOA setups where clients must be isolated from services (EX:
  different implementations in dev/test/production)
* Isolating different sub-systems

Here are some examples.

```ruby
class Mailer
  include Interchange.new(:deliver, :deliveries)
end

class SMTPDelivery
  def deliver(mail)
    # send w/SMTP
  end

  def deliveries
    # check the email account
  end
end

class SnailMail
  def deliver(mail)
    # print the mail and go to the post office
  end

  def deliveries
    # go outside and check the mailbox
  end
end

mailer = Mailer.new
mailer.register :smtp, SMTPDelivery.new
mailer.register :snail_mail, SnailMail.new

mail.use :smtp
mail.deliver some_message

mail.use :null # switch back to the null implementation.
```

These objects are useful when you have an interaction that needs
to happen but implementations can vary widely. You can also use this
as class if you don't like the instance flavor.

```ruby
class Mailer
  extend Interchange.new(:foo, :bar, :bar)
end

Mailer.register, :smtp, SomeSmtpClass
```

Since `Interchange.new` returns a new module, you can call define
methods and call `super` just like normal.

```ruby
class Mailer
  include Interchange.new(:deliver)

  def deliver(mail)
    raise "No address" unless mail.to
    super
  end
end
```

This is great when you have some shared logic at the boundary but not
across implementations.

## Installation

Add this line to your application's Gemfile:

    gem 'interchange'

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install interchange


## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request
