## Mailchimp API v3
[![Gem Version 0.2.15](https://badge.fury.io/rb/mailchimp_api_v3.svg)](https://rubygems.org/gems/mailchimp_api_v3)
[![Gem downloads](https://img.shields.io/gem/dt/mailchimp_api_v3.svg)](https://rubygems.org/gems/mailchimp_api_v3)
[![Build status](https://img.shields.io/circleci/project/dominicsayers/mailchimp_api_v3/develop.svg)](https://circleci.com/gh/dominicsayers/mailchimp_api_v3)
[![Code quality](http://img.shields.io/codeclimate/github/dominicsayers/mailchimp_api_v3.svg?style=flat)](https://codeclimate.com/github/dominicsayers/mailchimp_api_v3)
[![Coverage Status](https://coveralls.io/repos/github/dominicsayers/mailchimp_api_v3/badge.svg?branch=develop)](https://coveralls.io/github/dominicsayers/mailchimp_api_v3?branch=develop)
[![Dependency Status](https://dependencyci.com/github/dominicsayers/mailchimp_api_v3/badge)](https://dependencyci.com/github/dominicsayers/mailchimp_api_v3)
[![Security](https://hakiri.io/github/dominicsayers/mailchimp_api_v3/develop.svg)](https://hakiri.io/github/dominicsayers/mailchimp_api_v3/develop)

A simple gem to interact with Mailchimp through the Mailchimp API v3.

### A note on Gibbon

I wrote this gem when [Gibbon](https://github.com/amro/gibbon) did not support Mailchimp's API v3. I believe it now does, and is a more regularly-maintained gem. You are welcome to use my gem but I don't have a great deal of time to deal with issues or enhance it further.

### Project status

This gem currently does everything I need for my project but it is far from complete. I will willingly accept good quality pull requests to complete the feature set.

My own requirements are for subscription management for my mailing list, so the gem can add and remove members from a list and manage their interests.

### Installation

Add this line to your application's Gemfile:

    gem 'mailchimp_api_v3'

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install mailchimp_api_v3

### Usage

The Mailchimp API documentation is here: http://kb.mailchimp.com/api. Their suggestions for subscriber management are here: http://kb.mailchimp.com/api/article/how-to-manage-subscribers.

To connect to the Mailchimp API you need to supply an API key. You can do this explicitly when you connect, or you can set an environment variable `MAILCHIMP_API_KEY`.

Examples:

```ruby
Mailchimp.connect(my_api_key) # Uses the API key in my_api_key
Mailchimp.connect # Uses ENV['MAILCHIMP_API_KEY']
```

```ruby
Mailchimp.connect.lists
```

```ruby
mailchimp = Mailchimp.connect
list = mailchimp.lists.find_by name: 'My first list'
```

```ruby
mailchimp = Mailchimp.connect
member = mailchimp.lists('e73f5910ca').members('ann@example.com')
member.name # => "Ann Example"
member.update last_name: 'Williams'
```

```ruby
# ActiveRecord-like methods
mailchimp = Mailchimp.connect
members = mailchimp.lists('e73f5910ca').where last_name: 'Example'
members.count # => 3
member = mailchimp.lists('e73f5910ca').members.first_or_create(
  email_address: 'ann@example.com',
  name: 'Ann Example',
  status: 'subscribed'
)
```

```ruby
# Dealing with big lists
mailchimp = Mailchimp.connect
list = mailchimp.lists('e73f5910ca')

list.members.find_each do |member|
  # Do something
end

list.members.find_in_pages do |members|
  # Do something to a page of members
end
```

### Exception handling

The gem will raise an exception if it encounters a condition where it cannot continue. All exceptions will be subclasses
of `Mailchimp::Exception` as follows:

- Mailchimp::Exception
    - DataException
    - APIKeyError
    - NotFound
    - Duplicate
    - MissingField
    - BadRequest
    - UnknownAttribute
    - MissingId

Exceptions have a `message` as usual, but also pass on the following properties from the information returned by the
Mailchimp API: `type`, `title`, `errors` (which, if present, is an array). For example:

```
rescue Mailchimp::Exception => e
  puts e.message
  puts e.type
  puts e.title

  e.errors.each do |error|
    puts error['field']
    puts error['message']
  end if e.errors
end
```

### Contributing

[![Developer](http://img.shields.io/badge/developer-awesome-brightgreen.svg?style=flat)](https://www.dominicsayers.com)

1.  Fork it
1.  Create your feature branch (`git checkout -b my-new-feature`)
1.  Commit your changes (`git commit -am 'Add some feature'`)
1.  Push to the branch (`git push origin my-new-feature`)
1.  Create new Pull Request

### Testing

The tests use Rspec and VCR. To avoid including my test API key in the VCR cassettes the tests use Erb to insert an api key at runtime. Whatever API key is available will allow the tests to pass.

If you need to add more cassettes you can use your own test API key then edit the VCR cassette in the same way before you issue the pull request.

### Acknowledgements

I used the sample code in https://github.com/mailchimp/APIv3-examples as my starting point for this gem. Thanks to
the Mailchimp developers for the head start.
