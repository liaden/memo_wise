
<p>
  <img src="logo/logo.png" width="175"/>
</p>

# `MemoWise`

[![Tests](https://github.com/panorama-ed/memo_wise/workflows/Main/badge.svg)](https://github.com/panorama-ed/memo_wise/actions?query=workflow%3AMain)
[![Code Coverage](https://codecov.io/gh/panorama-ed/memo_wise/branch/main/graph/badge.svg)](https://codecov.io/gh/panorama-ed/memo_wise/branches/main)
[![Yard Docs](http://img.shields.io/badge/yard-docs-blue.svg)](http://rubydoc.info/github/panorama-ed/memo_wise)
[![Inline docs](http://inch-ci.org/github/panorama-ed/memo_wise.svg?branch=main)](http://inch-ci.org/github/panorama-ed/memo_wise)
[![Gem Version](https://img.shields.io/gem/v/memo_wise.svg)](https://rubygems.org/gems/memo_wise)
[![Gem Downloads](https://img.shields.io/gem/dt/memo_wise.svg)](https://rubygems.org/gems/memo_wise)

## Why `MemoWise`?

`MemoWise` is **the wise choice for Ruby memoization**, featuring:

  * Fast performance of memoized reads (with [benchmarks](#benchmarks))
  * Support for [resetting](https://rubydoc.info/github/panorama-ed/memo_wise/MemoWise#reset_memo_wise-instance_method) and [presetting](https://rubydoc.info/github/panorama-ed/memo_wise/MemoWise#preset_memo_wise-instance_method) memoized values
  * Support for memoization on frozen objects
  * Support for memoization of class and module methods
  * Full [documentation](https://rubydoc.info/github/panorama-ed/memo_wise/MemoWise) and [test coverage](https://codecov.io/gh/panorama-ed/memo_wise)!

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'memo_wise'
```

And then execute:

    $ bundle install

Or install it yourself as:

    $ gem install memo_wise

## Usage

When you `prepend MemoWise` within a class or module, `MemoWise` exposes three
methods:

- [`memo_wise`](https://rubydoc.info/github/panorama-ed/memo_wise/MemoWise#memo_wise-class_method)
- [`preset_memo_wise`](https://rubydoc.info/github/panorama-ed/memo_wise/MemoWise#preset_memo_wise-instance_method)
- [`reset_memo_wise`](https://rubydoc.info/github/panorama-ed/memo_wise/MemoWise#reset_memo_wise-instance_method)

```ruby
class Example
  prepend MemoWise

  def slow_value(x)
    sleep x
    x
  end
  memo_wise :slow_value
end

ex = Example.new
ex.slow_value(2) # => 2 # Sleeps for 2 seconds before returning
ex.slow_value(2) # => 2 # Returns immediately because the result is memoized

ex.reset_memo_wise(:slow_value) # Resets all memoized results for slow_value
ex.slow_value(2) # => 2 # Sleeps for 2 seconds before returning
ex.slow_value(2) # => 2 # Returns immediately because the result is memoized
# NOTE: Memoization can also be reset for all methods, or for just one argument.

ex.preset_memo_wise(:slow_value, 3) { 4 } # Store 4 as the result for slow_value(3)
ex.slow_value(3) # => 4 # Returns immediately because the result is memoized
ex.reset_memo_wise # Resets all memoized results for all methods on ex
```

The same three methods are exposed for class methods as well:

```ruby
class Example
  prepend MemoWise

  def self.class_slow_value(x)
    sleep x
    x
  end
  memo_wise self: :class_slow_value
end

Example.class_slow_value(2) # => 2 # Sleeps for 2 seconds before returning
Example.class_slow_value(2) # => 2 # Returns immediately because the result is memoized

Example.reset_memo_wise(:class_slow_value) # Resets all memoized results for class_slow_value

Example.preset_memo_wise(:class_slow_value, 3) { 4 } # Store 4 as the result for slow_value(3)
Example.class_slow_value(3) # => 4 # Returns immediately because the result is memoized
Example.reset_memo_wise # Resets all memoized results for all methods on class
```

**NOTE:** Methods which take implicit or explicit block arguments cannot be
memoized.

For more usage details, see our detailed [documentation](#documentation).

## Benchmarks

Benchmarks are run in GitHub Actions, and the tables below are updated with every code change. **Values >1.00x represent how much _slower_ each gem’s memoized value retrieval is than the latest commit of `MemoWise`**, according to [`benchmark-ips`](https://github.com/evanphx/benchmark-ips) (2.9.1).

Results using Ruby 3.0.2:

|Method arguments|`Dry::Core` (0.7.1)|`Memery` (1.4.0)|
|--|--|--|
|`()` (none)|1.57x|19.86x|
|`(a)`|2.49x|12.09x|
|`(a, b)`|1.10x|5.24x|
|`(a:)`|2.27x|24.09x|
|`(a:, b:)`|1.09x|10.65x|
|`(a, b:)`|1.05x|10.27x|
|`(a, *args)`|2.10x|5.01x|
|`(a:, **kwargs)`|1.61x|6.24x|
|`(a, *args, b:, **kwargs)`|1.05x|2.94x|

Results using Ruby 2.7.4 (because these gems raise errors in Ruby 3.x):

|Method arguments|`DDMemoize` (1.0.0)|`Memoist` (0.16.2)|`Memoized` (1.0.2)|`Memoizer` (1.0.3)|
|--|--|--|--|--|
|`()` (none)|36.49x|3.55x|1.66x|4.13x|
|`(a)`|25.32x|18.07x|13.17x|14.84x|
|`(a, b)`|4.11x|3.04x|2.34x|2.59x|
|`(a:)`|35.71x|29.25x|24.63x|26.21x|
|`(a:, b:)`|6.70x|5.51x|4.88x|5.09x|
|`(a, b:)`|6.27x|5.25x|4.48x|4.75x|
|`(a, *args)`|5.43x|4.05x|3.36x|3.46x|
|`(a:, **kwargs)`|4.08x|3.48x|2.98x|3.15x|
|`(a, *args, b:, **kwargs)`|2.96x|2.57x|2.34x|2.37x|

You can run benchmarks yourself with:

```bash
$ cd benchmarks
$ bundle install
$ bundle exec ruby benchmarks.rb
```

If your results differ from what's posted here,
[let us know](https://github.com/panorama-ed/memo_wise/issues/new)!

## Documentation

### Documentation is Automatically Generated

We maintain API documentation using [YARD](https://yardoc.org/), which is
published automatically at
[RubyDoc.info](https://rubydoc.info/github/panorama-ed/memo_wise/MemoWise). To
edit documentation locally and see it rendered in your browser, run:

```bash
bundle exec yard server
```

### Documentation Examples are Automatically Tested

We use [yard-doctest](https://github.com/p0deje/yard-doctest) to test all
code examples in our YARD documentation. To run `doctest` locally:

```bash
bundle exec yard doctest
```

We use [dokaz](https://github.com/zverok/dokaz) to test all code examples in
this README.md file, and all other non-code documentation. To run `dokaz`
locally:

```bash
bundle exec dokaz
```

### A Note on Testing

When testing memoized *module* methods, note that some testing setups will
reuse the same instance (which `include`s/`extend`s/`prepend`s the module)
across tests, which can result in confusing test failures when this differs from
how you use the code in production.

For example, Rails view helpers are modules that are commonly tested with a
[shared `view` instance](https://github.com/rails/rails/blob/291a3d2ef29a3842d1156ada7526f4ee60dd2b59/actionview/lib/action_view/test_case.rb#L203-L214). Rails initializes a new view instance for each web request so any view helper
methods would only be memoized for the duration of that web request, but in
tests (such as when using
[`rspec-rails`'s `helper`](https://github.com/rspec/rspec-rails/blob/main/lib/rspec/rails/example/helper_example_group.rb#L22-L27)),
the memoization may persist across tests. In this case, simply reset the
memoization between your tests with something like:

```ruby
after(:each) { helper.reset_memo_wise }
```

## Further Reading

We've written more about MemoWise in a series of blog posts:

- [Introducing: MemoWise](https://medium.com/building-panorama-education/introducing-memowise-51a5f0523489)
- [Optimizing MemoWise Performance](https://ja.cob.land/optimizing-memowise-performance)
- [Esosteric Ruby in MemoWise](https://jemma.dev/blog/esoteric-ruby-in-memowise)

## Logo

`MemoWise`'s logo was created by [Luci Cooke](https://www.lucicooke.com/). The
logo is licensed under a
[Creative Commons Attribution-NonCommercial 4.0 International License](https://creativecommons.org/licenses/by-nc/4.0/deed.en).

## Contributing

[Bug reports](https://github.com/panorama-ed/memo_wise/issues) and
[pull requests](https://github.com/panorama-ed/memo_wise/pulls) are welcome on GitHub at
https://github.com/panorama-ed/memo_wise. This project is intended to be a safe,
welcoming space for collaboration, and contributors are expected to adhere to
the [code of conduct](https://github.com/panorama-ed/memo_wise/blob/main/CODE_OF_CONDUCT.md).

## Releasing

To make a new release of `MemoWise` to
[RubyGems](https://rubygems.org/gems/memo_wise), first install the release
dependencies (e.g. `rake`) as follows:

```shell
bundle config --local with 'release'
bundle install
```

Then carry out these steps:

1. Update `CHANGELOG.md`:
   - Add an entry for the upcoming version _x.y.z_
   - Move content from _Unreleased_ to the upcoming version _x.y.z_
   - Commit with title `Update CHANGELOG.md for x.y.z`

2. Update `lib/memo_wise/version.rb`
   - Replace with upcoming version _x.y.z_
   - Commit with title `Bump version to x.y.z`

3. `bundle exec rake release`

## License

The gem is available as open source under the terms of the [MIT License](https://opensource.org/licenses/MIT).

## Code of Conduct

Everyone interacting in the `MemoWise` project's codebases, issue trackers, chat
rooms and mailing lists is expected to follow the
[code of conduct](https://github.com/panorama-ed/memo_wise/blob/main/CODE_OF_CONDUCT.md).
