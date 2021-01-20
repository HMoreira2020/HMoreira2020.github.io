---
layout: post
title:      "PageTurners — A Ruby on Rails App for Book Lovers"
date:       2021-01-20 18:24:35 +0000
permalink:  pageturners_a_ruby_on_rails_app_for_book_lovers
---


In order to put my knowledge of Ruby and Rails to work for our third project at Flatiron, I built an app for readers who wanted to create and manage book lists, search for books on Google Books, add books to their lists, and review books. In an effort to learn as much as possible I added goals for myself that went beyond the project requirements.

**Project Requirements:**

**Additional Features added:**
Include more than one many-to-many relationship
Include more than one nested resource
Include both Facebook and Google for Omniauth for sign-in
Implemente Google Books API for books search and creation
Display book covers
Allow a user to add a book to a list and write a review from the book show page.
Allow a user to remove a book off of their list
Add Bootstrap CSS
Use Pundit for authorization
Use Pagy to paginate the books index page
Use Active Storage and Image Magick to give users ability to upload a profile image.
Display user’s profile image in navbar
I’ll talk about what I think were big lessons for me in this project: nailing down complex associations; creating forms to handle adding a book to a list and deleting it from a list; implementing the Google Books API for a book search and creating books in the database.

**Mapping Associations**
I started with the associations and learned a very useful option to help with my associations when there were multiple has_many :users lines in my Book model. (see below).
Image for post
**Helpful tip**: Initially there were two has_many associations with books and users in my book model → Book has_many :users, through: :reviews and Book has_many: :users, through: :lists. If I called book.users I wouldn’t know which users I was getting. In order to distinguish between a book’s users through lists and a book’s users through reviews I renamed the users who reviewed the book “reviewers” → Book has_many: :reviewers, through: :reviews, source: :user. I can then call book.users to get all the users who added that book to a list and call book.reviewers to get all the users that reviewed that book. I made the same change on the User side with User has_many: :reviewed_books, through: :reviews, source: :book to distinguish the books a user reviewed from the books that they added to their lists.

Before:
```
Class Book < ApplicationRecord
has_many: lists, through: :books_lists
has_many: users, through: :lists
has_many: :reviews
has_many: :users, through: :reviews
```

After:
```
Class Book < ApplicationRecord
has_many: lists, through: :books_lists
has_many: users, through: :lists
has_many: :reviews
has_many: :reviewers, through: :reviews, source: :user
```
Now I can call book.reviewers or book.users and know that I am getting the book’s reviewers or the book’s users (through lists) respectively.

**Adding a Book to a List**
I wanted to be able to add a book to a list and a review to a book all from a book’s show page instead of having them click a link that took them to a new page to do so. That might have been easier on the backend but again, I wanted to learn. It was also more user friendly. Since I had routes for lists nested under users, doing this took a considerable effort. Once I figured it out it seemed so clear and setting up custom forms in other places was pretty easy. It really clicked that manipulating the links or forms html gave me what I needed in the params and sent it to the controller action I wanted to put the logic in. I had to carefully build the forms to get them to route to the correct controller action, and in the action I had to handle the nested resources and associations.
To Add a Book to a list:
Image for post
The form to add a book to a list in the view — books/show.html.erb.

I use form_for @books in the form and with Rails magic it understands that it’s not creating a new book, so it sends this to the books#update action in the controller. I added a collection_select for all of the user’s lists. Now I can grab the book id from the params and the list id will be in params[:books][:lists]. I also authorize the user to add a book to the list, run checks for whether the book was added to the list already and add it to the list if not.
Image for post

In the books controller
I defined helper methods to see if the book is already on the list and to add a book to a list in the model:
Image for post

In book.rb
The helper method in the books controller displays an error message and redirects if the book is already on the list, and it displays a success notice and adds the book to the list the user chose if not.
Image for post
In the books controller
You can see that I also had to white list the :lists in the book_params and defined a set_book method to DRY up the controller.

**Removing a Book From a List**
The other side of this equation was that I had to allow users to remove books from their list without deleting the books entirely. This took some careful consideration. I decided ultimately to add to my list of nested resources. In addition to having reviews nested under books and lists nested under users, I now decided I wanted the books destroy action nested under lists.
In my list show page I render a table of the books on the list and each line has options to review the book or remove it. The remove link routes to ‘lists/:list_id/books/:book_id’
Image for post
The table to show the books on a list in the lists/show.html.erb
Image for post
The link to remove a book from a list.

Since books are nested under lists for the destroy action specifically I had to pass arguments for the list id and book id and specify the method. I ran rails routes to make sure I had the route name correct for this. You can see below that the list_book path will be routed to books#destroy. So I knew that was where I needed to handle the logic for deleting a book off of a list.
Image for post
After running rails routes

Below you can see that I have a conditional in my books#destroy to handle either removing a book off of a list or deleting a book entirely. It was fairly easy to remove a book from a list once I set up the link correctly. I had the book id and list_id from the params so it was as easy as @list.books.delete(@book).
Image for post
I could have also done BooksList.where(book_id: book.id, list_id: list.id).destroy all but I thought @list.books.delete(@book) was more readable. If you’d like to destroy the line in the join table you have to make sure you don’t have dependent :destroy on your associations or you’ll get a no such column :books_lists error.

**Implementing the Google Books API**
Ironically, google for how to incorporate the google books api was the first time I ever felt that google was failing me. I had already setup Google Omniauth for third party sign in, but for some reason using an API was harder for me. Their documentation is heavy to say the least, and for a newer developer I needed it to be more straightforward. Once again, I learned! I pieced together what I could and figured it out. I hope this helps someone who needs it because I wish I had something like this.
I only wanted to be able to search google books and create books in my database from the searches. I’m not going to walk you through getting your credentials because there is quite a lot of help out there for that part. I’ll start from where you have your API key.

1. Add the gems 'google-api-client', ‘dotenv-rails’ and ‘httparty’ to your Gemfile and run bundle. The gem httparty helps you process the HTTP response you will get from the google api.

2.  Create a file in your main directory called .env. First add the .env file to the end of your .gitignore so it isn’t pushed up to your version control for everyone to see. Then place your API key in your .env file just like this:
Image for post
Add .env to .gitignore file
Image for post
Add API key to your .env file

3. Create a model and class method to handle your Google search. I had seen someone create a model named google_api and I used this as well. You’ll need to write a method to handle your get request to the Google Books API. In their documentation they tell you that the BASE URL for a search will be “https://www.googleapis.com/books/v1/volumes?q=". They then explain where to put your API key and search terms in the URL when you make a request. Altogether the base url + search terms + options + API key is what you’ll pass as an argument into Httparty.get so you get back a parsed response in ruby objects like arrays or hashes. You store the response in a variable like “results” and iterate through it to grab the data you need. In the documentation Google does help by telling you the keys for the information you need. For example, the books come back to you in the “items” key. Iterate through the “items” and google books api docs tell you that a book’s info will be stored in a hash in the “volumeInfo” key. You can then iterate through the “items” at “volumeInfo” and grab things like “authors”, “description” and “categories”.
Image for post
Make sure to account for possible nil values in the response!

 4. The return value of my GoogleApi.search method above was an array of hashes. Each hash represented a book. In my books model I created a class method that iterates through that array of books hashes and uses mass assignment to find or create books from it.
Image for post
In book.rb

5. In your view you add the search form. This search form I built allows the user to search the PageTurners database, search by genre or search Google Books:
Image for post
In books/index.html.erb

6. The form is submitted to my books#index action. If the user searches google there will be search terms stored in params[:query]. These are the search terms that I pass to the Book.create_book_from_google(params[:query]) method.
Image for post
Books#index

7. Remember that GoogleApi.search(params[:query]) is called inside of Book.create_book_from_google(params[:query]), which gets the response and creates an array of book hashes for us to create books from. The books we create are stored in the @books variable and we can now display them in the view. Pagy is a pagination gem that much faster and more lightweight(in terms of memory usage) than the other gems out there. This tutorial is very quick and easy and informative: https://gorails.com/episodes/pagination-with-pagy-gem
Image for post
books/index.html.erb

8. I use a table in a partial to display the book search results and Pagy to limit the amount of books displayed on each page with the bootstrap extra for styling. Pagy is easy to implement and is known for it’s speed and low memory usage.
Resources

Just for your viewing pleasure and educational purposes.
Image for post
I lived on google for this project and I know that anything shared is helpful to the next person! If you have any questions please feel free to reach out. You can see it on github here: https://github.com/HMoreira2020/PageTurners
