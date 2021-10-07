# Uber PHP Client






Via Composer

``` bash
$ composer require stevenmaguire/uber-php
```



## Usage

### Create client

```php
$client = new Stevenmaguire\Uber\Client(array(
    'access_token' => 'YOUR ACCESS TOKEN',
    'server_token' => 'YOUR SERVER TOKEN',
    'use_sandbox'  => true, // optional, default false
    'version'      => 'v1.2', // optional, default 'v1.2'
    'locale'       => 'en_US', // optional, default 'en_US'
));
```

### Get Products

#### By location:

```php
$products = $client->getProducts(array(
    'latitude' => '41.85582993',
    'longitude' => '-87.62730337'
));
```


#### By Id:

```php
$product = $client->getProduct($productId);
```


### Get Price Estimates

```php
$estimates = $client->getPriceEstimates(array(
    'start_latitude' => '41.85582993',
    'start_longitude' => '-87.62730337',
    'end_latitude' => '41.87499492',
    'end_longitude' => '-87.67126465'
));
```


### Get Time Estimates

```php
$estimates = $client->getTimeEstimates(array(
    'start_latitude' => '41.85582993',
    'start_longitude' => '-87.62730337'
));
```


### Get Promotions

```php
$promotions = $client->getPromotions(array(
    'start_latitude' => '41.85582993',
    'start_longitude' => '-87.62730337',
    'end_latitude' => '41.87499492',
    'end_longitude' => '-87.67126465'
));
```


### Get User Activity

This feature is only available since version `1.1`.

```php
$client->setVersion('v1.2'); // or v1.1
$history = $client->getHistory(array(
    'limit' => 50, // optional
    'offset' => 0 // optional
));
```


### Get User Profile

```php
$profile = $client->getProfile();
```


### Update User Profile

```php
$attributes = array('applied_promotion_codes' => 'PROMO_CODE');
$profileResponse = $client->setProfile($attributes);
```


### Get Payment Methods

```php
$paymentMethods = $client->getPaymentMethods();
```


### Get Place

```php
$placeId = 'home';
$place = $client->getPlace($placeId);
```


### Update a Place

```php
$placeId = 'home';
$attributes = array('address' => '685 Market St, San Francisco, CA 94103, USA');
$place = $client->setPlace($placeId, $attributes);
```


### Request A Ride

```php
$request = $client->requestRide(array(
    'start_latitude' => '41.85582993',
    'start_longitude' => '-87.62730337',
    'end_latitude' => '41.87499492',
    'end_longitude' => '-87.67126465',
    'product_id' => '4bfc6c57-98c0-424f-a72e-c1e2a1d49939', // Optional
    'surge_confirmation_id' => 'e100a670',                  // Optional
    'payment_method_id' => 'a1111c8c-c720-46c3-8534-2fcd'   // Optional
));
```

#### Upfront Fares






#### Surge Confirmation Flow


```php
try {
    $request = $client->requestRide(array(
        'product_id' => '4bfc6c57-98c0-424f-a72e-c1e2a1d49939',
        'start_latitude' => '41.85582993',
        'start_longitude' => '-87.62730337',
        'end_latitude' => '41.87499492',
        'end_longitude' => '-87.67126465'
    ));
} catch (Stevenmaguire\Uber\Exception $e) {
    $body = $e->getBody();
    $surgeConfirmationId = $body['meta']['surge_confirmation']['surge_confirmation_id'];
}
```


### Get Current Ride Details

```php
$request = $client->getCurrentRequest();
```


### Get Ride Details

```php
$request = $client->getRequest($requestId);
```


### Update Current Ride Details

```php
$requestDetails = array(
    'end_address' => '685 Market St, San Francisco, CA 94103, USA',
    'end_nickname' => 'da crib',
    'end_place_id' => 'home',
    'end_latitude' => '41.87499492',
    'end_longitude' => '-87.67126465'
);

$updateRequest = $client->setCurrentRequest($requestDetails);
```


### Update Ride Details

```php
$requestId = '4bfc6c57-98c0-424f-a72e-c1e2a1d49939'
$requestDetails = array(
    'end_address' => '685 Market St, San Francisco, CA 94103, USA',
    'end_nickname' => 'da crib',
    'end_place_id' => 'home',
    'end_latitude' => '41.87499492',
    'end_longitude' => '-87.67126465'
);

$updateRequest = $client->setRequest($requestId, $requestDetails);
```


### Get Ride Estimate

```php
$requestEstimate = $client->getRequestEstimate(array(
    'product_id' => '4bfc6c57-98c0-424f-a72e-c1e2a1d49939',
    'start_latitude' => '41.85582993',
    'start_longitude' => '-87.62730337',
    'end_latitude' => '41.87499492', // optional
    'end_longitude' => '-87.67126465', // optional
));
```


### Get Ride Map

```php
$map = $client->getRequestMap($requestId);
```


### Get Ride Receipt

```php
$receipt = $client->getRequestReceipt($requestId);
```


### Cancel Current Ride

```php
$request = $client->cancelCurrentRequest();
```


### Cancel Ride

```php
$request = $client->cancelRequest($requestId);
```


### Create Reminder

```php
$attributes = array(
    'reminder_time' => '1429294463',
    'phone_number' => '555-555-5555',
    'event' => array(
        'time' => '1429294463',
        'name' => 'Frisbee with friends',
        'location' => 'Dolores Park',
        'latitude' => '37.759773',
        'longitude' => '-122.427063',
    ),
    'product_id' => 'a1111c8c-c720-46c3-8534-2fcdd730040d',
    'trip_branding' => array(
        'link_text' => 'View team roster',
        'partner_deeplink' => 'partner://team/9383',
    )
);
$reminder = $client->createReminder($attributes);
```


### Get Reminder

```php
$reminderId = '4bfc6c57-98c0-424f-a72e-c1e2a1d49939';
$reminder = $client->getReminder($reminderId);
```


### Update Reminder

```php
$reminderId = '4bfc6c57-98c0-424f-a72e-c1e2a1d49939';
$attributes = array(
    'reminder_time' => '1429294463',
    'phone_number' => '555-555-5555',
    'event' => array(
        'time' => '1429294463',
        'name' => 'Frisbee with friends',
        'location' => 'Dolores Park',
        'latitude' => '37.759773',
        'longitude' => '-122.427063',
    ),
    'product_id' => 'a1111c8c-c720-46c3-8534-2fcdd730040d',
    'trip_branding' => array(
        'link_text' => 'View team roster',
        'partner_deeplink' => 'partner://team/9383',
    )
);
$reminder = $client->setReminder($reminderId, $attributes);
```


### Cancel Reminder

```php
$reminderId = '4bfc6c57-98c0-424f-a72e-c1e2a1d49939';
$reminder = $client->cancelReminder($reminderId);
```


### Get Driver Profile

```php
$profile = $client->getDriverProfile();
```


### Get Driver Payments

```php
$profile = $client->getDriverPayments(array(
    'limit' => 50, // optional
    'offset' => 0 // optional
));
```


### Get Driver Trips

```php
$profile = $client->getDriverTrips(array(
    'limit' => 50, // optional
    'offset' => 0 // optional
));
```


### Rate Limiting



```php
$product = $client->getProduct($productId);

$rateLimit = $client->getRateLimit();

$rateLimit->getLimit();        // Rate limit capacity per period
$rateLimit->getRemaining();    // Requests remaining in current period
$rateLimit->getReset();        // Timestamp in UTC time when the next period will begin
```


### Using the Sandbox



```php
$request = $client->requestRide(array(
    'product_id' => '4bfc6c57-98c0-424f-a72e-c1e2a1d49939',
    'start_latitude' => '41.85582993',
    'start_longitude' => '-87.62730337',
    'end_latitude' => '41.87499492',
    'end_longitude' => '-87.67126465'
));

$updateRequest = $client->setSandboxRequest($request->request_id, array('status' => 'accepted'));
```


```php
$product = $client->getProduct($productId);

$updateProduct = $client->setSandboxProduct($productId, array('surge_multiplier' => 2.2, 'drivers_available' => false));
```


## Testing

``` bash
$ ./vendor/bin/phpunit
```

