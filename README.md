Note: this is a transcript of the [screencast](http://spinejs.com/pages/screencasts "Spine & Rails integration in 7 minutes"). I just transcribed it. The screencast was created by Alex MacCaw (maccman).

---
The first step is to generate our rails application.

_opens terminal window_

    rails new spine-rails-tree

Now that that's generated let's open it up.

    cd spine-rails-tree

_opens directory in an editor_

Now we need to add the spine-rails gem to the gemfile

_opens Gemfile and adds the line_

    gem 'spine-rails'

and run bundle update

_switches to terminal_

    bundle update

So the next step is to generate the scaffold. We're going to generate a "post" model and a "post" controller. The post model is going to have "title" and "content" attributes.

    rails generate scaffold post title:string content:string

Now let's run the migrations.

    rake db:migrate

Now let's set up spine.

    rails g spine:new

This is going to create a new spine application under app/assets/javascripts/app so let's take a look at that. There is one file, index.coffee, which is the basis for the rest of our application. Let's actually generate a new model called "post" in spine. It's got two attributes a "title" and a "content."

    rails g spine:model post title content

This matches the model that we've generated on the server side. Now let's run the server

    rails s

and navigate to the rails application.

_types http://localhost:3000/posts in a browser window_

There are no posts at the moment, let's create a new post. We can use the browser's console to interact with spine directly.

_opens safari's console window_

So we can say:

    var post = App.Post.create({
      title: "Hello World!",
      content: "Spine & Rails, sitting in a tree!"
    });

and in the background a request to /posts is going to be sent. It's going to be a POST request and it is going to return a 201 from rails. If we refresh the page, you can see that the new post is now there. We can also fetch all of the posts from rails:

    App.Post.fetch();

and have a look at them in memory:

    App.Post.first().content

then we can delete them:

    App.Post.first().destroy()

Now let's refresh the page and the post is gone. We can see how easy and quick it is to integrate spine and rails.

So now that we have spine and rails integrated in the console, let's replace the rails scaffolding with the spine scaffolding so I use an ajax GUI for editing posts. Spine scaffolding uses "eco" templates, so we need to add the "eco" gem to the Gemfile. Let's do that now.

_opens the Gemfile in an editor and adds the line_

    gem 'eco'

Let's run bundle install.

_switches to terminal_

    bundle install

Okay, so let's generate the spine scaffold.

    rails g spine:scaffold Post title content

Okay, so that's generated a spine scaffold for us, let's have a look at that. We have this new controller "posts.js.coffee".

_opens app/assets/javascripts/app/controllers/posts.js.coffee_

This has a number of sub-controllers inside of it: a class "New", a class "Edit", a class "Show", and a class "Index." These will show a list of the posts, a particular post, edit a post, and create a new post. Then we have the "Spine.Stack". It is a way of collecting multiple controllers together and ensuring that only one controller is ever shown at one time. It is a very common scenario and it is perfect for this case because we want to show: an "edit" page, a "new" page, a "show" page, and an "index" page and we want to make sure those pages are shown independently and not all at once. So here you can see controllers for our application: "Index", "Edit", "Show", and "New", the routes associated with those controllers, and a couple of options such as the "default" controller and the "className" for the stack.

For our stack to work correctly, we need to add a bit of CSS to make sure that only one controller is shown at one time. Let's go to app.css

_opens app/assets/stylesheets/application.css_

and we're going to say "stack" and we're going to select the children that don't have the class "active" and we're going to make sure that they're not displayed.

    .stack > *:not(.active) {
      display: none;
    }

The next thing we need to do is instantiate our controller in our application. We're going to to inside our app controller and append our posts controller.

_opens app/assets/javascripts/app/index.js.coffee and adds the line to the constructor_

    @append(@posts = new App.Posts)

The last thing we need to do is remove the scaffolding that rails has created.

_deletes app/views/posts/form.html.erb, app/views/posts/edit.html.erb, app/views/new.html.erb, and app/views/posts/show.html.erb_
_opens app/views/posts/index.html.erb, deletes the contents_

We're going to create a div with id "app" which is where our app is going to be located and then when the page loads, we're going to instantiate our application using that div.

_adds the following to app/views/posts/index.html.erb_

    <div id="app"></div>
    
    <script type="text/javascript" charset="utf-8">
      jQuery(function(){
        new App({el: $('app')});
      });
    </script>

_switches back to browser window_

Okay, so let's refresh the page and you can see that we're using spine as a scaffold instead of rails. Everything is instant and we have ways of deleting records and editing records and creating new ones. 

_opens safari's console to "Network" tab_

As before, you can see that whenever we create, read, update, or delete an ajax call is being sent off to rails. If we refresh the page we can see that those changes have been posted to the database.

So that's the end of the screencast. If you have any questions pop on the mailing list of the IRC channel and all of the source is available online (spinejs.com)