---
layout: post
title:      "First Portfolio Project — A CLI Data Gem"
date:       2021-01-20 18:16:54 +0000
permalink:  first_portfolio_project_a_cli_data_gem
---


It’s been about a month since I started Flatiron’s Part-time Online Software Engineering program and it’s already time for the first portfolio project. I didn’t really think it was possible to do this program while having my 2 and 4 year old at home with me due to the Coronavirus social-distancing and quarantine restrictions. But I’m doing it! So far, so good.

**The project’s requirements are as follows:**
Provide a CLI
Your CLI application must access data from a webpade to demonstrate scraping or the use of an API
The data provided must go one level deep (For example, the user sees a list of information, makes a choice, and then sees more details)
**
Step One: What would my project be on?**
My first idea was to scrape a yoga studio website for class style info and schedule. Unfortunately COVID-19 has shut down most yoga studios and they have converted to online classes only. When I looked at my favorite studio for class styles, there were only “Live Stream Class via Zoom” classes listed. I decided to find a new idea.
I looked for a surf retreat website because my husband and I miss surfing and are dreaming of a retreat! We moved from California to Arizona so I could complete this program. The idea was that we would save money in rent in order to pay for the tuition payments and childcare I needed so I could get the work done. We didn’t see coronavirus coming. Now, without my kids in school, I high-five my husband around 5pm to hand off the kids and I work until around 11pm. I can’t think of anything more glorious than a surf retreat at the moment!

**Step Two: Project Planning**
With an idea in mind, I followed Beth’s advice and checked to see if the website was scrapable with the Scraper Checker Tool. I also followed the project planning resources and built a flow chart to help me decide what Ruby classes I would need and what they would need to do.
Image for post
I watched both Beth and Avi’s videos that were recommended in the [BONUS] CLI Project Planning Resources lesson. Both videos detail how to setup your gem using Bundler.

**Step three: Creating my skeleton app and repo on Github**
By watching the cli videos I was able create a project skeleton by opening Sandbox and using the bundle gem command. I had to do this twice, because the first time I ran bundle gem swell-retreats-cli. Using the ‘ - ‘ instead of ‘ _ ‘ created problems, so I went back and recreated my gem by running bundle gem swell_retreats. Just to reiterate, use underscores when creating your GEM_NAME.
I then pushed my repo up to Github and ran cd swell_retreats to cd into the directory. I edited the README.me file according to the project requirements, and added dependencies like nokogiri, open-uri and pry in the gemspec file. I ran bundle install, which makes sure the gems I need are there in development, staging and production. I made sure to make Git commit messages early and often. Also, I’ll be doing a blog post on Git commit messages. It’s a science that I have not yet mastered!
None of this happened without a few hiccups, but once I had it all setup I was ready to build my classes.
**
Step four: Creating my classes and their methods**
I knew from creating a flow chart that I needed a Scraper class, a Retreat class, and a CLI class.
I stubbed out the methods in each class, mostly building the CLI class to see it working with basic methods. I then tackled the Scraper class by using binding.pry and the Scraper Check Tool in replit that I mentioned above. I built Scraper.scrape_homepage to scrape the homepage for the list of upcoming retreats. The return value of this method was an array of hashes, each representing a retreat on the list with key/value pairs that represented the retreat’s name and date. I then built Scraper.scrape_retreat_details to get more detail on a retreat. The return value of this method was a hash representing data for one retreat. Once I had the Scraper class built I worked on the Retreat class to build retreat objects using the data I scraped.

In the Retreat class I knew I needed the attr_accessors to match all of the keys I created in the Scraper class so that I could make each key/value pair an object attribute. I used the .send() method to do so. We learned that a method can take a hash as an argument in one of the recent lessons, so I decided to use this structure. The Retreat.create_from_collection method takes in the retreats_array return value of the Scraper.scrape_homepage method, iterates over the array and calls Retreat.new on each hash, which is then sent to the initialize method that takes in a hash as an argument, iterates over each key/value pair and calls self.send((“#{key}=”), value). I love this method. It’s beautiful. I use it for the #add_retreat_details method that takes in the hash of additional details for a retreat and, with the help of another method in the cli, adds those details as attributes to the exisiting retreat object.
Image for post

I then went back to the CLI class to finish. I have to say, this part was fun. I liked deciding how the user will interact with the program. I liked tidying things up. It makes all the hard work worth it. The application starts with a call method that puts out the greeting and calls the other methods needed to make the retreat objects come to life, list them out for the user, and ask for/handle their input.

I had quite a lot of code in my #retreat_menu method and decided to take sections out to create a separate #show_details method that handles how the additional retreat details are presented. I also had originally called #add_details_to_retreat(retreat) in the #call method, but this slowed my program down significantly. It meant that at the start of my program, I was scraping the homepage and scraping all the individual retreat pages ahead of the user input. Instead I put it in the CLI#retreat_menu method which meant I wasn’t scraping the details of a retreats’ page unless the user selected to see it.

Image for post

I see the importance of separating responsibilities and strive to do more of it in the future. I am already looking forward to the challenge of refactoring. I can see breaking down the steps of the #retreat_menu method even further. After a short break, of course.

When working on the CLI class I added another dependency in my gemspec file, colorize, so that I could change the color of certain sections to make the output more readable.

Finally, in my executable file(which I gave executable permissions with chmod +x) I added a ‘she-bang’ , required my environment.rb file, created an instance of my CLI class SwellRetreats::CLI.new.call. 
To run my program, I run ./bin/swell_retreats in the terminal. If you’d like to see how it works, clone down the repo from Github and try it out! CLI Data Gem Swell_Retreats

**UPDATE:**
I decided to refactor my CLI class. The goal was to make sure each method had only one responsibility, so I created a #greeting method and a #menu_options method.

I also refactored the #show_details method to handle a new conditional. Previously, if the user entered “1” or “2” to see more information about the 1st or 2nd retreat on my list, the “location” section was blank. This is because the websites for these two retreats did not include a location heading and text as in all the other retreat sites on the list. To avoid giving the user a blank section, I used the ternary operator and placed it in CLI#show_details. If retreat.location == nil, the user sees “See website for location information”. 
See below:
retreat.location.nil? ? (puts "See website for location information."):(puts "#{retreat.location}")`
