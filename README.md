# themis-checker-server
[![Latest Version](https://img.shields.io/gem/v/themis-checker-server.svg?style=flat-square)](https://rubygems.org/gems/themis-checker-server)
[![License](https://img.shields.io/github/license/aspyatkin/themis-checker-server.svg?style=flat-square)](https://github.com/aspyatkin/themis-checker-server/blob/master/LICENSE)
[![Dependencies status](https://img.shields.io/gemnasium/aspyatkin/themis-checker-server.svg?style=flat-square)](https://gemnasium.com/aspyatkin/themis-checker-server)
[![Code Climate](https://img.shields.io/codeclimate/github/aspyatkin/themis-checker-server.svg?style=flat-square)](https://codeclimate.com/github/aspyatkin/themis-checker-server)  
A Ruby gem for creating service checker for [Themis Finals](https://github.com/aspyatkin/themis-finals), CTF contest checking system.

## Installation
```sh
gem install themis-checker-server
```
or just add `gem 'themis-checker-server'` to your Gemfile and run `bundle`.

## Example
A service checker subclasses `Themis::Checker::Server` and overrides two methods.

Here's the example:
```ruby
require 'themis/checker/server'
require 'themis/checker/result'

class SampleChecker < Themis::Checker::Server
    def push(endpoint, flag, adjunct, metadata)
        # business logic...
        return Themis::Checker::Result::UP, adjunct
    end

    def pull(endpoint, flag, adjunct, metadata)
        # business logic...
        Themis::Checker::Result::UP
    end
end

checker = SampleChecker.new
checker.run
```

### Operation status
See [themis-checker-result](https://github.com/aspyatkin/themis-checker-result).

## Configuration
To run service checker, a bunch of environment variables should be specified:
### `BEANSTALKD_URI`
A connection string for Beanstalk server.
### `TUBE_LISTEN`
A tube to listen for commands from the central server.
### `TUBE_REPORT`
A tube to report operation results to the central server.
### `LOG_LEVEL`
An optional service checker log level. One of `DEBUG`, `INFO`, `WARN`, `ERROR`, `FATAL` or `UNKNOWN`. Default is `INFO`.
### `STDOUT_SYNC`
An optional parameter controlling `$stdout.sync`.

## Tips
### Logging
There is a class member `logger`. You can use it in overriden methods `push` and `pull` as `@logger`.
### Stopping service checker
Service checker stops on `INT` signal.
### Configuration for [God](https://github.com/mojombo/god) process manager
```ruby
God.watch do |w|
    w.name = 'sample'
    w.dir = '/path/to/checker'
    w.uid = 'nobody'
    w.gid = 'nogroup'
    w.log = '/path/to/checker/logs/checker.log'
    w.start = 'bundle exec ruby checker.rb'
    w.env = {
        'BEANSTALKD_URI' => '127.0.0.1:11300',
        'LOG_LEVEL' => 'INFO',
        'STDOUT_SYNC' => 'true',
        'TUBE_LISTEN' => 'volgactf.service.sample.listen',
        'TUBE_REPORT' => 'volgactf.service.sample.report'
    }
    w.stop_signal = 'INT'
    w.keepalive
end
```

## See also
- [themis-finals](https://github.com/aspyatkin/themis-finals)
- [themis-finals-guidelines](https://github.com/aspyatkin/themis-finals-guidelines)
- [themis-finals-infrastructure](https://github.com/aspyatkin/themis-finals-infrastructure)
- [themis-attack-protocol](https://github.com/aspyatkin/themis-attack-protocol)
- [themis-attack-py](https://github.com/aspyatkin/themis-attack-py)
- [themis-attack-result](https://github.com/aspyatkin/themis-attack-result)
- [themis-checker-py](https://github.com/aspyatkin/themis-checker-py)
- [themis-checker-result](https://github.com/aspyatkin/themis-checker-result)

## License
MIT @ [Alexander Pyatkin](https://github.com/aspyatkin)
