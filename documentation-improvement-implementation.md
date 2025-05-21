# Documentation Improvement Implementation

Based on the requirements, here are the specific changes that need to be made to the documentation:

## 1. Use Mintlify Step Components for Steps

Replace regular step descriptions with Mintlify `<Step>` components wrapped in `<Steps>` tags.

### Example:

**Current format:**
```markdown
1. **Initialize Payment**: Create a payment with customer and transaction details
2. **Customer Enters Phone Number**: The customer enters their Mobile Money phone number
3. **Payment Request**: Notch Pay sends a payment request to the Mobile Money provider
```

**New format:**
```markdown
<Steps>
  <Step title="Initialize Payment">
    Create a payment with customer and transaction details
  </Step>
  <Step title="Customer Enters Phone Number">
    The customer enters their Mobile Money phone number
  </Step>
  <Step title="Payment Request">
    Notch Pay sends a payment request to the Mobile Money provider
  </Step>
</Steps>
```

### Files to Update:
- `accept-payments/mobile-money.mdx` - Complete Integration Flow steps (already implemented for the main flow)
- `accept-payments/other-methods.mdx` - Digital Wallet, USSD, and QR Code payment flow steps

## 2. Use Mintlify Card Components

Replace custom div elements with Mintlify `<Card>` components wrapped in `<CardGroup>` tags.

### Example:

**Current format:**
```html
<div className="grid grid-cols-1 md:grid-cols-3 gap-4 mt-6">
  <div className="border rounded-lg p-4 hover:shadow-md transition-shadow">
    <h3 className="text-md font-semibold mb-2">MTN Mobile Money</h3>
    <p className="text-sm">Available in Cameroon, Côte d'Ivoire, Ghana, Rwanda, Uganda, and other countries where MTN operates.</p>
    <p className="text-xs mt-2 text-gray-500">Average success rate: 92%</p>
  </div>
</div>
```

**New format:**
```markdown
<CardGroup cols={3}>
  <Card title="MTN Mobile Money">
    Available in Cameroon, Côte d'Ivoire, Ghana, Rwanda, Uganda, and other countries where MTN operates.

    Average success rate: 92%
  </Card>
</CardGroup>
```

### Files to Update:
- `accept-payments/mobile-money.mdx` - Provider cards and best practices sections
- `accept-payments/other-methods.mdx` - Wallet information cards and best practices sections

## 3. Update API Response Examples

Update API response examples to show data in the "payment" property instead of "transaction".

### Example:

**Current format:**
```
.then(data => {
  // Store the payment reference for the next step
  const paymentReference = data.transaction.reference;
})
```

**New format:**
```
.then(data => {
  // Store the payment reference for the next step
  const paymentReference = data.payment.reference;
})
```

### Files to Update:
- `accept-payments/mobile-money.mdx`:
  - Line 177: `data.transaction.reference` → `data.payment.reference`
  - Line 204: `data.transaction.id` → `data.payment.id`
  - Lines 219-222: `data.transaction.status` → `data.payment.status`
- `accept-payments/other-methods.mdx`:
  - Line 118: `data.transaction.reference` → `data.payment.reference`
  - Line 182: `data.transaction.reference` → `data.payment.reference`
- `get-started/quickstart.mdx`:
  - Line 218: `data.transaction.status` → `data.payment.status`
  - Line 251: `data.transaction.status` → `data.payment.status`
  - Line 272: `data.transaction.status` → `data.payment.status`
- `api-reference/payments.mdx`:
  - Lines 272-280: Update the "transaction" property to "payment" in the response example
  - Lines 313-323: Update the "transaction" property to "payment" in the response example
  - Lines 422-426: Update the "transaction" property to "payment" in the response example

## 4. Convert Appropriate Content Sections to Tabs

Ensure that elements that should be tabs are consistently using the Mintlify `<Tabs>` and `<Tab>` components.

### Example:

**Current format (if using Accordion):**
```markdown
<AccordionGroup>
  <Accordion title="Section 1">
    Content for section 1
  </Accordion>
  <Accordion title="Section 2">
    Content for section 2
  </Accordion>
</AccordionGroup>
```

**New format:**
```markdown
<Tabs>
  <Tab title="Section 1">
    Content for section 1
  </Tab>
  <Tab title="Section 2">
    Content for section 2
  </Tab>
</Tabs>
```

### Files to Review:
- Review all documentation files to ensure consistent use of tabs for similar content types

## Implementation Notes

1. The mobile-money.mdx file already has Steps and Step components implemented for the main flow, but needs updates for the Complete Integration Flow steps.
2. The file has formatting issues that make it difficult to make targeted edits. It's recommended to make a complete copy of the file and make all the necessary changes at once.
3. When updating API response examples, be careful to maintain the correct indentation and formatting of the code blocks.
4. Some sections are already using tabs (country-specific information, integration options), so these don't need to be changed.
5. The troubleshooting sections are using AccordionGroup/Accordion, which may be appropriate for that type of content, so they might not need to be converted to tabs.

## Testing

After making the changes, test the documentation to ensure that:
1. All components render correctly
2. Code examples are properly formatted
3. The overall structure and navigation remain intact
