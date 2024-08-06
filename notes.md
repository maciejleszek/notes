## LinkedIn

1. SQL
2. XML

## Git


1. basic Git guide:
### CONFIG

* ` git config --global user.name “Maciej Leszek" `
* ` git config --global user.email “mleszek@cbk.waw.pl" `
* ` git config -l ` :list all current settings

* `git init` :repo initialization
* `git status` :view untracked files
* `git diff` :show all the changes that have been made to the files

* `git add -A` :add all files to the staging area
* `git reset file.py` :remove file from the staging area | plain `git reset` for all files

### COMMIT

* `git commit -m “Commit message" `
* `git log` :log of specific commit

### REMOTE REPO

* `git remote add <origin> <url>` :adding remote repo - when our local repo has codes
* `git clone <url> <where to clone>` :cloning remote repo - when our local repo is empty
* `git fetch` :downloads the latest changes from the remote repo
* `git remote -v` :view info about remote repo 
* `git remote update <origin> --prune` :update changes made on remote 

### BRANCH

* `git branch -a` :list all branches in the repo, both locally and remotely
* ` git checkout <branch name> ` :set up to track specific branch, while choosing the right branch of imported submodule we need to go into the submodule folder

### PUSH/PULL

* `git pull origin <branch name>` :we have to first pull any changes that have been done in the remote repository before pushing our changes to it to avoid conflicts
* `git push origin <branch name>` :push changes to the remote repository
* `git push <origin> <local branch>:<remote branch>` :push changes with specified branches

* warning `git push --set-upstream --force` :fully deletes branch content and history


### **SUBMODULE**

* ` git submodule add <url> ` :we use add when we want to add submodule to our existing code
* ` git submodule update --init --recursive ` :to correctly import files of the nested labcore submodule
* `git submodule init` :initialize your local configuration file
* `git submodule update` :fetch all the data from that project and check out the appropriate commit listed in your superproject

2. `git fetch` is safe to run at any time since it never changes any of your local branches, while `git pull` brings a local branch up-to-date with its remote version, while also updating your other remote-tracking branches

3. to be checked: Rebase and Squash

*LINKS*
- git basics: https://www.freecodecamp.org/news/the-essential-git-handbook-a1cf77ed11b5/
- git submodules cool basic guide: https://gist.github.com/gitaarik/8735255


## Python

1. generator expression: `max_value = max(len(word) for word in unique_words_in_score_phrase)`
    This line is using a generator expression to find the maximum length of a string in the unique_words_in_score_phrase set.
    It's better to use that type of coding even with multiple for loops, in a basic test it was 10 times quicker:
    ```python
    import csv
    import psycopg2
    import timeit

    `SLOWER`
    conn = psycopg2.connect("dbname=dq user=dq")
    cur = conn.cursor()
    with open('ign.csv', 'r') as f:
        next(f)
        reader = csv.reader(f)
        start = timeit.timeit()
        rows, mogrified_rows, decoded_rows = [], [], []
        for row in reader:
            rows.append(row)
            mogrified_single_row = cur.mogrify("(%s, %s, %s, %s, %s, %s, %s, %s, %s)", row)
            mogrified_rows.append(mogrified_single_row)
            decoded_rows.append(mogrified_single_row.decode(conn.encoding))
        end = timeit.timeit()
        print(end - start)
        insert_string = ",".join(decoded_rows)
        cur.execute("INSERT INTO ign_reviews VALUES " + insert_string + ";")
        conn.commit()
        conn.close()
    
    `FASTER`
    conn1 = psycopg2.connect("dbname=dq user=dq")
    cur = conn1.cursor() 
    with open("ign.csv", "r") as t:
        next(t)
        reader = csv.reader(t)
        start = timeit.timeit()
        rows = [row for row in reader]
        mogrified_rows = [cur.mogrify("(%s, %s, %s, %s, %s, %s, %s, %s, %s)", row) for row in rows]
        decoded_rows = [row.decode(conn.encoding) for row in mogrified_rows]
        end = timeit.timeit()
        print(end - start)
        insert_string = ",".join(decoded_rows)
        cur.execute("INSERT INTO ign_reviews VALUES " + insert_string + ";")
        conn1.commit()
        conn1.close()
    ```

2. In general, to get an estimation of the runtime, we run the function several times and compute the average runtime

3. `b'hello'` - bytes literal, it means you're creating a bytes literal in Python. This is used to represent a sequence of bytes, rather than a sequence of characters. This indicates that the output is a `bytes` object. In order to convert a `bytes` object into a string, we need to use the `bytes.decode()` method by passing the encoding used on the `byte`s object.

4. Better Code Hub/Sigrid - apps that check your code and project structure - free alternatives to find

5. OOP - variables defined isnide of methods don't have self. Self is used when referencing to the globally defined variables for example in `__init__` or other methods. Meaning they are local variables

6. `entries`, `eventdata` are equal to the two values returned by `filter_map`

```python
def filter_map(event_str, entries):
    # code
    return matches, eventdata

def handle_event(self, event_str, _eventdata):

        entries, eventdata = LabGUI.filter_map(event_str, self.map) 
```

7. `__repr__` - is a special method in Python that is used to define the “official” string representation of an object. This string representation should be unambiguous and ideally could be used to recreate the object. `!r` represents `repr()`
https://stackoverflow.com/questions/44800801/in-python-format-f-string-strings-what-does-r-mean

8. In Python, the order of method definitions inside a `class` does not affect their usage *within* that class. 


### Data types

#### Dictionary

- In dictionary `.items()` returns an iterable view of its key-value pairs

- Merging two dicts: `eventdata = {**eventdata, **_eventdata}`

    ```python
    eventdata = {"key1": "value1", "key2": "value2"}
    _eventdata = {"key2": "new_value2", "key3": "value3"}

    eventdata = {**eventdata, **_eventdata}

    {"key1": "value1", "key2": "new_value2", "key3": "value3"} # eventdata values
    ```

- `get()` method returns the value of the item with the specified key

#### List

- `extend()` method adds the specified list elements (or any iterable) to the end of the current list. When the `extend() `function says "appending elements from the iterable," it means that it will take each element from the iterable (whether it's a list, string, tuple, etc.) and add them one by one to the end of the list:
```python
# Start with a list
my_list = [1, 2, 3]

# Define another iterable, like a string
another_iterable = "456"

# Use extend to add elements from the string to my_list
my_list.extend(another_iterable)

print(my_list)  # Output: [1, 2, 3, '4', '5', '6']
```

- 

### Type hints

- Specifying possible type of arg and return  - list or bool
```python
def func(arg: int | str) -> int | str:
    #         ^^^^^^^^^     ^^^^^^^^^ 
    #        type of arg   return type
```


### Print

- It changes print's ending character to space (" ")
```python
print(arg, end=" ")
```

### Args and Kwargs

- Allow to pass multiple arguments (`*args` - non-key, `**kwargs` - keyword)

- They allow to pass in multiple arguments without knowing how many of them, there will be:
```python
def add(*args): 
    total = 0
    for arg in args:
        total += arg

add(1, 2, 3)
```
- args is a `tuple` type

- Operator `*` is used to unpack. The single asterisk operator `*` can be used on any iterable that Python provides, while the double asterisk operator `**` can only be used on dictionaries. E.g.:
```python
my_list = [1, 2, 3]
print(my_list)
# [1, 2, 3]

print(*my_list)
# 1 2 3
```



- `kwargs` (keyword args) is a `dict` type

- We can access keys(`kwargs.keys()`) and values(`kwargs.values()`) of `kwargs`. To access both we can use `kwargs.items()`

### __ init __ 

1. Initialize attributes to be used in other functions, even blank:

```python
def __init__(self, temperature, humidity):
        self.temperature = temperature
        self.humidity = humidity
        self.observers = []
 
    #add an observer to the list of observers
    def add_observer(self, observer):
        self.observers.append(observer)
```

2. `__version__ = '0'` - ***TODO*** - read about it

### Abstract Class

1. Abstract classes are only meant to be subclassed, not to create instances directly

2. `self.__class__` checks whether the class is a type of itself

### OOP methods

1. Instance methods

```python
class MyClass:
    def method(self):
        return 'instance method called', self

```

- Has `self` parameter, through which instance methods can freely access attributes and other methods on the same object. This gives them a lot of power when it comes to modifying an object’s state. Not only can they modify object state, instance methods can also access the class itself through the `self.__class__` attribute. This means instance methods can also modify class state.

- Can modify object *instance state* 

- Can modify object *class state* - This makes instance methods powerful in terms of access restrictions - they can modify state on the object instance and on the class itself.

```python
obj = MyClass()
obj.method()

# we can write it shorter
MyClass.method(obj)

('instance method called', <MyClass instance at 0x10205d190>) # method has access to the object instance
```

- This confirmed that method (the instance `method`) has access to the object instance (printed as `<MyClass instance>`) via the `self` argument.

- When the method is called, Python replaces the `self` argument with the instance object, `obj`.

- Attempt to call the `method()` without specyfing object will result with a *TypeError*


2. Class methods

```python
class MyClass:
    @classmethod
    def classmethod(cls):
        return 'class method called', cls
```

- Instead of accepting a self parameter, class methods take a `cls` parameter that points to the class—and not the object instance—when the method is called. Because the class method only has access to this cls argument, it can’t modify object instance state. That would require access to self. However, class methods can still modify class state that applies across all instances of the class

- Can't modify object *instance state* 

- Can modify object *class state*

```python
obj = MyClass()
obj.classmethod()
('class method called', <class MyClass at 0x101a2f4c8>) # Only access to the class, not an instance as previous one
```

- Calling `classmethod()` showed us it doesn’t have access to the `<MyClass instance>` object, but only to the `<class MyClass>` object, representing the class itself (everything in Python is an object, even classes themselves).

- Notice how Python automatically passes the class as the first argument to the function when we call `MyClass.classmethod()`. Calling a method in Python through the *dot syntax* triggers this behavior. The `self` parameter on instance methods works the same way.

- Please note that naming these parameters `self` and `cls` is just a convention. All that matters is that they’re positioned *first* in the parameter list for the method.



3. Static methods

```python
class MyClass:
    @staticmethod
    def staticmethod():
        return 'static method called'
```

- This type of method takes neither a self nor a cls parameter. Therefore a static method can neither modify object state nor class state. Static methods are restricted in what data they can access - and they’re primarily a way to` namespace` your methods - ***TODO*** namespace to be checked

- Can't modify object *instance state* and *class state*

```python
obj = MyClass()
obj.staticmethod()
'static method called'
```

- They work like regular functions but belong to the class’s (and every instance’s) namespace.

- Static methods also have benefits when it comes to writing test code. The method is completely independent from the rest of the class it’s much easier to test.




### Conventions

1. Single Leading Underscore `_variable`: This is a convention indicating that a variable or method is intended for internal use within the class/module. It does not prevent access but signals to other developers that it’s not part of the public API - it shouldn't be accessed directly from outside the class

2. Double Leading Underscore `__variable`: This triggers name mangling where the interpreter changes the name of the variable/method to include the class name, making it harder to access from outside the class. It's used to avoid conflicts in subclasses.

3. Single underscore `_`: This indicates a temporary or throwaway variable

4. Single Trailing Underscore `var_`: This is used when the desired name is already taken by a keyword in Python. It helps avoid conflicts with Python keywords



### Event-Driven programming

Links:
1. https://gpttutorpro.com/what-is-event-driven-programming-an-introduction-and-overview/


Events are occurrences or changes in the state of the system, such as user input, network requests, timers, or errors

Callbacks are functions that are executed when an event occurs, usually by passing them as arguments to other functions or methods.

The program does not follow a predefined sequence of instructions, but rather reacts to the events as they happen. This makes event-driven programming suitable for applications that need to handle multiple concurrent tasks, such as gui, web servers.

Event-driven programming is non-blocking, meaning that the program does not wait for an event to finish before moving on to the next one

There are three key concepts: 
1. the event loop - it checks for new events and executes the corresponding callbacks
2. the event queue - is a data structure that stores the events that occur in the system. The events are added to the queue in the order they occur, and are removed from the queue when they are processed by the event loop. it can also handle different priorities of events
3. the callback functions - functions that are executed when an event occurs

Python provides a built-in event module that allows you to create and manage event objects that can be set, cleared, and waited for by different threads or processes.
Another way to handle events in Python is to use the observer pattern, which is a behavioral design pattern that defines a one-to-many relationship between a subject and multiple observers.

The event module is a built-in module in Python that provides a class called Event that represents an event object. An event object has two possible states: set or clear.

- `set()`: This method sets the event object to the set state. This means that the event has occurred or the condition is satisfied.

- `clear()`: This method clears the event object to the clear state. This means that the event has not occurred or the condition is not satisfied.

- `is_set()`: This method returns True if the event object is in the set state, or False otherwise.

- `wait(timeout=None)`: This method blocks the calling thread or process until the event object is set, or until the optional timeout expires

1. Button
2. Event listener
3. Event queue
4. Event loop
5. Event handler


## Flask

### WebSockets

- Run an app using SocketIO: `socketio.run(app) `

- With `"event"` we are specyfing event's name
```python
@socketio.on("connect")
def handle_connect():
    print("Client connected")

@socketio.on("user_join")
def handle_suer_join(username): # username is being sent from JS 
    print("user {username} joined)

@socketio.on("new_message")
def handle_new_message(message):
    print("Message: {message}")
    # Sends message (data) e.g. str or dict, broadcast allows to send the message to all connected clients
    socketio.emit("chat", {"message": message}, broadcast=True) 

```

## JS

1. The JS `for of` statement loops through the values of an iterable object.
```js
for (variable of iterable) {
  // code block to be executed
}
```

2. 

### Objects

- Initialization of an empty dictionary (in JS it's not a defined data type)
    ```js
    const object = {}
    ```

### WebSockets
https://www.youtube.com/watch?v=AMp6hlA8xKA&t=3s
- SocketIO object is needed: `const socket = io();`

- Not connecting directly `const socket = io({autoConnect: false});`

```js
document.getElementById("join-btn").addEventListener("click", function(){
    let username = document.getElementById("username").value;

    socket.connect(); //conneting to the socket from js

    socket.on("connect", function() { // "connect" is from py file with @socketio.on("connect")
        socket.emit("user_join", username); // it sends new event(user_join) over to the server, username gives info which user joins
    })
})

document.getElementById("message").addEventListener("keyup", function (event) {
    if (event.key == "Enter") { // Pressing Enter key is our event
        // Assigns value of the element message
        let message = document.getElementById("message").value;
        socket.emit("new_message", message);
        // Resets message value
        document.getElementById("message").value = "";
    }
})
```

## SQL


## Bash
1. `code .` - opens VSC

2. `start .` - opens current directory in file explorer

3. `cat <file_name>` :writes file content in the terminal

4. `.` represents the directory you are in and `..` represents the parent directory

## VSC
1. theme: JoelCrosby.one-dark-darker


## Programming generally

1. Difference between a function and a method
    - *Function* - self-standing block of code, not associated with class or object

    - *Method* - associated with a class or an object and frequently operates on this object's data

2. Difference between a parameter and an argument
    - *Parameter* - is a variable in the declaration of a function. It is a placeholder used in the function definition, where you define what kind of input the function expects

    - *Argument* - is the actual value or data that you pass to the function when you call it. It is the input that you provide to a function when invoking it

3. 


