---
layout: post
title:      "LeaveNoTrace — A Sinatra MVC app"
date:       2021-01-20 18:19:55 +0000
permalink:  leavenotrace_a_sinatra_mvc_app
---

Project 2 at Flatiron Software Engineering program

The project requirements were to build an MVC Sinatra application using ActiveRecord to persist the data to a database. I originally thought I’d like to do a site called GoodNews where users could post good news or good experiences to combat the world of negative news out there. I also considered trying to build some sort of Time Banking app but realized, dang, I don’t have the skills quite yet.
Ultimately I decided to do a hiking app where users can post hikes they’ve completed and looked through other users hikes to get inspired for their next adventure. This was inspired by a recent trip to West Fork Trail in Sedona, AZ. If you haven’t been, you have to go. It’s all red rocks and high cliffs and beautiful green trees and a long, winding creek that you can swim or wade through. The antithesis of quarantine.

**The full requirements were as follows:**
1. Build an MVC Sinatra application.
2. Use ActiveRecord with Sinatra.
3. Use multiple models.
4. Use at least one has_many relationship on a User model and one belongs_to relationship on another model.
5. Must have user accounts — users must be able to sign up, sign in, and sign out.
6. Validate uniqueness of user login attribute (username or email).
7. Once logged in, a user must have the ability to create, read, update and destroy the resource that belongs_to user.
8. Ensure that users can edit and delete only their own resources — not resources created by other users.
9. Validate user input so bad data cannot be persisted to the database.
10. BONUS: Display validation failures to user with error messages. (This is an optional feature, challenge yourself and give it a shot!)

**File Structure**
The first thing I set up was my LeaveNoTrace directory with the ‘mkdir’ command. I added my config.ru file where I required my environment file, added a raise to make sure there were no migrations pending before running the app and mounted my controllers. I only had the ApplicationController at first. Make sure you only ‘run’ one controller and ‘use’ the rest. And the ‘run’ controller must be mounted last.
Image for post
use Rack::MethodOverride is middleware that allows you to use patch and delete requests
Before moving on to build models, views and controllers, I setup my Gemfile to load all dependencies and establish the connection to my database in the environment file. I ran bundle and moved on to my models.
Image for post
Image for post

**Models**
For a hiking app I decided I needed users and hikes. A user has_may hikes and a hike belongs_to user. I decided on their attributes and built the models with validations for presence of their attributes so I didn’t persist any bad data. I also validated for uniqueness of email and trailname for the user. I added the has_secure_password macro to the User model and used the BCrypt gem. With this combination I was able to salt and hash the user’s password and then authenticate that salted and hashed password in order to let them log in. The method slug replaces characters in a user’s trailname so that it can be used as the slug in the URL. The class method self.find_by_slug(slug) allows us to find a user by their slug.
Image for post
Image for post

**Migrations**
Knowing what attributes my User and Hike models needed meant I could create my users and hikes table. I ran rake db:create_migration NAME=create_users and rake db:create_migration NAME=create_hikes to create the migration files. I needed to add two columns that I forgot to add initially, so I had to create new migrations for those as well.
Image for post
Image for post
Image for post
Image for post
I ran rake db:migrate and…..nothing happened. It took me a while but I realized I was using ActiveRecord 6.0 with code in my config.ru file that wasn’t compatible with that version. Once I switched to ActiveRecord 4.2 and changed the code in my config.ru file I ran rake db:migrate. And it worked. Phew! Now I had my database. I created a seed file and ran rake db:seed so that I had a couple of users and a couple of hikes in my database to play around with.

**Views**
I structured my views according to convention.
sidebar — I love convention! It keeps things clean and organized. I love clean and organized. It’s one of the reasons I felt that programming could suite my personality. The fact that you start with a mess and piece by piece you pull together an organized, functional, logical application is super fun to me.
Image for post
In all the views I pushed myself to maintain consistency in formatting and styling. I took as much as I could out of the views and put it in the layout. The only thing left in the views are the forms or a few lines of code to display the user or hike’s details. I pushed myself to get a navbar in there, used bootstrap for styling, called for my flash alerts in the layout, and added a copyright footer which I though were all nice touches.
Image for post
Don’t forget that <%=yield%> line in your layout.erb!

**Controllers**
Your controllers allow you to match the HTTP request to a route in which you define the action. Together they are the controller action. The block of code in each controller action handles the requests and either renders a view or redirects to another route.
I followed RESTFUL routing conventions and I really appreciate how clear it makes the connection between routes and CRUD actions. The table we were provided with in our lessons sums this up perfectly.
Image for post
The routes I used in my hikes controller
I have an ApplicationController in which I enable sessions and define my helper methods to establish who the current user is and whether they are logged in. My UsersController, HikesController and SessionsController each inherit from the ApplicationController.

**The Bonus requirement — displaying error messages**
Without incorporating error messages into your app, the user will just see that the form or request doesn’t go through but they won’t know why. I used conditionals in my controller actions and displayed custom error messages in the views. You can do this by defining the flash[:message] or flash[:warning] in the controller action and then using erb to display the message or warning in the view. (When @hike.save fails below, I could have accessed the error messages through the error.messages instance variable but I wanted a simpler message to show up so I used flash messages.)
Image for post
Image for post
This iteration to display the flash values is in my layout.erb instead of in each individual view. I have them styled so they show up bold and red.
Another way to incorporate error messages is using validations and the error.messages instance variable. I have validation features in my both my User and Hike models. The validations in the user model validate the presence of email, trailname and password on signup. It also validates for the uniqueness of the email and trailname. My Hike model has validations for the presence of each attribute of a hike so that when a user fills out a new hike form, they can’t submit it without filling in all the fields. The validations prevent the new hike from being saved to the database with invalid data. If you use validations, Rails will run your validations before saving a new ActiveRecord object. If any validations produce an error, Rails will not save the object to your database. Any errors found can be accessed with errors.messages instance method and rendered in the view. I used that method on the user signup page as you can see below. It allows you to display a more detailed error message so the user knows why post request failed.
Image for post
When the @user.save conditional returns false the errors can be accessed with @user.errors.messages in the view. I used an instance variable @user so I could access it in the view.
Image for post
In the view I add the line to display the error messages in full sentences if there are any.
Image for post
The error message is specific and displayed in the view.
You can see that when using this method the error messages are specific to which validation failed, instead of more general messages like “Invalid name or password”.

**Features:**
My last project was pretty clean and simple. I am proud of it but I wanted to push myself more on this one. I did that by tackling the bonus requirement, putting effort into the layout.erb file, and adding some extra functionality beyond what was required:
Added a NavBar
Added a users show page
Added a home page
Added validations for uniqueness of email and trailname, as well as validations for presence for all hike attributes so that I didn’t persist any bad data.
Called for error messages in the layout.erb file as opposed to calling them in each view.
Added a confirmation before deletion on delete event handler — did this inline with html.
A goal for the future: Work on styling and see if I can add functionality that allows users to upload a photo when they post a hike.

Thanks for reading! Check out this repo at https://github.com/HMoreira2020/LeaveNoTrace
