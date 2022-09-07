---
layout: single
title: '2017-09-01, python args kwargs functions'

---


# Python functions, *args, **kwargs

Why use args and kwargs with python? A little while ago I did not understand kwargs, I find that to write about something helps build an understanding of a topic and it allows the information to be digested, much like rubber ducking. Also, because learning is fun and filling in knowledge gaps is better, here we go.  [rubberduckdebugging](https://rubberduckdebugging.com/ "rubberduckdebugging") 

I am going to borrow a number of examples from the digital ocean tutorial about this because the way that they explained it really helped me to understand it. [how-to-use-args-and-kwargs-in-python-3 ](https://www.digitalocean.com/community/tutorials/how-to-use-args-and-kwargs-in-python-3  "how-to-use-args-and-kwargs-in-python-3") 
Variables in functions with python; in order to pass data to a function variables are setup and passed in a specific order. The example function is called with a string and a number, simple enough.

```
def profile_info(username, followers):
 print(“Username: “ + username)
 print(“Followers: “ + str(followers))
profile_info("chris",15)
>>Username: chris
>>Followers: 15
```

Problem: This approach and function call mandates that the variables are passed in a specific order and all of the variables must always be passed. 
Solution: Add some default values which will let us pass none or some of the values without incurring an error.

```
def profile_info(username="name", followers=0):
 print(“Username: “ + username)
 print(“Followers: “ + str(followers))
profile_info()
profile_info(username="thor")
>>Username: name
>>Followers: 0
>>Username: thor
>>Followers: 0
```

Benefit: Order doesn’t matter when passing via keyword. If we want to mix and match the order of the variables we can pass variables with the keyword attached.

```
def profile_info(username="name", followers=0):
 print(“Username: “ + username)
 print(“Followers: “ + str(followers))
profile_info(followers=6, username="thor")
>>Username: thor
>>Followers: 6
```

Great, now what about many of variables ... Using *args we can pass an undisclosed number of variables as a tuple. Whats the difference between a tuple and a list? In python a tuple is immutable, where as a list is mutable.
Some objects are mutable, meaning they can be altered. Others are immutable; they cannot be changed but rather return new objects when attempting to update. 
[python-objects-mutable-vs-immutable](https://codehabitude.com/2013/12/24/python-objects-mutable-vs-immutable/ "python-objects-mutable-vs-immutable") 
Why does mutability matter?

```
string_build = ""
for data in container:
    string_build += str(data)
```

Simple enough but because a string is an immutable object, a new object is created on every concat, throwing away the old one and as a result using more memory.
Solutions:

```
builder_list = []
for data in container:
    builder_list.append(str(data))
“”.join(builder_list)
 
### Another way is to use a list comprehension
“”.join([str(data) for data in container])
 
### or use the map function
“”.join(map(str, container))
```

Another gotcha is using a list as a parameter for a function, otherwise known as the poor mans args.

```
def my_function(param=[]):
 param.append(“thing”)
 return param
 
my_function() # returns [“thing”]
my_function() # returns [“thing”, “thing”]
```
Despite using the empty list as a default value, python only evaluates the definition once and therefore that empty list is only empty before the first time it is called. The function will reuse the same list every time it is called. *oops
Back to *args, for functions. Lists are cool and all but using the *args option for function parameters is very useful. This way the function can accept any number of variables without having to rebuild the call for each permutation.
```
def multiply(*args):
    z = 1
    for num in args:
        z *= num
    print(z)

multiply(4, 5)
multiply(10, 9)
```

That is pretty awesome but it lacks the ability to have a different order of params and to define specific params. This gives us more flexibility in one area but less in another. Whats the fix for that?
**kwargs
KWargs or “keyword arguments” allows you to pass the keyworded, dictionary as arguments. Because dictionaries are almost always super useful.
def print_values(**kwargs):
    for key, value in kwargs.items():
        print("The value of {} is {}".format(key, value))

print_values(my_name="thor", your_name="hulk")
Key worded arguments are a pretty fantastic additional however, in order to leverage them effectively the code is going to have to handle dictionaries appropriately. Finally if the intent is to mix and match argument types the order of the declaration needs to work like this.
def example2(arg_1, arg_2, *args, kw_1="shark", kw_2="blobfish", **kwargs):
```
What about passing to args and kwargs? Simple enough just build a list or a dictionary and pass it to the proper function.
def some_args(arg_1, arg_2, arg_3):
    print("arg_1:", arg_1)
    print("arg_2:", arg_2)
    print("arg_3:", arg_3)

my_list = [2, 3]
some_args(1, *my_list)
def some_kwargs(kwarg_1, kwarg_2, kwarg_3):
    print("kwarg_1:", kwarg_1)
    print("kwarg_2:", kwarg_2)
    print("kwarg_3:", kwarg_3)

kwargs = {"kwarg_1": "Val", "kwarg_2": "Harper", "kwarg_3": "Remy"}
some_kwargs(**kwargs)
```

Conclusions, args and kwargs are fantastic for a number of cases, but of course they are not the end all be all solution. When writing functions and using them only use what is needed, don’t just use kwargs because it’s a new shiny toy because when all you have is a hammer then everything is a nail. Think about optimization, think about memory usage and think about maintainability as well as readability, then use the appropriate data type for the job.
Thank you to digital ocean for writing a fantastic tutorial on python, seriously check out their series on everything python.