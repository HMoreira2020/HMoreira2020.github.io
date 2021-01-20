---
layout: post
title:      "Prep Deck — Javascript SPA to help you prepare for coding interviews"
date:       2021-01-20 13:25:26 -0500
permalink:  prep_deck_javascript_spa_to_help_you_prepare_for_coding_interviews
---

For Flatiron’s Javascript project we are required to build a Single Page Application using Ruby on Rails for the backend and Javascript, HTML and CSS for the frontend. I decided that a sort of flashcard application comprised of Javascript topics would be useful. It’s not a quiz, but a deck of questions to test your knowledge and prepare you for coding interviews. If you get a question that you don’t know the answer to, you can add that question to your own deck to come back to later. The questions help to remind you that you need more practice in that area. Once you feel confident that you know enough, you can remove that question from your deck.
Image for post
Image for post

**Communicating with the Server — AJAX, Fetch() and JSON**
In order to create an SPA you need to be able to make requests to the server and handle the response without reloading the page. We do this by using the AJAX technique, short for "asynchronous JavaScript and XML, though now the data in the response is mostly sent back in JSON, short for JavaScript Object Notation. The process happens in 3 parts:
Using fetch() to communicate with my server I get/send data to my backend API. In the fetch is where I structure the request and can define what the parameters will be by configuring the body of the request.
In the backend controllers I handle the request, including persisting my data to my database and structuring the response to render in JSON, short for JavaScript Object Notation.

On the frontend I handle the JSON response in the second .then. This is where I would create a javascript object and render it in HTML.

For adding a question to a users deck in my application, this is what the process looked like:
Image for post

The fetch request, where you structure your REQUEST and handle your response in the second .then.
Image for post

The update action in the Decks Controller in the backend. Where you handle the request and structure your RESPONSE.
It’s useful to understand that how you structure the body determine what your params will be.
```
const deckId = event.target.dataset.id
const questionId = questionDiv.dataset["id"]
//this in the bodyin configObj in your fetch...
body: JSON.stringify({deck_id: deckId, question_id: questionId}),
//becomes the params that you use in your controller...

3.2.6.1 :001 > params
=> #<Parameters {"deck_id"=>"9", "question_id"=>"52", "controller"=>"decks", "action"=>"update", "id"=>"9", "deck"=>{}} permitted: false>
```

**Refactoring with OOJS**
After everything was working in my app I went about refactoring. The first thing I did was build classes for deck, user and question. This greatly reduced the code in the second .then of my fetches where I was handling the JSON response from my backend and rendering the data in HTML. Before I created JS classes my fetch for adding a question to a user’s deck looked like this:

```
fetch(BACKEND_URL + `/decks/${event.target.dataset.id}`, configObj)
.then(response => response.json())
.then(json => {
       let usersDeck = Deck.findById(json.id)
       let deckQuestions = usersDeck.questions ques.id)
       let ids = deckQuestions.map(ques => ques.id)      
       function filterById(id){return Question.findById(id)}
       let userQuestions = ids.map(filterById)      
       startDeck(userQuestions)
  })
}
```

In order to DRY up the code in the second .then I knew I needed to create the deck class. I wanted to be able to create a new Deck object and just call deck.seeDeck, or something of the sort. I created the decks class and wrote instance methods I would need to call on the main deck and on the users deck.
Image for post

I then refactored the fetch() :
Image for post

From several lines of code to 2. Improvement!

I continued refactoring by changing my function declarations to function expressions while keeping hoisting in mind. I also used arrow functions that are new with ES6 because they use less code, especially when iterating because if you can keep the work to one line you don’t even need to write ‘return’. Using arrow functions can change what ‘this’ refers to in your function so that was also something to keep in mind.

**The Three Pillars**
It was difficult to start this project. I felt less prepared than in previous projects, maybe because in this module there weren’t big labs that had us practice how all the lessons in the module tie together like there were in previous labs. I had to figure a lot of this out on my own, and I think that’s a good thing. Every challenge shows me that it’s just a matter of some hard work and determination and GOOGLE. In time, I’ll figure it out and get the hang of it. I am feeling much more confident in the Three Pillars that we were meant to address in this project:
1) Manipulating the DOM
2) Javascript eventing
3) Communicating with the server.

**Final Thoughts**
I would like to share the two most useful things I learned in this project.
Feedback. Get all the feedback you can and constantly. Place debuggers and byebugs, and hop into the rails console. Keep testing it out and getting feedback as often as you can about what your variables are in this function or that, what are the parent and sibling Elements of your html, what can you do with you associations. Put a debugger in and then move it down one line at a time until you get what is happening. It’s incredibly helpful. You can google all you want for how to do something, but ultimately if you want to really understand your code this is better.

Sometimes if you try to declare a variable in the console in your browser using ‘let’ you will get undefined, even though you are doing it correctly. Try using ‘var’ but then use the ‘let’ in your code. This goes back to the feedback thing because testing things out in the console when you are in a debugger is how you figure things out. I really stumbled at times until my technical lead coach enlightened me with this little tidbit! This might seem small but who knows where else you will get this info. Spread the word!

Please take some time to check this out. Hopefully it will help you prepare for project assessments or interviews. Thank you for reading!
Github: https://github.com/HMoreira2020/Prep-Deck
Contact:
Heather Moreira
E | moreiraheather@gmail.com
