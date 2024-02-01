---
description: tiny note on python stuff
---

# Python Gotchas

## \_\_str\_\_ VS \_\_repr\_\_

```python

class Book:
    def __init__(self, title, author, price):
        self.title = title
        self.author = author
        self.price = price

    def __str__(self):
        return f"{self.title} by {self.author}, costs {self.price}"

    def __repr__(self):
        return f"title={self.title}, author={self.author}, price={self.price}"
```

The `__repr__` method in Python returns a string that describes a printable representation of the object. This string should be detailed and contain all the information necessary to recreate the object, if possible. It's primarily used for debugging and development. `repr()` is intended to produce output that is mostly machine-readable (in many cases, it could be valid Python code).

On the other hand, `__str__` is used to find the “informal” (or nicely printable) string representation of an object. The `__str__` method is supposed to return a human-readable format, which is good for logging or to display information about the object.

**When to use `__str__` vs `__repr__`?**

If you have designed a new class and want to control how it should look when it's printed or converted to a string, you should implement `__str__`.

If you want to control how the object should look in environments that are designed for developers (like in debuggers), or if you want to output detailed information about the object for debugging purposes, you should implement `__repr__`.

<mark style="color:yellow;">**If**</mark><mark style="color:yellow;">** **</mark><mark style="color:yellow;">**`__str__`**</mark><mark style="color:yellow;">** **</mark><mark style="color:yellow;">**is not defined, Python will call**</mark><mark style="color:yellow;">** **</mark><mark style="color:yellow;">**`__repr__`**</mark><mark style="color:yellow;">** **</mark><mark style="color:yellow;">**as a fallback. If you only implement one of these methods, choose**</mark><mark style="color:yellow;">** **</mark><mark style="color:yellow;">**`__repr__`**</mark><mark style="color:yellow;">**, because it's used as a fallback for**</mark><mark style="color:yellow;">** **</mark><mark style="color:yellow;">**`__str__`**</mark><mark style="color:yellow;">**. But if you need a human-readable representation of the object, you should implement**</mark><mark style="color:yellow;">** **</mark><mark style="color:yellow;">**`__str__`**</mark><mark style="color:yellow;">** **</mark><mark style="color:yellow;">**as well.**</mark>

## \_\_hash\_\_ and hash()

```python
class Book:
    def __init__(self, title, author, price):
        self.title = title
        self.author = author
        self.price = price

    def __hash__(self):
        return hash((self.title, self.author, self.price))
```

The `hash()` function in Python is used to generate a unique integer identifier for an object. This function takes an object as input and returns an integer. The `hash()` function is used to support efficient lookup and storage of objects in a dictionary, which is a hash table.

The `hash()` function works by taking input and generating a fixed-size string of bytes, typically in the form of an integer. The output is intended to be random and unique for different inputs. However, it's important to note that the `hash()` function is not designed to be a secure method for hashing data. It's primarily used for data retrieval and not for cryptographic or security purposes.

In your selected code, the `hash()` function is used in the `__hash__` method of a class. This method is used to override the default behavior of the `hash()` function for instances of this class. Instead of using the default hash (which is based on the object's memory address), this `__hash__` method returns a hash that is based on the `title`, `author`, and `price` attributes of the object. This means that two objects with the same `title`, `author`, and `price` will have the same hash value.

<mark style="color:yellow;">This is useful when you want to use instances of this class as keys in a dictionary or elements in a set. Python uses the hash value to quickly compare objects and to organize them in memory. If the</mark> <mark style="color:yellow;"></mark><mark style="color:yellow;">`__hash__`</mark> <mark style="color:yellow;"></mark><mark style="color:yellow;">method wasn't defined in this class, Python would use the default hash which is unique for each instance, even if their attributes are the same.</mark>

let's consider a scenario where you want to use instances of the `Book` class as keys in a dictionary to store reviews for each book. Here's how you can do this

```python
# Create some books
book1 = Book("Harry Potter", "JK Rowling", 39.95)
book2 = Book("The Hobbit", "J.R.R. Tolkien", 29.95)

# Create a dictionary where the keys are books and the values are reviews
reviews = {
    book1: "Great book, very enjoyable!",
    book2: "An epic adventure!"
}

# Now you can look up a review for a book in an efficient manner
print(reviews[book1])  # Outputs: "Great book, very enjoyable!"

# If you create another book with the same details as book1
book3 = Book("Harry Potter", "JK Rowling", 39.95)

# It will be considered equal to book1, because they have the same title, author, and price
# So you can use it to look up the review for book1
print(reviews[book3])  # Outputs: "Great book, very enjoyable!"
```

In this example, the `__hash__` method of the `Book` class is used to generate a hash value for each book. This hash value is used by the dictionary to quickly locate the value associated with a given key. If the `__hash__` method wasn't defined in the `Book` class, each instance would have a unique hash value, even if their attributes are the same. This would mean that `book1` and `book3` would be considered different keys in the dictionary, even though they represent the same book.

### How set and dictionary uses hash()

In Python, both sets and dictionaries (dicts) are implemented as hash tables. This means they use the `hash()` function to quickly look up values.

**Here's how it works:**

**Sets**: When you add an object to a set, Python uses the `hash()` function to compute a hash value for the object. This hash value is then used to determine where in the set's underlying data structure the object should be stored. When you check if an object is in the set, Python again computes the hash value of the object and looks directly at the corresponding location in the data structure. This makes checking membership in a set very fast, regardless of the size of the set.

**Dictionaries**: Dictionaries work in a similar way, but they use the hash values of keys instead of values. When you store a value in a dictionary with a certain key, Python computes the hash value of the key and uses it to determine where to store the value in the dictionary's underlying data structure. When you look up a value in the dictionary by its key, Python again computes the hash value of the key and looks directly at the corresponding location in the data structure. This makes looking up values in a dictionary by their keys very fast, regardless of the size of the dictionary.
