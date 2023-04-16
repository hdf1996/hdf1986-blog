---
layout: "../../layouts/BlogPost.astro"
title: "Advent of code 2022 - Day 1"
description: "Lorem ipsum dolor sit amet"
pubDate: "Jul 08 2022"
heroImage: "/placeholder-hero.jpg"
labels: "algoritmos,advent-of-code"
---

Christmas is coming! <---- TODO: Componente de luces

Welcome to our very first post of our "Advent of Code" series! If it's your first time ~hearing~ reading about it, let me briefly tell you about Advent of Code.

Every year, every December, Advent of Code (AoC) https://adventofcode.com publishes their Advent calendar, it's a little bit different than normal calendars! It contains a list of 25 different programming puzzles, that each day are unlocked, starting on the 1st until 25th. 

Normally every day gets a little bit harder (And weekends are usually way harder), in the same line, each exercise includes 2 parts, and the second one gets unblocked once you resolved the first one. You can use any language to solve them, but in my case i'll use ruby whenever it's possible

I don't want to spoil any of the fun! So just let me tell you that we are going to be doing each exercise together, starting from Day 1 2022. Maybe we are fast enough to keep it up and have the entire 2022 calendar ready before 2023 christmas season arrives (Dreaming is still free, right?)

I'll be posting my solutions in the blog, and also at https://github.com/hdf1986/advent-of-code along with my input

# Day 1: Calorie Counting
## Part 1

After carefully reading the description of day one https://adventofcode.com/2022/day/1. We find ourselves needing to take a look at each Elves and see how much calories each one is carrying. Our goal for this part, is to identify which Elve is carrying the most calories in his bag, and how many calories are there

For this, we are provided with the list of calories for each Elve bag (our input file is bigger, so let's just use this as an example):

```
1000
2000
3000

4000

5000
6000

7000
8000
9000

10000
```

Each empty line represents the separation between each Elve bag, so if we summarized by bag, it would look like this\

```
bag 1: 1000 + 2000 + 3000 = 6000

bag 2: 4000 = 4000

bag 3: 5000 + 6000 = 11000  

bag 4: 7000 + 8000 + 9000 = 24000 <---- Our winner!

bag 5: 10000 = 10000
```

Looks like bag 4 (24000 calories) is our winner. Now that we've confirmed the bag with the most calories, and that we understand how this input it's structured, let's try to solve it ourselves


Let's start by reading the input from a file. This will allow us to read the input from a `input.txt` file (it should be located at the same folder than your ruby code). You'll see me doing this often in our AoC exercises, so don't be surprised if next time I just omit this part

```ruby
input = File.read(File.join(__dir__, 'input.txt'))
```

Now that we've the file as an string, we can do all kind of dirty tricks to group each bag, but there's a simple one! every time you see 2 linebreaks together, you have a group delimitation! And since \n represents a linebreak, we can split this string using \n twice (\n\n)

Let's update our input to adopt this idea

```ruby
input = File.read(File.join(__dir__, 'input.txt')).split("\n\n")
# input now should be something like ['1000\n2000\n3000', ...]
```

We are closer, we have the bags, but we still have to sum each number, but wait... `'1000\n2000\n3000'` is just an string! We cannot sum an string just like this. It still requires some treatment before, we need to separate each bag spliting by simple \n and also convert it to something we can sum, like a number

```ruby
input = File.read(File.join(__dir__, 'input.txt')).split("\n\n")
# ['1000\n2000\n3000', ...]

food = input.map { |elf| elf.split("\n") } 
# [['1000', '2000', '3000'], ...]
```

Almost there! The numbers are now separated into individual strings, but our goal was to move it to a number, let's assume they are integers (normally, in AoC numbers are integers) and cast those ugly strings into integers

```ruby
input = File.read(File.join(__dir__, 'input.txt')).split("\n\n")
# ['1000\n2000\n3000', ...]

food = input.map { |elf| elf.split("\n").map(&:to_i) } 
# [[1000, 2000, 3000], ...]
```

We make use of `.map(&:to_i)`. Each string (and some other objects) do have a `to_i` method that you can invoke for casting. In this case we also make use of the special syntax `&:to_i` which will tell `map` to invoke the `to_i` method of the element of each iteration.

You can consider it the equivalent of `.map { |element| element.to_i }`. I would risk to say that you'll often see the former `&:` syntax, since it's usually easier to write and read once you get often to it

Now that everything is ready, let's see if we can make the sums for each bag. Ruby has a very convenient [`Enumerable#sum`](https://ruby-doc.org/3.2.2/Enumerable.html#method-i-sum) method, and since arrays are just enumerables, we can use it!

```ruby
input = File.read(File.join(__dir__, 'input.txt')).split("\n\n")
# ['1000\n2000\n3000', ...]

food = input.map { |elf| elf.split("\n").map(&:to_i).sum } 
# [6000, 4000]
```

Finally, we have each bag calories, now let's pick up the greatest number using [`Enumerable#max`](https://ruby-doc.org/3.2.2/Enumerable.html#method-i-max). Don't forget to print it so you can see the actual number!

```ruby
input = File.read(File.join(__dir__, 'input.txt')).split("\n\n")

food = input.map { |elf| elf.split("\n").map(&:to_i).sum } 

pp food.max # 24000
```

Try it with the example input, and if it works, proceed to use the input provided by AoC, which should give you the real answer

## Part 2

We now need to find the top 3 bags, and sum them. We already had the list of calories per bag, we just need to pick the top 3 and generate an array from them, so we can easilly sum them with [`Enumerable#sum`](https://ruby-doc.org/3.2.2/Enumerable.html#method-i-sum)

Very conviniently, [`Enumerable#max`](https://ruby-doc.org/3.2.2/Enumerable.html#method-i-max) accepts an optional argument, which if given, it'll make the method return a new array containing the `N` amount of maximum elements of the array... That's exactly what we need!

Let's update our code for that

```ruby
input = File.read(File.join(__dir__, 'input.txt')).split("\n\n")

food = input.map { |elf| elf.split("\n").map(&:to_i).sum } 

pp food.max(3).sum # 45000
```

Again, try it with the example input, and if it works, use your personal input

### Summary
That's it for Day 1! In our case, we were able to use a couple of convenient methods from the `Enumerable` class. It almost felt like cheating!

Keep these methods in your toolset, we are very likely going to use them for the upcoming AoC days (and in your day-to-day coding why not). Feel free to take a look at the doc, you may find out other useful methods
- [`Enumerable#sum`](https://ruby-doc.org/3.2.2/Enumerable.html#method-i-sum)
- [`Enumerable#max`](https://ruby-doc.org/3.2.2/Enumerable.html#method-i-max)

See you on Day 2!