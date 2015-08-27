# imgix-rails

`imgix-rails` is a gem designed to make integrating imgix into your Rails app easier. It builds on [imgix-rb](https://github.com/imgix/imgix-rb) to offer a few Rails-specific interfaces.

imgix is a real-time image processing service and CDN. It allows you to manipulate images merely by changing their URL parameters. For a full list of URL parameters, please see the [imgix URL API documentation](https://www.imgix.com/docs/reference).

We recommend using something like [Paperclip](https://github.com/thoughtbot/paperclip), [Carrierwave](https://github.com/carrierwaveuploader/carrierwave), or [s3_direct_upload](https://github.com/waynehoover/s3_direct_upload) to handle uploads and then serving the images out using this gem.

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'imgix-rails'
```

And then execute:

    $ bundle

## Usage

imgix-rails provides a few different hooks to work with your existing Rails application. All current methods are drop-in replacements for the `image_tag` helper.

### Configuration

Before you get started, you will need to define your imgix configuration in your `config/application.rb`, or in an environment-specific configuration file.

```ruby
Rails.application.configure do
  config.imgix = {
    source: "Name of your source, e.g. assets.imgix.net",
    secure_url_token: "optional secure URL token found in your dashboard (https://webapp.imgix.com)"
  }
end
```

### ix_image_tag

The simplest way of working with imgix-rails is to use the `ix_image_tag`, this allows you to pass parameters to imgix to handle things like resizing, cropping, etc.

```erb
<%= ix_image_tag('/path/to/image.jpg', { w: 400, h: 300 }) %>
```

Will render out HTML like the following:

```html
<img src="https://assets.imgix.net/path/to/image?w=400&h=300" />
```

We recommend leveraging this to generate powerful helpers within your application like the following:

```ruby
def profile_image_tag(user)
  ix_image_tag(user.profile_image_url, { w: 100, h: 200, fit: 'crop' })
end
```

Then rendering the portrait in your application is very easy:

```erb
<%= profile_image_tag(@user) %>
```

### ix_responsive_image_tag

The `ix_responsive_image_tag` helper method makes it easy to bring responsive imagery to your Rails app. We talk a bit about using the `srcset` attribute in an application in the following blog post: [“Responsive Images with `srcset` and imgix.”](http://blog.imgix.com/post/127012184664/responsive-images-with-srcset-imgix)

The `ix_responsive_image_tag` will generate an `<img>` element with a filled-out `srcset` attribute that leans on imgix to do the hard work.

It will use the configured device-pixel-ratios in the `config.imgix[:responsive_resolutions]` value. It will default to `[1, 2]`.

```erb
<%= ix_responsive_image_tag('/users/1/avatar.png', { w: 400, h: 300 }) %>
```

This will generate the following HTML:

```html
<img
  srcset="https://assets.imgix.net/users/1/avatar.png?w=400&h=300 1x,
          https://assets.imgix.net/users/1/avatar.png?w=400&h=300&dpr=2 2x"
  src="https://assets.imgix.net/users/1/avatar.png?w=400&h=300"
/>
```

## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release` to create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

1. Fork it ( https://github.com/[my-github-username]/imgix-rails/fork )
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create a new Pull Request