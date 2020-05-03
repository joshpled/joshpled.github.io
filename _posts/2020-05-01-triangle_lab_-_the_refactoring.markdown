---
layout: post
title:      "Triangle Lab - The Refactoring"
date:       2020-05-01 13:00:00 -0400
permalink:  triangle_lab_-_the_refactoring
---

The OO Triangle Lab was a fascinating adventure into refactoring that I think you'll benefit from hearing. 

![](https://i.imgur.com/v1SgLKJ.jpg)

It is my personal opinion that refactoring is a skill as important as knowing how to write the code in the first place. 

The challenge in this lab was when three sizes where inputted via the initialize method, what kind of triangle would the sizes make?

After running the tests I realized it's not as easy as coding every type of triangle. 
They add the additional challenge of triangles with no size or negative sizes. 

![](https://i.imgur.com/SdVhTfz.png)

I'm not going to lie to you... I probably spoke to my rubber ducky more this time around than I had in a while. My initial code looked something like this (for the sake of this blog, we'll ignore the custom error required):

```
class Triangle

attr_accessor :side_a, :side_b, :side_c,:equilateral,:isosceles, :scalene
  # write code here
  def initialize(side_a,side_b,side_c)
    @side_a = side_a
    @side_b = side_b
    @side_c = side_c
  end

  def kind
    if @side_a + @side_b > @side_c && @side_a + @side_c > @side_b && @side_b + @side_c > @side_a && @side_a > 0 && @side_b > 0 && @side_c > 0
          return :equilateral if @side_a == @side_b && @side_b == @side_c
          return :isosceles if @side_a == @side_b && @side_b != @side_c || @side_b == @side_c && @side_a != @side_c || @side_a == @side_c && @side_b != @side_c
          return :scalene if @side_a != @side_b && @side_b != @side_c && @side_c != @side_a
    end
end
end 
```

In essence, it works. It will return the appropriate triangle. However, it is messy and doesn't account for illegal triangles.

My initial thought process was to create more returns for all of the illegal triangle possibilities which of course, was the purpose of this lab. 

However, that would make things worse. 

So the googling begins.

**How can I refactor this and account for the illegal and legal triangles without losing my mind?**

The journey wasn't simple, but it made me reconsider what I knew about implementing certain concepts I already knew. 

For one, the initialize method is fine (could be better... we'll get to that)

```
def initialize(side_a,side_b,side_c)
    @side_a = side_a
    @side_b = side_b
    @side_c = side_c
  end
```

The biggest problem is the comparison loop. It's too wordy and hard to read.
	
How can I make sure that I'm comparing the sides without having to guess which side is which... AN ARRAY! 

```
sides = [@side_a,@side_b,@side_c].sort
```
	 
 ![](https://i.imgur.com/frJEkIp.png)
 
So, re-writing the code with an array in mind

```
def kind
    sides = [@side_a,@side_b,@side_c].sort
          return :equilateral if @side_a == @side_b && @side_b == @side_c
          return :isosceles if @side_a == @side_b && @side_b != @side_c || @side_b == @side_c && @side_a != @side_c || @side_a == @side_c && @side_b != @side_c
          return :scalene if @side_a != @side_b && @side_b != @side_c && @side_c != @side_a
        end 
end
```

**SO MUCH BETTER.**

I didn't want to give up, I knew there had to be a better way. 

I got very lucky to stumble upon a possible array solution... if all the sizes are the same and you add `.uniq` to the array... there should only be a certain amount of numbers in the array... right?! 

That would mean:
* If all 3 sizes are the same, the array only has 1 number in it, that is an equilateral triangle.
* With the same logic, if 2 sides are the same, or if the array has 2 numbers, it would mean the triangle is isosceles.
* Finally, if all 3 sides are different, or if the array has 3 numbers, it would mean the triangle is scalene.

My rubber ducky got a headache too, but we figured something out...

```
  def kind
    sides = [@side_a,@side_b,@side_c].uniq
          return :equilateral if sides.length == 1
          return :isosceles if sides.length == 2
          return :scalene if sides.length == 3
        end
end
```

![](https://i.imgur.com/ObAtcx0.jpg)

Wonderful! 

What about the illegal triangles? ::insert face palm emoji here::

Well I can write an if statement to determine if the sides in the array are certain values... 

Here's the kicker, in order for this to work, we have to change a few things. 

That's when I discovered `*`  or the Splat operator!
[FreeCodeCamp: An introduction to Ruby’s Splat and double Splat operators](https://www.freecodecamp.org/news/rubys-splat-and-double-splat-operators-ceb753329a78/)

> The splat operator has almost endless uses!

It really does, but how can it work for me?
By the way, I had no idea. I saw someone use it in a method and I was determined to figure out how to use it for me. 

Then... SIMPLE! Refine the initialize method so regardless of the inputed arguments, the arguments would be converted to an array! 

```
def initialize(*args)
    @sides = *args.sort
  end
```

![](https://i.imgur.com/OuhNI42.jpg)

So now... the sides are a sorted array, saves me one more line of code in the method. 

Next is how to check this array for the illegal triangles? (I'll use the error method here as to exemplify what the code is doing): 

```
def kind
    raise TriangleError if @sides.any? {|s| s <= 0} or
      @sides[0]+ @sides[1] <= @sides[2]
			...
```

This will raise the error if any of the sides are less than or equal to 0 **OR** any of the sides are violating triangle inequality (In mathematics, the triangle inequality states that for any triangle, the sum of the lengths of any two sides must be greater than or equal to the length of the remaining side).

Last and final step: is there any way to refactor the cases for each triangle type?

Cases... case... CASE! 

```
case @sides.uniq.size
    when 1
      :equilateral
    when 2
      :isosceles
    else
      :scalene
    end
```

Put it all together! 

```
class Triangle

attr_accessor :side_a, :side_b, :side_c,:equilateral,:isosceles, :scalene
  
	def initialize(*args)
    @sides = *args.sort
  end

  def kind
    
		raise TriangleError if @sides.any? {|s| s <= 0} || @sides[0]+ @sides[1] <= @sides[2]
    
		case @sides.uniq.size
    when 1
      :equilateral
    when 2
      :isosceles
    else
      :scalene
    end
  end


class TriangleError < StandardError
     
		 def message
        "This is not a triangle!"
      end

end
end 
```

![](https://i.imgur.com/odu4WMc.jpg)

Final Thoughts: While I have to admit, this lab took me way too long,  I enjoyed learning that while code can work, it can always be written better (or different, depending on your perspective). 

The solution can be found by googling concepts you may not have thought about and making it so much easier for other programmers to understand your code and sometimes let you read your code much better as well. 

While I'll admit, I was lucky to find the splat operator online, as it is not an operator we were taught in the curriculum, it still took me a lot of googling before realizing what any of this means.

In the end, however, I learned an invaluable lesson in perseverance, patience, and the power of Google. 
