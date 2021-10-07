# Uber PHP Client




A PHP client for authenticating with Uber using OAuth 2.0 and consuming the API.

*This package is intended to be used for communicating with the Uber API after you've secured an access token from your users. To authenticate users and retrieve access tokens.*

## Install

Via Composer

``` bash
$ composer require stevenmaguire/uber-php
```

> Note that the required version of PHP is 5.5. If you want use library with PHP 5.4 you should use 1.2.0 version.

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
*Please review the documentation on how to develop and test against these endpoints without making real-world Requests and being charged.*

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

Upfront fares means the total fare is known before the ride is taken.

- An end location is required
- There is no surge confirmation flow
- The user should specify a fare_id to confirm consent to the upfront fare
- The user should specify the number of seats that are required for shared products (like UberPOOL)

1. In the products endpoint `GET /products`, products will have the `upfront_fare_enabled` field set to `true`.
2. Use the ride request estimate endpoint `POST /requests/estimate` with the `product_id` to get a `fare_id`. The `fare_id` can be used to lock down an upfront fare and arrival time for a trip. The `fare_id` expires after two minutes. If the `fare_id` is expired or not valid, we return a 422 error.
3. Request the ride using the ride request endpoint `POST /requests` with the `fare_id` returned in the previous step.


#### Surge Confirmation Flow

If the ride request is using a product that has a surge multiplier, the API wrapper will throw an Exception and provide a response body that includes a surge confirmation ID.

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

> This feature is only supported for `v1` version of the API.

Rate limiting is implemented on the basis of a specific client's secret token. By default, 1,000 requests per hour can be made per secret token.

When consuming the service with this package, your rate limit status will be made available within the client.

```php
$product = $client->getProduct($productId);

$rateLimit = $client->getRateLimit();

$rateLimit->getLimit();        // Rate limit capacity per period
$rateLimit->getRemaining();    // Requests remaining in current period
$rateLimit->getReset();        // Timestamp in UTC time when the next period will begin
```
These values will update after each request. `getRateLimit` will return null after the client is created and before the first successful request.


### Using the Sandbox

Modify the status of an ongoing sandbox Request.

> These methods will throw `Stevenmaguire\Uber\Exception` when invoked while the client is not in sandbox mode. The underlying API endpoints have no effect unless you are using the sandbox environment.

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

Simulate the possible responses the Request endpoint will return when requesting a particular product, such as surge pricing, against the Sandbox.

```php
$product = $client->getProduct($productId);

$updateProduct = $client->setSandboxProduct($productId, array('surge_multiplier' => 2.2, 'drivers_available' => false));
```


## Testing

``` bash
$ ./vendor/bin/phpunit
```

## Contributing

Please see [CONTRIBUTING](CONTRIBUTING.md) for details.

## Credits

- [Steven Maguire]
- [All Contributors]

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.
