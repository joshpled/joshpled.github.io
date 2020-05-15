---
layout: post
title:      "CLI PROJECT - SHELTERLUV"
date:       2020-05-13 22:05:24 -0400
permalink:  cli_project_blog_-_shelterluv
---


## PIBA FOUNDATION USES SHELTERLUV TO CONNECT DOGS TO THEIR FUREVER HOME... I WANT TO HELP 

### PIBA FOUNDATION & SHELTERLUV.COM:

The concept for the program was inspired by the PIBA Foundation. They are a non-profit organization based in St. Petersburg, Florida dedicated to rescuing dogs, pairing with fosters, and finding their furever home. 

Shelterluv.com offers a user friendly software to manage a rescue whether brick & mortar or foster based. It also includes a wonderful API to grab any information from the database of the rescue. That information can be used to make the lives of rescues much easier and more efficient. 

### THE APPLICATION

Being that this is the CLI project, I was immediately attracted to the possibility of helping the PIBA Foundation on their website. However, being an amateur programmer I wasn't exactly sure how this would help until I started the program. 

The essence of the program is to utilize the API of Shelterluv to grab the information from the PIBA Database and display in the terminal.
This is all done with three different options:

1. List of Dogs
2. Search for Dogs
3. List of Fosters

When I knew what I wanted to do with the API I had to find the endpoints. Thankfully the API offered two great endpoints for people and animals.

The list of dogs allows you to choose a dog and get all the major details. The search feature can let you search by sex, size, and availability. The list of fosters lets the user see all the fosters and get contact information. 

To start, I used the `bundle gem` because it created the basic file structure for the project. What's that old saying again? Work smarter, not harder? 

### BIGGEST CHALLENGES

First big challenge was the API KEY. I guess in hindsight I could have chosen an API that didn't require a key, but what's the fun in that?

I learned of the `dotenv` gem. A file named just ".env" with the API KEY set inside the file. Then utilizing the `request["x-api-key"] = ENV["KEY"]` where the key is required. Gem requirement: `require 'dotenv'` and `Dotenv.load`. Final step: create a `.gitignore` file and add the line `.env`. I did run into some issues after I realized that the .env file was still being pushed into the repo. I googled my way through a few commands such as `git rm -f .env` to remove the file. I still had questions about adding the file back, but I left it as is because it didn't show up on the repo! I call that a success. 

Another big challenge was the CLI loop for user input. Especially because user input would be required after any helper method and the input variable was constantly changing. I'll display the while loop below:

```
## Loop for input.
    while input != "exit"
		if input.to_i == 1 or input == 'list'
		    ## Display list of all current dogs in the PIBA Database.
		    puts "\n" + b.asciify('LIST').colorize(:yellow)
		    print_dogs_by_name(Dog.all)
		    input = gets.strip.downcase.to_i
		    if input > 0
		    chosen_dog(input)
		    end 
		elsif input.to_i == 2 or input == 'search'
		    ## Search for a dog based on a criteria
		    puts "\n" + b.asciify('SEARCH').colorize(:yellow)
		    puts "\nYou can search based on the following criteria:"
		    puts "\n1. Size"
		    puts "2. Sex"
		    puts "3. Availability"
		    print "\nOption: " 
		    input = gets.strip.downcase
		    case input.to_i
		    when 1
		        ## Uses a helper method below to display results.
		        dogs_by_size
		        print "\nOption: "
		        input = gets.strip.downcase.to_i
		        dog_id = Search.results[input.to_i-1].internal_id
		        dog_details(dog_id)
		    when 2
		        ## Uses a helper method below to display results.
		        dogs_by_sex
		        print "\nOption: "
		        input = gets.strip.downcase.to_i
		        dog_id = Search.sex_results[input.to_i-1].internal_id
		        dog_details(dog_id)
		    when 3
		        ## No helper method needed. 
		        Search.search_by_availability
		    else
		     prompt_user
		    end 
		elsif input.to_i == 3 or input == 'fosters'
		    ## List of all fosters in the system. 
		    puts "\n" + b.asciify('FOSTERS').colorize(:yellow)
		    Person.all.each.with_index(1) do |person, index|
		        puts "#{index}. #{person.first_name} #{person.last_name}"
		        end 
		    puts "\n\nSome fosters are also staff."
		    print "\nOption: " 
		    input = gets.strip.downcase.to_i
		    input > 0 ? chosen_foster(input) : "Invalid Input"
		elsif input == "home"
		    prompt_user
		else
		    puts "\nInvalid input"
		    puts "\nIf choosing from a numbered list, your input must be a number."
		    puts 
		end
  puts "\nTYPE: \n'list' for list of dogs, \n'fosters' for list of fosters, \n'search' for search menu, \n'home' for main menu,  \nor 'exit' to close program"
            print "\nMenu Options: "
            input = gets.strip.downcase
        end 
        ## IF 'exit' is entered, the following will show and close the app. 
        puts "\n" + b.asciify('PIBA Foundation').colorize(:yellow)
        puts "\nThe PIBA Foundation is committed to assisting and advocating for the well-being of individuals and animals through disaster relief, community engagement, and environmental quality."
        puts "\nThe PIBA Foundation is a 501(c)(3) organization. Contributions to The PIBA Foundation are tax-deductible to the extent allowed by law. The tax identification number is 84-2979389."
        puts "\nThank You for checking in!"
        puts
    end 
```
Since I'm currently writing this before the refacoring process, note that the loop is simple in it's design. A while loop that waits for `exit` command. The complexity came when the input was requested at the wrong time. Since input was constantly being called for every menu, it was easier to change every line that `print "\nOption: "` to something unique. Then running the possible scenarios and taking note of all the breaks. It was a feat worth the time put into it as I learned TONS about input and how to handle integers and string inputs. I also learned that menu choosing could be refactored to something much more elegant but I wanted to showcase my current knowledge before spending any more time on the loop.

One of the absolute greatest discoveries was the challenge of understanding the error: `undefined method for nil:NilClass`
It has so many possibilities but ever since running into it more times than I care to relive, I learned many ways to debug it. 
The easiest was "undefined method" of course if the method isn't created... easy enough.
 
`undefined method '[]' for nil:NilClass (NoMethodError)`
This particular one had me stumped for a while. I had to track it down in pry from the CLI class all the way back to the root. It took a long time, but I finally found the culprit. It wasn't necessarily a method, but rather that while creating the objects (dog or person) if one the values it was looking for was nil, this error would show up! So I had to create if statements to account for the nil. 

This may seem like a simple error and trivial to figure out, but errors are the bane of any programmers existence. Things don't always workout exactly as expected and each error is a new Google journey into the abyss. Understanding even one of those errors will hopefully help me in the future. 


### Wonderful Discoveries 

I discovered so many great gems for Ruby through this journey.
Possibly my favorite is Artii. For this CLI project it really added a much needed flair to titles.
Best part about this gem is the simplicity of setup:

```
a = Artii::Base.new
b = Artii::Base.new :font => 'straight'
```
 
 Then I combined the Colorize gem to add color to the titles:
 
 ```
  puts "\n" + a.asciify('PIBA').colorize(:light_yellow)
  puts "\n" + "#{b.asciify('powered by')} Shelter Luv".colorize(:yellow)
 ```
 
I also realized that I understand WAY more than I initially thought. I learned the difference between many different iterators. Truly grasped the concept of scope and self. I learned how important refactoring is and how much I prefer it. Even for myself, messy code stresses me out. 

### FINAL THOUGHTS

The best part of being left to create the application on (mostly) your own is the amount of times I googled and learned something new. No matter how badly I didn't understand something, I needed this application to work! 

One of my favorite quotes is by Albert Einstein:

> It's not that I'm so smart, it's just that I stay with problems longer.

With this project, I can honestly say the greatest lesson learned was to keep at it! 
If something doesn't make sense just keep looking, keep trying, keep reading, and eventually it will click like a light bulb and you will have accomplished something wonderful! 
