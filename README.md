# Sidekiq::Result
[![Gem Version](https://badge.fury.io/rb/sidekiq_result.png)](http://badge.fury.io/rb/sidekiq_result)
[![Code Climate](https://codeclimate.com/github/ChrisMacNaughton/sidekiq_result.png)](https://codeclimate.com/github/ChrisMacNaughton/sidekiq_result)
[![Build Status](https://secure.travis-ci.org/ChrisMacNaughton/sidekiq_result.png)](http://travis-ci.org/ChrisMacNaughton/sidekiq_result)
[![Inline docs](https://inch-ci.org/github/ChrisMacNaughton/sidekiq_result.png)](https://inch-ci.org/github/ChrisMacNaughton/sidekiq_result)

An extension to [Sidekiq](http://github.com/mperham/sidekiq) message processing to track your job results.

## Installation

gem install sidekiq-result

## Usage

### Configuration

Configure your middleware chains, lookup [Middleware usage](https://github.com/mperham/sidekiq/wiki/Middleware)
on Sidekiq wiki for more info.

``` ruby
require 'sidekiq'
require 'sidekiq-result'

Sidekiq.configure_server do |config|
  config.server_middleware do |chain|
    chain.add Sidekiq::Result::ServerMiddleware, expiration: 30.minutes # default
  end
end
```

After that you can use your jobs as usual and only include `Sidekiq::Result::Worker` module if you want additional functionality of passing the job's finished stateto the client.

``` ruby
class MyJob
  include Sidekiq::Worker
  include Sidekiq::Result::Worker

  def perform(*args)
  # your code goes here
  end
end
```

To overwrite expiration on worker basis and don't use global expiration for all workers write a expiration method like this below:

``` ruby
class MyJob
  include Sidekiq::Worker
  include Sidekiq::Result::Worker

  def expiration
    @expiration ||= 60*60*24*30 # 30 days
  end

  def perform(*args)
    # your code goes here
  end
end
```

But keep in mind that such thing will store details of job as long as expiration is set, so it may charm your Redis storage/memory consumption. Because Redis stores all data in RAM.

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/chrismacnaughton/sidekiq_result.

## License

The gem is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).

