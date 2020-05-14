---
layout: post
title:      "CLI Project Blog - Shelterluv"
date:       2020-05-13 22:05:24 -0400
permalink:  cli_project_blog_-_shelterluv
---

## Background: 

PIBA Foundation is a non-profit organization dedicated to homeless animals and rehoming animals. It's a young company with many needs and as a cause I believe in I decided to dedicated my project to them. 

Shelterluv.com is "The easy-to-use, modern shelter software you've been looking for." It has loads of features but best of all a fully functional API to use. 

My CLI project will utilize Shelterluv API with PIBA Database.

## Concept and Planning:

Once I realized what I wanted to do, the planning stages included understanding how the API handled the information. There were four endpoints to consider - I decided to use two. 

```
url = URI("https://www.shelterluv.com/api/v1/animals")
## and
url = URI("https://www.shelterluv.com/api/v1/people")
```

One would be used to GET all animals and their information. The other would be to GET all people that have been entered in the database. The PIBA Foundation used this feature for foster volunteers. Thus that's what this endpoint would be used for. 

Here's the plan: 
1. Get all animal details and create an array with many objects. 
2. Get all the people and create and array with many objects. 
3. Doing this in two easy steps will ensure all the information is used before being called within the CLI. 
4. I would need several classes: API, Person, Dog, and CLI.
5. I want to include a Search class for search based on sex, size, and availability.
6. Lastly, the CLI class should allow easy navigation through options without overcomplicating information shown. 

Before execution, the final piece was an API key. Once I was given access, the process began.

## Initial Stages: 

The API was easy enough (the following code is before refactoring):

```

class Api

        def self.get_by_name
				
            url = URI("https://www.shelterluv.com/api/v1/animals")
        
            https = Net::HTTP.new(url.host, url.port);
            https.use_ssl = true
        
            request = Net::HTTP::Get.new(url)
            request["x-api-key"] = ENV["KEY"]
        
            response = https.request(request)
            animals = JSON.parse(response.body)
            dogs = animals['animals']
     
            dogs.collect do |animal|
                name = animal["Name"]
                internal_id = animal["Internal-ID"]
                sex = animal["Sex"]
                status = animal["Status"]
                in_foster = animal["InFoster"].to_s
                size = animal["Size"]
                photos = animal["Photos"]
                breed = animal["Breed"]
                color = animal["Color"]
                age = animal["Age"]
                adoption_fee = animal["AdoptionFeeGroup"]
                person = animal["AssociatedPerson"]
                Dog.new(name: name, internal_id: internal_id, sex: sex, status: status, in_foster: in_foster, size: size, photos: photos, breed: breed, color: color, age: age, adoption_fee: adoption_fee, person: person)
            end
            
        
        end 

        def self.get_people
            url = URI("https://www.shelterluv.com/api/v1/people")
        
            https = Net::HTTP.new(url.host, url.port);
            https.use_ssl = true
        
            request = Net::HTTP::Get.new(url)
            request["x-api-key"] = ENV["KEY"]
        
            response = https.request(request)
            people_array = JSON.parse(response.body)
            people = people_array["people"]
            people.collect do |person|
                first_name = person["Firstname"]
                last_name = person["Lastname"]
                email = person["Email"]
                internal_id = person["Internal-ID"]
                Person.new(first_name: first_name, last_name: last_name, email: email, internal_id: internal_id)
            end
        end 

end
    
```

My biggest challenge in the API class 

```
adoption_fee = animal["AdoptionFeeGroup"]
person = animal["AssociatedPerson"]
```

For some unknown reason to a beginner programmer like myself, if I didn't include `dogs = animals['animals']` the entire code would break because the `["AdoptionFeeGroup"]` and `["AssociatedPerson"]` would not show up when called. 
it also allowed easier access to the information since the API looked like so:

```
{
    "success": 1,
    "animals": [
        {
            "ID": "1",
            "Internal-ID": "21970677",
            "Name": "Sheldon",
            "LitterGroupId": null,
            "Type": "Dog",
            "CurrentLocation": null,
            "Sex": "Male",
            "Status": "Available Foster",
            "InFoster": true,
            "AssociatedPerson": {
                "FirstName": "Marissa",
                "LastName": "Nelson",
                "OutDateUnixTime": "1587309169",
                "RelationshipType": "foster"
            },
            "CurrentWeightPounds": "",
            "Size": "Medium (20-59)",
            "Altered": "Yes",
            "DOBUnixTime": 1418948388,
            "Age": 64,
            "CoverPhoto": "https://www.shelterluv.com/sites/default/files/animal_pics/24820/2020/04/24/08/20200424083059.png",
            "Photos": [
                "https://www.shelterluv.com/sites/default/files/animal_pics/24820/2020/04/24/08/20200424083059.png"
            ],
            "Videos": [],
            "Breed": "Terrier, American Pit Bull/Mixed Breed (Large)",
            "Color": "Tan/None",
            "Pattern": "None",
            "AdoptionFeeGroup": {
                "Id": "231799",
                "Name": "Adult Dog",
                "Price": "200",
                "Discount": 0,
                "Tax": 0
            },
            "Description": "",
            "PreviousIds": [],
            "Microchips": [
                {
                    "Id": "981020031518027",
                    "Issuer": "Petlink / Datamars",
                    "ImplantUnixTime": ""
                }
            ],
            "LastIntakeUnixTime": "1579047588",
            "Attributes": [],
            "LastUpdatedUnixTime": "1588529866"
        },
				...
```

When the API is called it also creates all the dog objects. Within the Dog class and Person class there is an @@all array class variable to store all the dogs and people.

After all the Dog and Person objects were created it was time to make the CLI!

## CLI:

The CLI file looks a bit confusing at first, but I also added notes per section to help easily identify the code. 

I decided on a while loop to identify whether or not the input was ever changed to exit. 

```
 while input != "exit"
```

```
Choose from the following options:

1. List of Dogs
2. Search Dogs
3. Fosters

Option: 
```

First code I wanted to work on was the list of dogs. 

The code is quite simple. It takes the array of dog objects, iterates through them, and `puts` each dog! 

```
def print_dogs_by_name(list)
puts "\nThese are the dogs currently in the PIBA Foundation Database:"
        puts 
        list.each.with_index(1) do |dog, index|
        puts "#{index}. #{dog.name}"
        end 
        puts "\nWhich dog would you like more information on?"
        print "\nOption: " 
end 
```

`.each.with_index(1)` -- will iterate through the array at index 0 but starts the list at 1. 

When a dog is chosen the details will be displayed accordingly. 

However, I wanted to ensure accuracy when selecting a dog, I decided to sort the `@@all` array within the dog class by` internal_id` (same with the person class). Since internal_id attribute was unique to every dog and person, it would keep the list organized for recalling. 

```
def self.all
        @@all.sort_by{ |word| word.internal_id }
    end
```

Choosing a dog is processed with this helper method:

```
def chosen_dog(input)
        dog_id = Dog.all[input.to_i-1].internal_id
        dog_details(dog_id)
    end 
```

```
def dog_details(dog_id)
        dog = Dog.all.detect {|dog| dog.internal_id == dog_id.to_s}
        
        puts "\nSay Hello To: #{dog.name.colorize(:light_yellow)}!"
        if dog.age == 12
            puts "\nAge: #{dog.ag / 12} year old"
        elsif dog.age < 12
            puts "\nAge: #{dog.age % 12} months old"
        elsif dog.age > 12 && dog.age < 14
            puts "\nAge: #{dog.age / 12} year & #{dog.age % 12} months old"
        else 
            puts "\nAge: #{dog.age / 12} years & #{dog.age % 12} months old"
        end 
        puts "Size: #{dog.size}"
        puts "Breed: #{dog.breed}"
        puts "Color: #{dog.color}"
        puts "Status: #{dog.status.include?("Available") ? "Available for Adoption" : "Not Available, Currently with Foster"}"
        puts "With A Foster? #{dog.in_foster ? "Yes" : "No"}"
        if dog.in_foster && dog.person
            puts "Foster: #{dog.person["FirstName"]} #{dog.person["LastName"]}"
        else
            puts "No information for Foster"
        end 
        if dog.adoption_fee
            puts "Adoption Fee: $#{dog.adoption_fee["Price"]}"
        else
            puts "Adoption Fee: N/A"
        end 
        puts "Photos: #{dog.photos.join}"
        puts 

    end 
```

I wanted the details to account for many possibilities: age variations, foster name, and adoption fee. I was also getting `undefined method for nil:NilClass` error triggered by a detail returned `nil`. 

During the refactor process I hope to join both methods together. 

```
puts "\nTYPE: \n'list' for list of dogs, \n'fosters' for list of fosters, \n'search' for search menu, \n'home' for main menu,  \nor 'exit' to close program"
            print "\nMenu Options: "
            input = gets.strip.downcase
```


