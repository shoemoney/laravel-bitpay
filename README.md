# Laravel + BitPay Integration

[![Latest Version on Packagist](https://img.shields.io/packagist/v/vrajroham/laravel-bitpay.svg?style=for-the-badge)](https://packagist.org/packages/vrajroham/laravel-bitpay)
[![Build Status](https://img.shields.io/travis/vrajroham/laravel-bitpay/master.svg?style=for-the-badge)](https://travis-ci.org/vrajroham/laravel-bitpay)
[![Quality Score](https://img.shields.io/scrutinizer/g/vrajroham/laravel-bitpay.svg?style=for-the-badge)](https://scrutinizer-ci.com/g/vrajroham/laravel-bitpay)
[![Total Downloads](https://img.shields.io/packagist/dt/vrajroham/laravel-bitpay.svg?style=for-the-badge)](https://packagist.org/packages/vrajroham/laravel-bitpay)

Accept Bitcoin and Bitcoin Cash for your business with your Laravel application and BitPay client.

> **Note** : Due to upstream dependency, this package cannot be used for Laravel > 6.* until the dependency is resolve from the upstream package [bitpay/php-bitpay-client](https://github.com/bitpay/php-bitpay-client).

## Installation

You can install the package via composer:

```bash
composer require vrajroham/laravel-bitpay
```
Publish config file with:

```bash
php artisan vendor:publish --provider="Vrajroham\LaravelBitpay\LaravelBitpayServiceProvider"
```
## Steps to configure and pair with BitPay Server

- Add following keys to `.env` file and updated the details ([view more about configuration](https://support.bitpay.com/hc/en-us/articles/115003001063-How-do-I-configure-the-PHP-BitPay-Client-Library-)):

    ```dotenv
    BITPAY_PRIVATE_KEY_PATH=/tmp/bitpay.pri
    BITPAY_PUBLIC_KEY_PATH=/tmp/bitpay.pub
    BITPAY_NETWORK=testnet
    BITPAY_KEY_STORAGE_PASSWORD=SomeRandomePasswordForKeypairEncryption
    BITPAY_TOKEN=
    ``` 

- Create [Test Account](http://test.bitpay.com/)(for devlopement) or [Live Account](http://bitpay.com/) on BitPay.

- Create [new token for application](https://test.bitpay.com/dashboard/merchant/api-tokens)(test account) or [new token for application](https://bitpay.com/dashboard/merchant/api-tokens) on BitPay and **copy newly generated 7 character Pairing Code**.

- Create keypairs and pair your client(application) with BitPay server.

    ```bash
    php artisan laravel-bitpay:createkeypair yourPairingCode
    ```
    
    <p align="center"><a href="https://preview.ibb.co/gaJ1DJ/laravel_bitpay_3.png"><img src="https://preview.ibb.co/gaJ1DJ/laravel_bitpay_3.png" alt="laravel_bitpay_1" border="0"></a></p>
Above command will **generate private key, public key** and save to your specified location. Next it will pair your client i.e. application with BitPay server referring your provided **pairing code** and create new token/key and will be saved in `.env`.

- Done. :golf:

## Usage

##### Create Invoice and checkout
``` php
public function createInvoice()
{
    // Create instance of invoice
    $invoice = LaravelBitpay::Invoice();

    // Create instance of item
    $item = LaravelBitpay::Item();

    // Set item details
    $item->setCode('124')
        ->setDescription('Item 1')
        ->setPrice('1.99');

    // Add item to invoice. (Only one item can be added)
    $invoice->setItem($item);

    // Order reference number from the point-of-sale (POS). 
    // It should be a unique identifier for each order that you submit. 
    $invoice->setPosData(uniqid()); // Optional

    // Create buyer instance
    $buyer = LaravelBitpay::Buyer();

    // Add buyer details
    $buyer->setFirstName('Vaibhav')
        ->setLastName('Roham');

    // Add buyer to invoice
    $invoice->setBuyer($buyer);

    // Set currency for this invoice
    $invoice->setCurrency(LaravelBitpay::Currency('USD'));

    // Create invoice on BitPay server
    $invoice = LaravelBitpay::createInvoice($invoice);

    // Redirect URL on success
    $invoice->setRedirectUrl(route('bitpay-redirect-back'));

    // Webhook URL to receive various events
    $invoice->setNotificationUrl(route('bitpay-webhook'));

    // Redirect user to following URL for payment approval. 
    // Or you can create stripe like checkout from https://bitpay.com/create-checkout
    $paymentUrl = $invoice->getUrl();
}
```

 <p align="center"><a href="https://preview.ibb.co/jxMzFy/laravel_bitpay_2.png"><img src="https://image.ibb.co/jxMzFy/laravel_bitpay_2.png" alt="laravel_bitpay_2" border="0"></a></p>

----

### Testing

``` bash
composer test
```

### Changelog

Please see [CHANGELOG](CHANGELOG.md) for more information on what has changed recently.

## Contributing

Please see [CONTRIBUTING](CONTRIBUTING.md) for details.

### Security

If you discover any security related issues, please email vaibhavraj@vrajroham.me instead of using the issue tracker.

## Credits

- [Vaibhavraj Roham](https://github.com/vrajroham)
- [All Contributors](../../contributors)

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.
