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
    - A fragment cannot slide backwards earlier than any element it references is declared.
    - A fragment cannot slide forwards beyond any element that references it.
    - A fragment cannot slide over any statement that modifies an element it references.
    - A fragment that modifies an element cannot slide over any other element that references the modified element.
A fragment that modifies an element cannot slide over any other element that references the modified element.
  - Cut the fragment from the source and paste into the target position.
  - Test.

```
// There seems to be a lot of repeated patterns in the following statement, but the code isn't immediately extractable because it's not clear where we can extract right now
function pythagoreanTheorem() {
  const sidesOfTriangle = getSidesOfTriangle();
  const firstSideOfTriangle = sidesOfTriangle['a'];
  const secondSideOfTriangle = sidesOfTriangle['b'];
  const squaredSecondSideOfTriangle = secondSideOfTriangle * secondSideOfTriangle;
  const squaredFirstSideOfTriangle = firstSideOfTriangle * firstSideOfTriangle;
  return squaredFirstSideOfTriangle + squaredSecondSideOfTriangle;
}

// Sliding squaredFirstSideOfTriangle higher up more clearly shows the repeated pattern of accessing the sidesOfTriangle object, then calculating the square. We can more easily extract the shared code here. 
function pythagoreanTheorem() {
  const sidesOfTriangle = getSidesOfTriangle();

  const firstSideOfTriangle = sidesOfTriangle['a'];
  const squaredFirstSideOfTriangle = firstSideOfTriangle * firstSideOfTriangle;

  const secondSideOfTriangle = sidesOfTriangle['b'];
  const squaredSecondSideOfTriangle = secondSideOfTriangle * secondSideOfTriangle;

  return squaredFirstSideOfTriangle + squaredSecondSideOfTriangle;
}
```

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

```
  // in this example, both the proveTriangleSatisfiesTherom() and getTriangleSides() methods get the triangle, then do something with the sides. We can save lines of code here if we parameterize the proveTriangleSatisfiesTheorem so that it takes a sides object, eliminating the repeated code in the service

  public PythagoreanTheoremControllerHelper() {
    public getTriangleSides() {
      const triangle = PythagoreanTheoremService.getTriangle();
      return triangle.select(x => x.sides);  
    }
  }

  public PythagoreanTheoremController() {
    public doesTriangleSatisfyTheorem() {
      return PythagoreanTheoremService.proveTriangleSatisfiesTheorem();
    }

    public getTriangleSides() {
      return PythagoreanTheoremControllerHelper.getTriangleSides();
    }
  }

  public PythagoreanTheoremService() {
    public getTriangle() {
      return new Triangle {
        sides: {
          {'a': 3},
          {'b': 4},
          {'c': 5}
        }
        type: 'Scalene'
      }
    }

    public proveTriangleSatisfiesTheorem() {
      const triangle = getTriangle();
      const sideASquared = triangle.sides['a'] * triangle.sides['a'];
      const sideBSquared = triangle.sides['b'] * triangle.sides['b'];
      const sideCSquared = triangle.sides['c'] * triangle.sides['c'];
      return sideASquared + sideBSquared = sideCSquared;
    }
  }
```  
  
```
  // duplicated code in service eliminated and service method is parameterized

  public PythagoreanTheoremControllerHelper() {
    public getTriangleSides() {
      const triangle = PythagoreanTheoremService.getTriangle();
      return triangle.select(x => x.sides);  
    }
  }

  public PythagoreanTheoremController() {
    public doesTriangleSatisfyTheorem() {
      const sides = PythagoreanTheoremControllerHelper.getTriangleSides();
      return PythagoreanTheoremService.proveTriangleSatisfiesTheorem(sides);
    }

    public getTriangleSides() {
      return PythagoreanTheoremControllerHelper.getTriangleSides();
    }
  }

  public PythagoreanTheoremService() {
    public getTriangle() {
      return new Triangle {
        sides: new Side[] {
          {'a': 3},
          {'b': 4},
          {'c': 5}
        }
        type: 'Scalene'
      }
    }

    public proveTriangleSatisfiesTheorem(Side[] sides) {
      public squared(side)
        return side * side;

      return squared(sides['a']) + squared(sides['b']) = squared(sides['c']);
    }
  }
```