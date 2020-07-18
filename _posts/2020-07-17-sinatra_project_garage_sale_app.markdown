---
layout: post
title:      "Sinatra Project: Garage Sale App"
date:       2020-07-18 02:41:08 +0000
permalink:  sinatra_project_garage_sale_app
---

### The App

The app allows user to create listings with items and keep track of inventory.

The simplicity of the app is based on a craigslist but with better inventory control.


### How It Works

The User will:

1. Sign up
2. Create a list
3. Add items
4. Click Sold button to mark sold
5. Restore sold item
6. Delete Items
7. Edit items
8. Delete Listings
9. Edit Listings
10. Delete their account

See images:
[https://imgur.com/a/7oSedag]

### Special Challenges

The basics of the application were easy: routes, MVC, etc. The hard part was error messages, validation, CSS, Front-End, and protecting routes.

I would like to warn future sinatra programmers to never use Rack:Flash. It was a nightmare that was more easily fixed by using Sinatra::Flash. Which may sound like a no brainer but sometimes we take the hard route to learn our lessons harder. 

Sinatra::Flash offers a very simple way of showing flash messages. The hard part was trying to create messages for the many different errors without a long if loop or case when loop. 

```
    def create_user_error_messages(user)
      flash[:password_error] = user.errors.messages[:password][0]
      flash[:password_confirmation] = user.errors.messages[:password_confirmation][0]
      flash[:email_error] = user.errors.messages[:email][0]
      flash[:username_error] = user.errors.messages[:username][0]
      flash[:username_used]  = user.username
      flash[:email_used] = user.email
    end

    def create_user_error_messages_reader
      @password_error = flash[:password_error]
      @passconfirm_error = flash[:password_confirmation]
      @email_error = flash[:email_error]
      @username_error = flash[:username_error]
      @username_used = flash[:username_used]
      @email_used = flash[:email_used]
    end
```

I used these helper methods instead. I look forward to a better error handling method but with sinatra it was as simple as these helper methods and then:

```
<% if @error != nil %>
  <div class="alert alert-danger" role="alert">
    <%= @error%>
    <button type="button" class="close" data-dismiss="alert" aria-label="Close">
      <span aria-hidden="true">&times;</span>
    </button>
  </div>
<% end %>
```

Utilizing this inside the erb file to display the error when it occurs.

### CSS and Front End Development

I used bootstrap for this project as it seemed like the most simple approach to creating a great looking website. 
After much research and the wide learning curve of understanding how Bootstrap works, I created a simple looking layout so it would look more appealing and work well on mobile phones. 

Bootstrap is a wonderful library, but I have to wonder how much more difficult it would be to create my own css files from scratch. The website is no where near how I would love for it to look. 

###  Password Validation

```
PASSWORD_FORMAT = /\A
  (?=.{8,})          # Must contain 8 or more characters
  (?=.*\d)           # Must contain a digit
  (?=.*[a-z])        # Must contain a lower case character
  (?=.*[A-Z])        # Must contain an upper case character
  (?=.*[[:^alnum:]]) # Must contain a symbol
/x

  validates :password, :confirmation => true,
                       :length => { :within => 6..40 },
                       format: { with: PASSWORD_FORMAT },
                       :on => :create

  validates :password, :confirmation => true,
                       :length => { :within => 6..40 },
                       format: { with: PASSWORD_FORMAT },
                       :allow_blank => false,
                       :on => :update
```

###  404 Page

Sinatra offers a way to go to a 404 page when the route hasn't been defined and raises a "Sinatra doesn't know this ditty" error. 

```
 not_found do
    erb :'404_page'
  end
```

## Final Thoughts
This sinatra project was a fun learning experience of handling mutiple dev docs and constantly trying to learn ways to negotiate with sinatra for functionality. 

I learned that reading the docs (or as much as you can when seeking a problem) is invaluable. The community, stackoverflow, and other resources are also invaluable. 

I could use more practice and reading on CSS and HTML. However, I am very much looking forward to learning Javascript and utilizing better libraries to get the results I needed!
