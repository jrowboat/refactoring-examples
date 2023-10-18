## Mysterious Name

### Notes from reading
- "When you can't think of a good name for something, it's often a sign of a deeper design malaise."

### Remedies for mysterious naming

#### Rename a function with simple mechanics
- What code smell is the focus?
  - Name of a function
```
  // poorly named function - unnecessarily abbreviated
  function circum(radius) {
    return 2 * Math.PI * radius;
  }
```
- What is the process of removing the code smell (refactoring)?
  - Change the method name to a more understandable name.
  - Find all references to the old method declaration, update them to the new one.
  - Test.
```
  // method name is instinctual and easy to comprehend when first reading, provides some context for the behavior of the function
  function circumference(radius) {
    return 2 * Math.PI * radius;
  }
```
- How is the remedy a good refactoring? What are the benefits of the refactor?
  - Any future work on this code will be aided by a name that provides more context about the contents of the given function.
  - However, if the number of references to this function are sizeable, a [migration approach](#rename-a-function-with-migration-mechanics) may be more benefitial.

#### Rename a function with migration mechanics
- What code smell is the focus?
  - Name of a function
```
  // poorly named function - unnecessarily abbreviated
  function circum(radius) {
    return 2 * Math.PI * radius;
  }
```
- What is the process of removing the code smell (refactoring)?
  - Extract the function into a new function with a more understandable name.
  - Inline within the old function the newly extracted function
  - Replace references to the old function with reference to the new function.
  - With each reference replaced, test
  - Once all references to the old function have been updated, remove the old function
```
  // new function is inlined into the existing function.
  function circum(radius) {
    return circumfrence(radius);
  }

  // method name is instinctual and easy to comprehend when first reading, provides some context for the behavior of the function
  function circumference(radius) {
    return 2 * Math.PI * radius;
  }
```
- How is the remedy a good refactoring? What are the benefits of the refactor?
  - Quality of code readability is gradually improved over time.
  - Downside of this approach is a longer period of time with code smells in the system
  - Consumers of a given function do not have to immediately jump to the new name.

#### Rename a variable
- What code smell is the focus?
  - Name of a variable
```
  var webStAd = "www.google.com";
```
- What is the process of removing the code smell (refactoring)?
  - change the variable name
  - change a reference to the variable to match the new name
  - test
```
  var webSiteAddress = "www.google.com";
```
- How is the remedy a good refactoring? What are the benefits of the refactor?
  - The name is more clear and provides better context for what is in the contents of the variable

Discussion:
- what is the benefit of getters and setters for a variable?
  - getters and setters for a FIELD promote information hiding
    - the c# docs here said that a property, in using getters and setters, "helps promote the safety and flexibility of methods" when accessing data. However, I want to affirm the quality of code that is enhanced, not just the benefit given by the construct