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
- How can this tool help?
- How do I use this tool?

#### Split loop
- What code smell is the focus?
- How can this tool help?
- How do I use this tool?

#### Introduce Parameter Object and Preserve Whole Object
- What code smell is the focus?
- How can this tool help?
- How do I use this tool?

#### Replace Function with Command
- What code smell is the focus?
- How can this tool help?
- How do I use this tool?

#### Replace Conditional with Polymorphism
- What code smell is the focus?
- How can this tool help?
- How do I use this tool?

#### Decompose conditional
- What code smell is the focus?
    - 
- How can this tool help?
- How do I use this tool?

