## Long Function

### Notes from reading
- A heuristic we follow is that whenever we feel the need to comment something, we write a function instead.

### Remedies for duplicated code

#### Extract function
- What code smell is the focus?
    - A function that is too long
```
public void ProcessOrder(Order order)
{
    // Validate the order
    if (order == null)
    {
        throw new ArgumentNullException(nameof(order));
    }

    if (order.Items == null || order.Items.Count == 0)
    {
        throw new ArgumentException("Order must contain at least one item", nameof(order));
    }

    // Calculate the total price of the order
    decimal totalPrice = 0;
    foreach (var item in order.Items)
    {
        totalPrice += item.Price * item.Quantity;
    }

    // Apply any discounts to the order
    if (order.DiscountCode != null)
    {
        var discount = GetDiscount(order.DiscountCode);
        if (discount != null)
        {
            totalPrice -= discount.Amount;
        }
    }

    // Apply any taxes to the order
    var taxRate = GetTaxRate(order.ShippingAddress.ZipCode);
    var taxAmount = totalPrice * taxRate;
    totalPrice += taxAmount;

    // Process the payment for the order
    var paymentMethod = GetPaymentMethod(order.PaymentMethodId);
    if (paymentMethod == null)
    {
        throw new ArgumentException("Invalid payment method", nameof(order));
    }

    var paymentResult = paymentMethod.ProcessPayment(totalPrice);
    if (!paymentResult.Success)
    {
        throw new Exception("Payment failed: " + paymentResult.ErrorMessage);
    }

    // Update the order status
    order.Status = OrderStatus.Processed;
    order.ProcessedDate = DateTime.UtcNow;

    // Send a confirmation email to the customer
    var emailService = new EmailService();
    var emailBody = $"Thank you for your order! Your total is {totalPrice:C}.";
    emailService.SendEmail(order.Customer.Email, "Order Confirmation", emailBody);
}

```
- What is the process of removing the code smell (refactoring)?
    - Extract highly coupled lines within the function into their own functions
```
ublic void ProcessOrder(Order order)
{
    // Validate the order
    ValidateOrder(order);

    // Calculate the total price of the order
    decimal totalPrice = CalculateTotalPrice(order);

    // Apply any discounts to the order
    ApplyDiscounts(order, ref totalPrice);

    // Apply any taxes to the order
    ApplyTaxes(order.ShippingAddress.ZipCode, ref totalPrice);

    // Process the payment for the order
    ProcessPayment(order.PaymentMethodId, totalPrice);

    // Update the order status
    UpdateOrderStatus(order);

    // Send a confirmation email to the customer
    SendConfirmationEmail(order, totalPrice);
}

private void ValidateOrder(Order order)
{
    if (order == null)
    {
        throw new ArgumentNullException(nameof(order));
    }

    if (order.Items == null || order.Items.Count == 0)
    {
        throw new ArgumentException("Order must contain at least one item", nameof(order));
    }
}

private decimal CalculateTotalPrice(Order order)
{
    decimal totalPrice = 0;
    foreach (var item in order.Items)
    {
        totalPrice += item.Price * item.Quantity;
    }
    return totalPrice;
}

private void ApplyDiscounts(Order order, ref decimal totalPrice)
{
    if (order.DiscountCode != null)
    {
        var discount = GetDiscount(order.DiscountCode);
        if (discount != null)
        {
            totalPrice -= discount.Amount;
        }
    }
}

private void ApplyTaxes(string zipCode, ref decimal totalPrice)
{
    var taxRate = GetTaxRate(zipCode);
    var taxAmount = totalPrice * taxRate;
    totalPrice += taxAmount;
}

private void ProcessPayment(int paymentMethodId, decimal totalPrice)
{
    var paymentMethod = GetPaymentMethod(paymentMethodId);
    if (paymentMethod == null)
    {
        throw new ArgumentException("Invalid payment method", nameof(paymentMethodId));
    }

    var paymentResult = paymentMethod.ProcessPayment(totalPrice);
    if (!paymentResult.Success)
    {
        throw new Exception("Payment failed: " + paymentResult.ErrorMessage);
    }
}

private void UpdateOrderStatus(Order order)
{
    order.Status = OrderStatus.Processed;
    order.ProcessedDate = DateTime.UtcNow;
}

private void SendConfirmationEmail(Order order, decimal totalPrice)
{
    var emailService = new EmailService();
    var emailBody = $"Thank you for your order! Your total is {totalPrice:C}.";
    emailService.SendEmail(order.Customer.Email, "Order Confirmation", emailBody);
}
```
- How is the remedy a good refactoring? What are the benefits of the refactor?
    - Provides more context within the original function
    - Reduces amount of information needed to understand original function

### Additional tools for duplicated code

#### Replace temp with query
- What code smell is the focus?
    - Extra code being used multiple places in a function
```
const basePrice = this._quantity * this._itemPrice;
if (basePrice > 1000)
  return basePrice * 0.95;
else
  return basePrice * 0.98;
```
- How can this tool help?
    - Instead of creating a temp variable, we can create a one line function that then can be referenced across the file and reduce the number of information points to load mentally
```
get basePrice() {this._quantity * this._itemPrice;}

...

if (this.basePrice > 1000)
  return this.basePrice * 0.95;
else
  return this.basePrice * 0.98;
```
- How do I use this tool?
    - Check that the variable is determined entirely before it's used, and the code that calculates it does not yield a different value whenever it is used.
    - If the variable isn't read-only, and can be made read-only, do so.
    - Test.
    - Extract the assignment of the variable into a function.
    - If the variable and the function cannot share a name, use a temporary name for the function.
    - Ensure the extracted function is free of side effects. If not, use Separate Query from Modifier.
    - Test.

#### Introduce Parameter Object
- What code smell is the focus?
    - A long list of parameters
```
function amountInvoiced(startDate, endDate) {...}
function amountReceived(startDate, endDate) {...}
function amountOverdue(startDate, endDate) {...}
```
- How can this tool help?
    - Reduce the size of a parameter list and create more understandable method signature with less information points
```
function amountInvoiced(aDateRange) {...}
function amountReceived(aDateRange) {...}
function amountOverdue(aDateRange) {...}
```
- How do I use this tool?
    - If there isn't a suitable structure already, create one.
    - Test.
    - Use Change Function Declaration to add a parameter for the new structure.
    - Test.
    - Adjust each caller to pass in the correct instance of the new structure. Test after each one.
    - For each element of the new structure, replace the use of the original parameter with the element of the structure. Remove the parameter. Test.

#### Preserve Whole Object
- What code smell is the focus?
    - A long list of parameters
```
const low = aRoom.daysTempRange.low;
const high = aRoom.daysTempRange.high;
if (aPlan.withinRange(low, high))
```
- How can this tool help?
    - Reduce the size of a parameter list and create more understandable method signature with less information points
```
if (aPlan.withinRange(aRoom.daysTempRange))
```
- How do I use this tool?
    - Create an empty function with the desired parameters.
    - Fill the body of the new function with a call to the old function, mapping from the new parameters to the old ones.
    - Run static checks.
    - Adjust each caller to use the new function, testing after each change.
    - Once all original callers have been changed, use Inline Function on the original function.
    - Change the name of the new function and all its callers.

#### Replace Function with Command
- What code smell is the focus?
    - A large function that can't easily be reduced via previously mentioned tools
```
function score(candidate, medicalExam, scoringGuide) {
  let result = 0;
  let healthLevel = 0;
  // long body code
}
```
- How can this tool help?
    - This can encapsulate the function into its own object, thus removing it from the context of the class it was in and reduce the amount of information in the originating class, making it more readable
```
class Scorer {
  constructor(candidate, medicalExam, scoringGuide) {
    this._candidate = candidate;
    this._medicalExam = medicalExam;
    this._scoringGuide = scoringGuide;
  }

  execute() {
    this._result = 0;
    this._healthLevel = 0;
    // long body code
  }
}
```
- How do I use this tool?
    - Create an empty class for the function. Name it based on the function.
    - Use Move Function to move the function to the empty class.
    - Consider making a field for each argument, and move these arguments to the constructor.

#### Replace Conditional with Polymorphism
- What code smell is the focus?
- How can this tool help?
- How do I use this tool?

#### Decompose conditional
- What code smell is the focus?
    - A complex conditional
```
if (!aDate.isBefore(plan.summerStart) && !aDate.isAfter(plan.summerEnd))
  charge = quantity * plan.summerRate;
else
  charge = quantity * plan.regularRate + plan.regularServiceCharge;
```
- How can this tool help?
    - By placing a complicated condition into its own function, the name of said function can help semantically capture what that condition is checking and improve the readability of the originating function
```
if (summer())
  charge = summerCharge();
else
  charge = regularCharge();
```
- How do I use this tool?
    - Apply Extract Function on the condition and each leg of the conditional.

#### Replace conditional with polymorphism
- What code smell is the focus?
    - Repeated use of the same switch statement
```
switch (bird.type) {
  case 'EuropeanSwallow':
    return "average";
  case 'AfricanSwallow':
    return (bird.numberOfCoconuts > 2) ? "tired" : "average";
  case 'NorwegianBlueParrot':
    return (bird.voltage > 100) ? "scorched" : "beautiful";
  default:
    return "unknown";
```
- How can this tool help?
    - Using polymorphism, classes can be built inheriting from a super object, then create an implementation of an abstract property on the parent object to give a specific calculation for that specific type of the parent object
```
class EuropeanSwallow {
  get plumage() {
    return "average";
  }
class AfricanSwallow {
  get plumage() {
     return (this.numberOfCoconuts > 2) ? "tired" : "average";
  }
class NorwegianBlueParrot {
  get plumage() {
     return (this.voltage > 100) ? "scorched" : "beautiful";
  }
```
- How do I use this tool?
    - If classes do not exist for polymorphic behavior, create them together with a factory function to return the correct instance.
    - Use the factory function in calling code.
    - Move the conditional function to the superclass.
    -If the conditional logic is not a self-contained function, use Extract Function to make it so.
    - Pick one of the subclasses. Create a subclass method that overrides the conditional statement method. Copy the body of that leg of the conditional statement into the subclass method and adjust it to fit.
    - Repeat for each leg of the conditional.
    - Leave a default case for the superclass method. Or, if superclass should be abstract, declare that method as abstract or throw an error to show it should be the responsibility of a subclass.

#### Split loop
- What code smell is the focus?
    - A large loop
```
let averageAge = 0;
let totalSalary = 0;
for (const p of people) {
  averageAge += p.age;
  totalSalary += p.salary;
}
averageAge = averageAge / people.length;
```
- How can this tool help?
    - By reducing the number of commands in a loop, the semantic meaning of what is being performed in each loop is more understandable
```
let totalSalary = 0;
for (const p of people) {
  totalSalary += p.salary;
}

let averageAge = 0;
for (const p of people) {
  averageAge += p.age;
}
averageAge = averageAge / people.length;
```
- How do I use this tool?
    - Copy the loop.
    - Identify and eliminate duplicate side effects.
    - Test.

