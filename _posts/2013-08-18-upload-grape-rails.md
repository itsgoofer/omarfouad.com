---
title: File Upload API with Grape and Rails
date: 2013-08-18 00:00:00 Z
permalink: "/blog/2013/08/18/file-upload-api-with-grape-and-rails/"
layout: article
---

The main difference between the web now and the web some years ago is the front end development. Most of the work one time was done on the server and the website that appeared to users were just a bunch of divs and tables to show data coming from the back-end. Interactivity was very limited at that time and AJAX was a myth and who had the guts to do some of it could be called a hero.
People went for Flash because it was a great platform which provided a higher level of web experience, animations, async operations, media management etc. But, that's in the past. Today, JavaScript and HTML5 have changed the game, we all know, along with the great browsers that developers have on their disposal (not all of them).

I've been reading about serverside management in the last couple of month and I've realized the majority of people are going for APIs. If you have a RESTful API, virutally everything that is connected to the internet can consume it. Web sites, web apps, mobile apps, or even space ships.Instead of writing different backends for different purposes, write one solid, generic and secure API and pimp the world. 

So, for those who are going to the same directions, Ruby along with [Rails](http://rubyonrails.org/) or [Sinatra](http://sinatrarb.com), would be the go. I personally prefer Rails for API development because it's easy, robust and its community is awesome.
While you could create a simple API using Sinatra, I'd choose Rails this time, because it has already some good functionalities under its hood, like database management. The rest of the API related functionalities are provided by [Grape](https://github.com/intridea/grape). Grape is described as a "micro-framework for creating REST-like APIs in Ruby". It's [really easy to set up and use](https://github.com/intridea/grape#installation).

Assuming you already know how to create a Rails app and [mount Grape on top of it (and eventually use it)](https://github.com/intridea/grape#mounting), let me go to the point and tell you more about [Paperclip](https://github.com/thoughtbot/paperclip).

Paperclip is a gem that makes the file uploading process to Rack based apps, a joke. There are [many](https://github.com/sferik/rails_admin/wiki/CarrierWave) [other](https://github.com/markevans/dragonfly) gems for that, but I found Paperclip
more flexible and easy to use. As a result, I will show you how to spin the wheel with it.

So after you have created your Rails app, mounted grape and created an API class let's install Paperclip. In your Gemfile add

```shell
gem 'paperclip'
```

Install your bundle with `bundle install` on terminal and you should be good to go.
Now let's assume you have a model called `User` with a `name` and `avatar` attribute. If not, just do:

```shell
rails g model User name:string avatar:string avatar_path:string
```

Then, before running the migration, go to the migration file of your User table and change `t.string :avatar` to `t.attachment :avatar`. It should look like this:

```ruby
# in /db/migrate/

class CreateUsers < ActiveRecord::Migration
  def change
    create_table :users do |t|
      t.string :name
      t.attachment :avatar
      t.string :avatar_path

      t.timestamps
    end
  end
end
```

Now run your migration:

```shell
rake db:migrate
```

With this we created an API resource which will just add a user with its name and upload their avatar storing a path reference of the uploaded image in `:avatar_path`, which obviously, is a string.
So let's create the API class in `/app/api/main/api.rb`. Once you do make sure the module name is the one you are mounting in your `route.rb` file as:

```shell
mount Main::API => '/'
```

Then:

```shell
# in /app/main/api.rb
module Main
	class API < Grape::API
		version 'v1', using: :header, vendor: 'some_vendor'
		format :json
	end
end
```

We are almost ready. All we need to do is to add the resouce of our upload service to it. So inside the class write:

```ruby
resource :upload do
	post do
		# takes the :avatar value and assigns it to a variable
		avatar = params[:avatar]

		# the avatar parameter needs to be converted to a
		# hash that paperclip understands as:
		attachment = {
			:filename => avatar[:filename],
			:type => avatar[:type],
			:headers => avatar[:head],
			:tempfile => avatar[:tempfile]
		}

		# creates a new User object
		user = User.new

		# This is the kind of File object Grape understands so let's
		# pass the hash to it
		user.avatar = ActionDispatch::Http::UploadedFile.new(attachment)

		# easy
		user.avatar_path = attachment[:filename]

		# even easier
		user.name = "dummy name"

		# and...
		user.save
	end
end
```

As you see in the commented code above, the attachment hash is needed to "hashify" the file object coming from the front-end. The pass this hash to the `ActionDispatch::Http::UploadedFile.new` method which returns a proper object that Grape understands and the file is uploaded. 

This step was undocumented and I had to do massive searches to put together this code and, when it worked, I kissed my wife.
But we are not done yet. We need to tell our User model that one attribute has to be treated as an upload:

```ruby
	`# in /app/models/user.rb
class User < ActiveRecord::Base
  	attr_accessible :name, :avatar_path, :avatar
  	has_attached_file :avatar, :path => ":rails_root/public/avatars/:filename"
end
```

`has_attached_file :avatar` tells Ruby that this accessor, is the one who will receive the file. `:path` is optional, in case you have a better place where to store your file.

Now, the moment of truth. do a `rails s` and let's try to upload our file from terminal using `curl`:

```shell
curl http://apiuploadtest.dev/upload -F avatar=@YOUR FILE PATH HERE
```

Note that the parameter name **must** be `avatar` or you'll get a 500 error. then the file path with an `@` prefix. so if your file is an image.png on your desktop you should go:

```shell
curl http://apiuploadtest.dev/upload -F avatar=@~/Desktop/image.png`
```

If everything went good, you should have a smile on your face once you open your public folder. 
In the next post I will show you how to upload a file to the same endpoint using a form and pure AJAX. 
Finally feel free to feedback or seek for help in the comments.