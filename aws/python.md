# Py
- single line comment start with `#` and multi with `triple quotes`
- When no return value is specified in a function, it will automatically return None.
- var definition make it visible in the whole outer scope not in the block only
### Type-System:

- Static vs. Dynamic Type
    - when type information is acquired
    - in statically typed languages the type information is acquired at compile or interpet time (e.g. C, C++, Java, Go)
    - in dynamically typed languages the type information is acquired at runtime (e.g. Python, Ruby, JavaScript)
      - type is not associated with the variable it associated with the value.
- Strong vs. Weak Type
    - how strictly types are distinguished
    - in strongly typed languages the type information is strictly distinguished (e.g. C, C++, Java, Go)
    - in weakly typed languages for example the language can implicitly convert types (e.g. Python, Ruby, JavaScript)
- Manifest vs. Inferred Type
    - in manifest typed languages the type information is explicitly declared (e.g. C, C++, Java, Go)
    - in inferred typed languages the type information is inferred by the compiler (e.g. Python, Ruby, JavaScript). you don't require to declare the type of the variable.
- Nominal vs. Structural Type
    - in nominal typed languages to compare if two types are equal the name of the type is compared (e.g. C, C++, Java, Go)
    - in structural typed(a static type system) languages to compare if two types are equal the structure of the type is compared (same fields) (e.g. Python, Ruby, JavaScript)
    - python is duck typed language(dynamic) it's kind of similar to structural typing but it's not the same. duck typing is a style of dynamic typing in which an object's methods and properties determine the valid semantics, rather than its inheritance from a particular class or implementation of a specific interface.
      - example: if it walks like a duck and quacks like a duck, then it must be a duck. function need param with method x so any object that has method x can be passed to the function.

### Context-Manager:
## 

- compatible with async code. allow to control what happens when you create or destroy a resource. making sure if there is an exception or the program need to stop the resource is closed properly.
__enter__ & __exit__ are the methods that are called when you create the resource and  when you destroy the resource.
```python
```python
- Note 3

## Chapter 2:

### Section 2.1:

- Note 1
- Note 2
- Note 3

### Section 2.2:

- Note 1
- Note 2
- Note 3

[]: # FILEPATH: /path/to/your/file.md
