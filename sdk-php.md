# NotchPay PHP Library

A PHP library to easily integrate the [Notch Pay](https://notchpay.co/) API into your applications.

## Installation

You can install the package via Composer:

```bash
composer require notchpay/notchpay-php
```

## Configuration

Before using the library, you need to configure your API key:

```php
use NotchPay\NotchPay;

// Set your API key
NotchPay::setApiKey('b.xxxxxxx'); // Production API key
// or
NotchPay::setApiKey('sb.xxxxxxx'); // Sandbox API key

// Optional: Set a Private key for certain operations
NotchPay::setPrivateKey('private_key_here');

// Optional: Set a Sync ID for certain operations
NotchPay::setSyncId('sync_id_here');
```

## Payments

### Initialize a payment

```php
use NotchPay\NotchPay;
use NotchPay\Payment;

NotchPay::setApiKey('b.xxxxxxx');

try {
    $payment = Payment::initialize([
        'amount' => 5000,                // Amount according to currency format
        'email' => 'client@example.com', // Unique customer email
        'currency' => 'XAF',             // ISO currency code
        'callback' => 'https://example.com/callback', // Callback URL (optional)
        'reference' => 'order_123',      // Unique transaction reference
        'description' => 'Product purchase', // Description (optional)
        'metadata' => [                  // Metadata (optional)
            'customer_id' => '123',
            'order_id' => '456'
        ]
    ]);
    
    // Redirect user to payment URL
    header('Location: ' . $payment->authorization_url);
    exit();
} catch(\NotchPay\Exceptions\ApiException $e) {
    // Handle error
    echo $e->getMessage();
}
```

For more details on parameters, see the [official documentation](https://developer.notchpay.co/#payment-initialize).

### Verify a payment

```php
use NotchPay\NotchPay;
use NotchPay\Payment;

NotchPay::setApiKey('b.xxxxxxx');

try {
    $reference = $_GET['reference']; // Get reference from callback URL
    $payment = Payment::verify($reference);
    
    if ($payment->transaction->status === 'complete') {
        // Payment was successful
        // Deliver product or service
    } else {
        // Payment is not yet completed or failed
    }
} catch(\NotchPay\Exceptions\ApiException $e) {
    // Handle error
    echo $e->getMessage();
}
```

### List payments

```php
use NotchPay\NotchPay;
use NotchPay\Payment;

NotchPay::setApiKey('b.xxxxxxx');

try {
    $payments = Payment::all([
        'limit' => 20,           // Number of items per page (optional)
        'page' => 1,             // Page number (optional)
        'status' => 'complete',  // Filter by status (optional)
        'date_start' => '2023-01-01', // Start date (optional)
        'date_end' => '2023-12-31'    // End date (optional)
    ]);
    
    foreach ($payments->data as $payment) {
        echo $payment->reference . ' - ' . $payment->amount . ' ' . $payment->currency . "\n";
    }
} catch(\NotchPay\Exceptions\ApiException $e) {
    // Handle error
    echo $e->getMessage();
}
```

### Process a payment

```php
use NotchPay\NotchPay;
use NotchPay\Payment;

NotchPay::setApiKey('b.xxxxxxx');

try {
    $reference = 'order_123';
    $data = [
        "channel": "cm.mtn",
        "data": [
            "account_number" => "670000000"
        ]       
    ];
    $result = Payment::charge($reference, $data);
    
    if ($result->status === 'success') {
        // Payment cancelled successfully
    }
} catch(\NotchPay\Exceptions\ApiException $e) {
    // Handle error
    echo $e->getMessage();
}
```

### Cancel a payment

```php
use NotchPay\NotchPay;
use NotchPay\Payment;

NotchPay::setApiKey('b.xxxxxxx');

try {
    $reference = 'order_123';
    $result = Payment::cancel($reference);
    
    if ($result->status === 'success') {
        // Payment cancelled successfully
    }
} catch(\NotchPay\Exceptions\ApiException $e) {
    // Handle error
    echo $e->getMessage();
}
```

## Transfers

### Initialize a transfer

```php
use NotchPay\NotchPay;
use NotchPay\Transfer;

NotchPay::setApiKey('b.xxxxxxx');
NotchPay::setPrivateKey('private_key_here');

try {
    $transfer = Transfer::initialize([
        'amount' => 5000,                // Amount according to currency format
        'currency' => 'XAF',             // ISO currency code
        'recipient' => [
            'name' => 'John Doe',
            'email' => 'recipient@example.com',
            'phone' => '+237600000000',
            'account' => '237600000000', // Account or phone number
            'provider' => 'mtn_momo'     // Provider (mtn_momo, orange_money, etc.)
        ],
        'description' => 'Salary payment', // Description (optional)
        'reference' => 'transfer_123',    // Unique reference (optional)
        'metadata' => [                   // Metadata (optional)
            'employee_id' => '123'
        ]
    ]);
    
    // Process response
    echo "Transfer initialized with reference: " . $transfer->reference;
} catch(\NotchPay\Exceptions\ApiException $e) {
    // Handle error
    echo $e->getMessage();
}
```

### Verify a transfer

```php
use NotchPay\NotchPay;
use NotchPay\Transfer;

NotchPay::setApiKey('b.xxxxxxx');
NotchPay::setPrivateKey('private_key_here');

try {
    $reference = 'transfer_123';
    $transfer = Transfer::verify($reference);
    
    echo "Transfer status: " . $transfer->status;
} catch(\NotchPay\Exceptions\ApiException $e) {
    // Handle error
    echo $e->getMessage();
}
```

### List transfers

```php
use NotchPay\NotchPay;
use NotchPay\Transfer;

NotchPay::setApiKey('b.xxxxxxx');
NotchPay::setPrivateKey('private_key_here');

try {
    $transfers = Transfer::all([
        'limit' => 20,           // Number of items per page (optional)
        'page' => 1              // Page number (optional)
    ]);
    
    foreach ($transfers->data as $transfer) {
        echo $transfer->reference . ' - ' . $transfer->amount . ' ' . $transfer->currency . "\n";
    }
} catch(\NotchPay\Exceptions\ApiException $e) {
    // Handle error
    echo $e->getMessage();
}
```

## Beneficiaries

### Create a beneficiary

```php
use NotchPay\NotchPay;
use NotchPay\Beneficiary;

NotchPay::setApiKey('b.xxxxxxx');
NotchPay::setPrivateKey('private_key_here');

try {
    $beneficiary = Beneficiary::create([
        'name' => 'John Doe',
        'email' => 'john@example.com',
        'phone' => '+237600000000',
        'account' => '237600000000',
        'provider' => 'mtn_momo',
        'country' => 'CM',
        'currency' => 'XAF',
        'description' => 'Employee' // Optional
    ]);
    
    echo "Beneficiary created with ID: " . $beneficiary->id;
} catch(\NotchPay\Exceptions\ApiException $e) {
    // Handle error
    echo $e->getMessage();
}
```

### Retrieve a beneficiary

```php
use NotchPay\NotchPay;
use NotchPay\Beneficiary;
NotchPay::setPrivateKey('private_key_here');

NotchPay::setApiKey('b.xxxxxxx');

try {
    $id = 'ben_123456';
    $beneficiary = Beneficiary::retrieve($id);
    
    echo "Beneficiary name: " . $beneficiary->name;
} catch(\NotchPay\Exceptions\ApiException $e) {
    // Handle error
    echo $e->getMessage();
}
```

### Update a beneficiary

```php
use NotchPay\NotchPay;
use NotchPay\Beneficiary;

NotchPay::setApiKey('b.xxxxxxx');
NotchPay::setPrivateKey('private_key_here');

try {
    $id = 'ben_123456';
    $beneficiary = Beneficiary::update($id, [
        'name' => 'John Updated',
        'description' => 'New position'
    ]);
    
    echo "Beneficiary updated: " . $beneficiary->name;
} catch(\NotchPay\Exceptions\ApiException $e) {
    // Handle error
    echo $e->getMessage();
}
```

### List beneficiaries

```php
use NotchPay\NotchPay;
use NotchPay\Beneficiary;

NotchPay::setApiKey('b.xxxxxxx');
NotchPay::setPrivateKey('private_key_here');

try {
    $beneficiaries = Beneficiary::all([
        'limit' => 20,           // Number of items per page (optional)
        'page' => 1              // Page number (optional)
    ]);
    
    foreach ($beneficiaries->data as $beneficiary) {
        echo $beneficiary->name . ' - ' . $beneficiary->account . "\n";
    }
} catch(\NotchPay\Exceptions\ApiException $e) {
    // Handle error
    echo $e->getMessage();
}
```

### Delete a beneficiary

```php
use NotchPay\NotchPay;
use NotchPay\Beneficiary;

NotchPay::setApiKey('b.xxxxxxx');
NotchPay::setPrivateKey('private_key_here');

try {
    $id = 'ben_123456';
    $result = Beneficiary::delete($id);
    
    if ($result->status === 'success') {
        echo "Beneficiary deleted successfully";
    }
} catch(\NotchPay\Exceptions\ApiException $e) {
    // Handle error
    echo $e->getMessage();
}
```

## Customers

### Create a customer

```php
use NotchPay\NotchPay;
use NotchPay\Customer;

NotchPay::setApiKey('b.xxxxxxx');


try {
    $customer = Customer::create([
        'name' => 'John Doe',
        'email' => 'john@example.com',
        'phone' => '+237600000000',
        'metadata' => [           // Optional
            'age' => 30,
            'address' => 'Douala, Cameroon'
        ]
    ]);
    
    echo "Customer created with ID: " . $customer->id;
} catch(\NotchPay\Exceptions\ApiException $e) {
    // Handle error
    echo $e->getMessage();
}
```

### Retrieve a customer

```php
use NotchPay\NotchPay;
use NotchPay\Customer;

NotchPay::setApiKey('b.xxxxxxx');

try {
    $id = 'cus_123456';
    $customer = Customer::retrieve($id);
    
    echo "Customer name: " . $customer->name;
} catch(\NotchPay\Exceptions\ApiException $e) {
    // Handle error
    echo $e->getMessage();
}
```

### Update a customer

```php
use NotchPay\NotchPay;
use NotchPay\Customer;

NotchPay::setApiKey('b.xxxxxxx');

try {
    $id = 'cus_123456';
    $customer = Customer::update($id, [
        'name' => 'John Updated',
        'phone' => '+237611111111'
    ]);
    
    echo "Customer updated: " . $customer->name;
} catch(\NotchPay\Exceptions\ApiException $e) {
    // Handle error
    echo $e->getMessage();
}
```

### List customers

```php
use NotchPay\NotchPay;
use NotchPay\Customer;

NotchPay::setApiKey('b.xxxxxxx');

try {
    $customers = Customer::all([
        'limit' => 20,           // Number of items per page (optional)
        'page' => 1              // Page number (optional)
    ]);
    
    foreach ($customers->data as $customer) {
        echo $customer->name . ' - ' . $customer->email . "\n";
    }
} catch(\NotchPay\Exceptions\ApiException $e) {
    // Handle error
    echo $e->getMessage();
}
```

### Block a customer

```php
use NotchPay\NotchPay;
use NotchPay\Customer;

NotchPay::setApiKey('b.xxxxxxx');

try {
    $id = 'cus_123456';
    $result = Customer::block($id);
    
    if ($result->status === 'success') {
        echo "Customer blocked successfully";
    }
} catch(\NotchPay\Exceptions\ApiException $e) {
    // Handle error
    echo $e->getMessage();
}
```

### Unblock a customer

```php
use NotchPay\NotchPay;
use NotchPay\Customer;

NotchPay::setApiKey('b.xxxxxxx');

try {
    $id = 'cus_123456';
    $result = Customer::unblock($id);
    
    if ($result->status === 'success') {
        echo "Customer unblocked successfully";
    }
} catch(\NotchPay\Exceptions\ApiException $e) {
    // Handle error
    echo $e->getMessage();
}
```

### Get customer payment methods

```php
use NotchPay\NotchPay;
use NotchPay\Customer;

NotchPay::setApiKey('b.xxxxxxx');

try {
    $id = 'cus_123456';
    $paymentMethods = Customer::paymentMethods($id);
    
    foreach ($paymentMethods->data as $method) {
        echo $method->type . ' - ' . $method->last4 . "\n";
    }
} catch(\NotchPay\Exceptions\ApiException $e) {
    // Handle error
    echo $e->getMessage();
}
```

### Get customer payments

```php
use NotchPay\NotchPay;
use NotchPay\Customer;

NotchPay::setApiKey('b.xxxxxxx');

try {
    $id = 'cus_123456';
    $payments = Customer::payments($id, [
        'limit' => 10,
        'page' => 1
    ]);
    
    foreach ($payments->data as $payment) {
        echo $payment->reference . ' - ' . $payment->amount . ' ' . $payment->currency . "\n";
    }
} catch(\NotchPay\Exceptions\ApiException $e) {
    // Handle error
    echo $e->getMessage();
}
```

## Webhooks

### Create a webhook

```php
use NotchPay\NotchPay;
use NotchPay\Webhook;

NotchPay::setApiKey('b.xxxxxxx');
NotchPay::setPrivateKey('private_key_here');

try {
    $webhook = Webhook::create([
        'url' => 'https://example.com/webhooks',
        'events' => ['payment.complete', 'payment.failed'],
        'description' => 'Webhook for payments', // Optional
        'active' => true // Optional
    ]);
    
    echo "Webhook created with ID: " . $webhook->id;
} catch(\NotchPay\Exceptions\ApiException $e) {
    // Handle error
    echo $e->getMessage();
}
```

### Retrieve a webhook

```php
use NotchPay\NotchPay;
use NotchPay\Webhook;

NotchPay::setApiKey('b.xxxxxxx');
NotchPay::setPrivateKey('private_key_here');

try {
    $id = 'wh_123456';
    $webhook = Webhook::retrieve($id);
    
    echo "Webhook URL: " . $webhook->url;
} catch(\NotchPay\Exceptions\ApiException $e) {
    // Handle error
    echo $e->getMessage();
}
```

### Update a webhook

```php
use NotchPay\NotchPay;
use NotchPay\Webhook;

NotchPay::setApiKey('b.xxxxxxx');
NotchPay::setPrivateKey('private_key_here');

try {
    $id = 'wh_123456';
    $webhook = Webhook::update($id, [
        'events' => ['payment.complete', 'payment.failed', 'transfer.complete'],
        'active' => true
    ]);
    
    echo "Webhook updated: " . $webhook->url;
} catch(\NotchPay\Exceptions\ApiException $e) {
    // Handle error
    echo $e->getMessage();
}
```

### List webhooks

```php
use NotchPay\NotchPay;
use NotchPay\Webhook;

NotchPay::setApiKey('b.xxxxxxx');
NotchPay::setPrivateKey('private_key_here');

try {
    $webhooks = Webhook::all();
    
    foreach ($webhooks->data as $webhook) {
        echo $webhook->url . ' - ' . ($webhook->active ? 'Active' : 'Inactive') . "\n";
    }
} catch(\NotchPay\Exceptions\ApiException $e) {
    // Handle error
    echo $e->getMessage();
}
```

### Delete a webhook

```php
use NotchPay\NotchPay;
use NotchPay\Webhook;

NotchPay::setApiKey('b.xxxxxxx');
NotchPay::setPrivateKey('private_key_here');

try {
    $id = 'wh_123456';
    $result = Webhook::delete($id);
    
    if ($result->status === 'success') {
        echo "Webhook deleted successfully";
    }
} catch(\NotchPay\Exceptions\ApiException $e) {
    // Handle error
    echo $e->getMessage();
}
```

## Balance

### Check account balance

```php
use NotchPay\NotchPay;
use NotchPay\Balance;

NotchPay::setApiKey('b.xxxxxxx');
NotchPay::setPrivateKey('private_key_here');

try {
    $balance = Balance::check();
    
    echo "Available balance: " . $balance->available . " " . $balance->currency . "\n";
    echo "Pending balance: " . $balance->pending . " " . $balance->currency;
} catch(\NotchPay\Exceptions\ApiException $e) {
    // Handle error
    echo $e->getMessage();
}
```

## Payment Channels

### List payment channels

```php
use NotchPay\NotchPay;
use NotchPay\Channel;

NotchPay::setApiKey('b.xxxxxxx');

try {
    $channels = Channel::all();
    
    foreach ($channels->data as $channel) {
        echo $channel->name . ' - ' . $channel->code . "\n";
    }
} catch(\NotchPay\Exceptions\ApiException $e) {
    // Handle error
    echo $e->getMessage();
}
```

### Retrieve a payment channel

```php
use NotchPay\NotchPay;
use NotchPay\Channel;

NotchPay::setApiKey('b.xxxxxxx');

try {
    $code = 'mtn_momo';
    $channel = Channel::retrieve($code);
    
    echo "Channel name: " . $channel->name;
} catch(\NotchPay\Exceptions\ApiException $e) {
    // Handle error
    echo $e->getMessage();
}
```

## Countries

### List countries

```php
use NotchPay\NotchPay;
use NotchPay\Country;

NotchPay::setApiKey('b.xxxxxxx');

try {
    $countries = Country::all();
    
    foreach ($countries->data as $country) {
        echo $country->name . ' - ' . $country->code . "\n";
    }
} catch(\NotchPay\Exceptions\ApiException $e) {
    // Handle error
    echo $e->getMessage();
}
```

## Currencies

### List currencies

```php
use NotchPay\NotchPay;
use NotchPay\Currency;

NotchPay::setApiKey('b.xxxxxxx');

try {
    $currencies = Currency::all();
    
    foreach ($currencies->data as $currency) {
        echo $currency->name . ' - ' . $currency->code . "\n";
    }
} catch(\NotchPay\Exceptions\ApiException $e) {
    // Handle error
    echo $e->getMessage();
}
```

## Error Handling

The library throws different exceptions that you can catch to handle errors:

```php
try {
    // Your NotchPay code here
} catch(\NotchPay\Exceptions\ApiException $e) {
    // API errors (validation errors, server errors, etc.)
    echo "API Error: " . $e->getMessage();
    print_r($e->errors); // Error details
} catch(\NotchPay\Exceptions\InvalidArgumentException $e) {
    // Invalid argument errors
    echo "Invalid Argument: " . $e->getMessage();
} catch(\NotchPay\Exceptions\NotchPayException $e) {
    // Other NotchPay errors
    echo "NotchPay Error: " . $e->getMessage();
}
```

## Official Documentation

For more information on API parameters and responses, see the [official NotchPay documentation](https://developer.notchpay.co/).

## Changelog

Please see [CHANGELOG](CHANGELOG.md) for more information on what has changed recently.

## Contributing

Please see [CONTRIBUTING](CONTRIBUTING.md) for details.

## Security

If you discover any security related issues, please email hello@notchpay.co instead of using the issue tracker.

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.
