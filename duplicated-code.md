## Duplicated Code

### Notes from reading
- Duplication means that every time you read these copies, you need to read them carefully to see if there's any difference. If you need to change the duplicated code, you have to find and catch each duplication.

### Remedies for duplicated code

#### Extract the common code into a method
- What code smell is the focus?
  - Repeated code in multiple functions or within the same function
```
// simple example where we are repeating an operation
function pythagoreanTheorem(a, b) {
  return (a * a) + (b * b);
}
```
- What is the process of removing the code smell (refactoring)?
  - Create a new function, and name it after the intent of the function (name it by what it does, not by how it does it)
  - Copy the extracted code from the source function into the new target function
  - Scan the extracted code for references to any variables that are local in scope to the source function and will not be in scope for the extracted function. Pass them as parameters.
    - nested functions don't run into this problem
  - Compile after all variables are dealt with.
  - Replace the extracted code in the source function with a call to the target function.
  - Test.
  - Look for other code that's the same or similar to the code just extracted, and consider using Replace Inline Code with Function Call to call the new function.
```
// new function is inlined where the repeated code existed
function pythagoreanTheorem(a, b) {
  return squared(a) + squared(b);
}

// repeated code is now encapsulated inside a function named after the intent of the function
function squared(value) { 
  return value * value;
}

```
- How is the remedy a good refactoring? What are the benefits of the refactor?
  - Repeated code is encapsulated
  - Example could be more complex


### Additional tools for duplicated code

#### Slide statements to make code extractable
- What code smell is the focus?
  - Similar code is present, but not exactly the same as another piece of code
- How can this tool help?
  - Code can be arranged for more easy extraction
- How do I use this tool?
  - Identify the target position to move the fragment to. Examine statements between source and target to see if there is interference for the candidate fragment. Abandon action if there is any interference.
  - Cut the fragment from the source and paste into the target position.
  - Test.

#### Pull code up from subclasses to reduce repeatedly executed code
- What code smell is the focus?
  - Similar code is located in classes below a super class or a layer of architecture above
- How can this tool help?
  - When subclasses share common code, I can extract that common code (if applicable) and place it into the parent class.
- How do I use this tool?
  - Inspect methods to ensure they are identical.
    - If they do the same thing, but are not identical, refactor them until they have identical bodies.
  - Check that all method calls and field references inside the method body refer to features that can be called from the superclass.
  - If the methods have different signatures, use Change Function Declaration to get them to the one you want to use on the superclass.
  - Create a new method in the superclass. Copy the body of one of the methods over to it.
  - Run static checks.
  - Delete one subclass method.
  - Test.
  - Keep deleting subclass methods until they are all gone.