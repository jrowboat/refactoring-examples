## Mysterious Name

### Notes from reading
- "When you can't think of a good name for something, it's often a sign of a deeper design malaise."

### Remedies for mysterious naming

#### Rename a function (Simple Mechanics)
- What code smell is the focus?
  - Name of a function
- What is the process of removing the code smell (refactoring)?
  - Change the method name to a more understandable name.
  - Find all references to the old method declaration, update them to the new one.
  - Test.
- How is the remedy a good refactoring? What are the benefits of the refactor?
- With discussion guide, include code snippets of how the changes are progressing
```
  // poorly named function - unnecessarily abbreviated
  function circum(radius) {
    return 2 * Math.PI * radius;
  }

  // method name is instinctual and easy to comprehend when first reading
  function circumference(radius) {
    return 2 * Math.PI * radius;
  }
```