---
layout: post
title:      "Recipe Book — Final Flatiron Project"
date:       2021-01-20 18:26:06 +0000
permalink:  recipe_book_final_flatiron_project
---


An ode to the beauty of React Components!
Ilove to cook and over time I’ve identified some healthy favorites and unique creations that my family loves. I thought it would be nice to create my own Recipe Book, so I built an application to do just that. I used a Ruby on Rails Backend, and React Frontend with Redux and Redux-Thunk middleware.
Image for post
Image for post
Project Requirements
The code should be written in ES6 as much as possible
Use the create-react-app generator to start your project.
Follow the instructions on this repo to setup the generator: create-react-app
Your app should have one HTML page to render your react-redux application
There should be 5 stateless components
There should be 3 routes
The Application must make use of react-router and proper RESTful routing (should you choose to use react-router v3 please refer to the appropriate docs; docs for v4 can be found here)
Use Redux middleware to respond to and modify state change
Make use of async actions and redux-thunk middleware to send data to and receive data from a server
Your Rails API should handle the data persistence with a database. You should be using fetch() within your actions to GET and POST data from your API - do not use jQuery methods.
Your client-side application should handle the display of data with minimal data manipulation
React vs. Javascript applications
The most notable difference when we jumped from our Javascript SPA (Single Page Application) to React was the incredible clarity and organization that React provides. Javascript has no official rules or boundaries for how an app should split up the UI components and functionality. This understandably leads to long hours and headaches when developers are trying to work with such unpredictable webs of files.
My brain loves everything organized, orderly, efficient and clear. Anything disorganized makes me extremely uncomfortable! I was so relieved when we started working with React.
React is a library written in Javascript, but it sets clear rules about how the app must be structured and how data can flow through the app. It requires that the app is organized into components, each of which must hold all the code needed to display AND update the UI. Generic components can be created and reused easily throughout the app. The different types of components provide further structure and clarity.
Container components vs. Functional/Presentational Components
React components are organized further into Container components and presentational components.
Container components:
how things work, the logic
are stateful
typically written as class components
Render other components (the functional components)
Pass data and behavior to the presentational components
Functional or Presentational components:
how things look
render/display the data but do not alter it
stateless
My application is carefully organized in this way. I have two resources, the books and the recipes. They are structured into a hierarchy of Containers and Presentational Components:
BooksContainer.js > Books.js > Book.js
RecipesContainer.js > Recipes.js > Recipe.js
The containers are in a containers folder and the components are in a components folder. Using importing and exporting to connect all the components together and by passing data around as props, the app separates a ton of potentially overwhelming code into predictable files and file structure.
Image for post
Admire the containers and components folders. There is clear separation of concerns.
It’s so simple! And so predictable, which makes the readability and debugging and sharing of components so much easier.
Below I will show the books components and file structure, but the recipes followed an almost identical pattern even within the rendering of the html. It builds into a very nice application so quickly.
Image for post
The Books Container doesn’t render anything in the browser, it passes data and behavior to the other components and defines the client-side routes.
Image for post
Books Component is passed the books data from the Books Container and renders all the books in the browser
Image for post
This is what the Books presentational component renders. It knows which books to render because the Books container told it which ones :)
Image for post
Book component that renders one book. Using the route defined in Books Container and the books that Books Container passes it, it finds which book to render.
Image for post
The book presentational component renders the book
The book presentational component you see above renders the book, but it also renders the Recipes Container which in turn handles all the logic to display that book’s recipes (the table of contents) and the recipe you see in the screenshot below.
Image for post
The book component renders the Recipes Container.
The book component renders the Recipes Container and passes the book info to it in props. The Recipes Container then has the book data needed to define the Recipes routes and render both presentational components for the book’s Recipes and individual Recipe.
Finalizing the Final Project
Wrapping up this project felt great. I am proud of the results and the way it looks. There are several things I would like to add in the future. I would first add a user so I could have a sign up and sign in. It would also allow me to have users edit/delete their own books and recipes, and add other people’s recipes to their book if they’d like. It also feels like an app I would want to use to build my own recipe book and have it printed. That ultimately was the point of the app. I wanted it to feel like you were creating a real Recipe Book of your own favorites or recipes you’ve created as opposed to just browsing recipes online to cook.
WRITTEN BY

Moreiraheather



