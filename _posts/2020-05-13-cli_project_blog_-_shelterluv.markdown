---
layout: post
title:      "CLI Project Blog - Shelterluv"
date:       2020-05-14 02:05:23 +0000
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
