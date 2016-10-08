ProxmoxVE API Client
====================

This **PHP 5.4+** library allows you to interact with your Proxmox server via API.

[![Build Status](https://travis-ci.org/ZzAntares/ProxmoxVE.svg?branch=master)](https://travis-ci.org/ZzAntares/ProxmoxVE)
[![Latest Stable Version](https://poser.pugx.org/zzantares/proxmoxve/v/stable.svg)](https://packagist.org/packages/zzantares/proxmoxve)
[![Total Downloads](https://poser.pugx.org/zzantares/proxmoxve/downloads.svg)](https://packagist.org/packages/zzantares/proxmoxve)
[![Latest Unstable Version](https://poser.pugx.org/zzantares/proxmoxve/v/unstable.svg)](https://packagist.org/packages/zzantares/proxmoxve)
[![License](https://poser.pugx.org/zzantares/proxmoxve/license.svg)](https://packagist.org/packages/zzantares/proxmoxve)

> If you find any errors, typos or you detect that something is not working as expected please open an [issue](https://github.com/ZzAntares/ProxmoxVE/issues/new) or tweetme [@ZzAntares](https://twitter.com/ZzAntares). I'll try to release a fix asap.

**looking for a PHP 5.3 library version?** Search through the [releases](https://github.com/ZzAntares/ProxmoxVE/releases) one that fits your needs, I recommend using the [2.1.1](https://github.com/ZzAntares/ProxmoxVE/releases/tag/v2.1.1) version.

Installation
------------

Recommended installation is using [Composer], if you do not have [Composer] what are you waiting?

In the root of your project execute the following:

```sh
$ composer require zzantares/proxmoxve ~3.0
```

Or add this to your `composer.json` file:

```json
{
    "require": {
        "zzantares/proxmoxve": "~3.0"
    }
}
```

Then perform the installation:
```sh
$ composer install --no-dev
```

Usage
-----

```php
<?php

// Require the autoloader
require_once 'vendor/autoload.php';

// Use the library namespace
use ProxmoxVE\Proxmox;

// Create your credentials array
$credentials = [
    'hostname' => 'proxmox.server.com',  // Also can be an IP
    'username' => 'root',
    'password' => 'secret',
];

// realm and port defaults to 'pam' and '8006' but you can specify them like so
$credentials = [
    'hostname' => 'proxmox.server.com',
    'username' => 'root',
    'password' => 'secret',
    'realm' => 'pve',
    'port' => '9009',
];

// Then simply pass your credentials when creating the API client object.
$proxmox = new Proxmox($credentials);

$allNodes = $proxmox->get('/nodes');

print_r($allNodes);
```


Sample output:

```php
Array
(
    [data] => Array
        (
            [0] => Array
                (
                    [disk] => 2539465464
                    [cpu] => 0.031314446882002
                    [maxdisk] => 30805066770
                    [maxmem] => 175168446464
                    [node] => mynode1
                    [maxcpu] => 24
                    [level] =>
                    [uptime] => 139376
                    [id] => node/mynode1
                    [type] => node
                    [mem] => 20601992182
                )

        )

)
```

Using custom credentials object
-------------------------------

Also is possible to create a ProxmoxVE instance passing a custom object that has all related data needed to connect to the Proxmox server:

```php
<?php
// Once again require the autoloader
require_once 'vendor/autoload.php';

// Sample custom credentials class
class CustomCredentials
{
    public function __construct($host, $user, $pass)
    {
        $this->hostname = $host;
        $this->username = $user;
        $this->password = $pass;
    }
}

// Create ProxmoxVE instance by passing your custom credentials object
$credentials = new CustomCredentials('proxmox.server.com', 'root', 'secret');
$proxmox = new ProxmoxVE\Proxmox($credentials);

// Then you can use it, for example create a new user.

// Define params
$params = [
    'userid' => 'new_user@pve',  // Proxmox requires to specify the realm (see the docs)
    'comment' => 'Creating a new user',
    'password' => 'canyoukeepasecret?',
];

// Send request passing params
$result = $proxmox->create('/access/users', $params);

// If an error occurred the 'errors' key will exist in the response array
if (isset($result['errors'])) {
    error_log('Unable to create new proxmox user.');
    foreach ($result['errors'] as $title => $description) {
        error_log($title . ': ' . $description);
    }
} else {
    echo 'Successful user creation!';
}
```

Using a custom credentials object is useful when your application uses some *ORM models* with the connecting data inside them, so you can pass for example an *Eloquent* model that holds the credentials inside.


Docs
----

On your proxmox client object you can use `get()`, `create()`, `set()` and `delete()` functions for all resources specified at [PVE2 API Documentation], params are passed as the second parameter in an associative array.

**What resources or paths can I interact with and how?**

In your proxmox server you can use the [pvesh CLI Tool](http://pve.proxmox.com/wiki/Proxmox_VE_API#Using_.27pvesh.27_to_access_the_API) to manage all the pve resources, you can use this library in the exact same way you would use the pvesh tool. For instance you could run `pvesh` then, as the screen message should say, you can type `help [path] [--verbose]` to see how you could use a path and what params you should pass to it. Be sure to [read about the pvesh CLI Tool](http://pve.proxmox.com/wiki/Proxmox_VE_API#Using_.27pvesh.27_to_access_the_API) at the [Proxmox wiki].

**How does the Proxmox API works?**

Consult the [ProxmoxVE API] article at the [Proxmox wiki].

**I feel you're hiding some cool tricks! I need more docs!**

See the [doc](https://github.com/ZzAntares/ProxmoxVE/tree/master/doc) directory for more detailed documentation, it's all in there I swear it... I think.


License
-------

This project is released under the MIT License. See the bundled [LICENSE] file for details.


Donations
---------

If you feel guilty by using this open source software library for free, you can make a donation:

[![Bitcoin](https://lh6.googleusercontent.com/-otZw6Z5QKfQ/U6tqGsLBWYI/AAAAAAAAB-8/kvXncUJKKpU/w100-h36-no/bitcoin_accepted_here2.png)](https://blockchain.info/address/12g5CxCnWTc2dfo2EozCvfhi4QaFRj1vsX)

> 12g5CxCnWTc2dfo2EozCvfhi4QaFRj1vsX


[![Litecoin](https://lh3.googleusercontent.com/-s0Z7VSNScBU/U6tqGnAU5DI/AAAAAAAAB-0/fqaowxhOoBg/w100-h36-no/litecoin-accepted2.jpg)](http://ltc.blockr.io/address/info/LZuAcxLX4CBCPfLRtixatwRh234jNm3EyS)

> LZuAcxLX4CBCPfLRtixatwRh234jNm3EyS


[![PayPal](https://lh5.googleusercontent.com/-bQoXNrSpb8M/U6tqGqAFQUI/AAAAAAAAB-4/OkjaZ2ZQCUc/w100-h26-no/pp-logo-100px.png)](https://www.paypal.com/mx/cgi-bin/webscr?cmd=%5fsend%2dmoney&nav=1)

> zzantares@gmail.com


[![Fork me on GitHub](https://lh4.googleusercontent.com/-02ApStd24fs/U6t2PxxAQVI/AAAAAAAACAE/koI2tECOE60/w81-h32-no/GitHubForkButton.png)](https://github.com/ZzAntares/ProxmoxVE/fork)

> Fork and code! There is no better donation that your code contribution ;)


[![@ZzAntares](https://lh4.googleusercontent.com/-y60LB84GXNk/U-gGlzNHUGI/AAAAAAAACDs/cuhszQOezYE/s50-no/twitter-logo.png)](https://twitter.com/ZzAntares)

> Or just cheer me up [@ZzAntares](https://twitter.com/ZzAntares).

Upcoming features
----

- SSL support.
- Save AuthToken data in serialized form to avoid ticket creation per request.
- Use an abstracted OOP layer to access all Proxmox resources.
- Code useful tests ¬_¬!

[LICENSE]:./LICENSE
[PVE2 API Documentation]:http://pve.proxmox.com/pve2-api-doc/
[ProxmoxVE API]:http://pve.proxmox.com/wiki/Proxmox_VE_API
[Proxmox wiki]:http://pve.proxmox.com/wiki
[Composer]:https://getcomposer.org/
