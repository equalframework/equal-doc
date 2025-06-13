# Sneak Peek Example

This example demonstrates a basic controller and how to run it via HTTP or the CLI.

## Controller

`/packages/demo/data/simple.php`:

```php
<?php
/**
 * (near) Zero learning-curve development
 */
echo 'This is a valid HTTP controller';
```

To reuse existing components:

`/packages/demo/data/reuse.php`:

```php
<?php
/**
 * Code re-usability
 */
echo run('get', 'demo_simple');
```

## HTTP request

`wget http://localhost/index.php?get=demo_simple`

> Response:
> Header excerpt:
> Status Code: 200 OK
> Content-Length: 26
> Content-Type: text/html; charset=UTF-8
> Body:
> This is a valid controller!

## CLI

```bash
$ ./equal.run --get=demo_simple
```

> This is a valid controller!

## Real life action

```bash
$ ./equal.run --get=demo_image
```

`/packages/demo/data/image.php`:

```php
<?php
use equal\http\HttpRequest;

/**
 * HTTP native support
 */
list($params, $providers) = eQual::announce([
    'description'   => 'Get picture data from imgur.com using imgur API.',
    'params'        => [
        'id' => [
            'description'   => 'Hash of the image to retrieve',
            'type'          => 'string',
            'default'       => 'a5NE1kW'
        ]
    ],
    'response'      => [
        'content-type'  => 'application/json',
        'charset'       => 'utf-8'
    ],
    'providers'     => ['context']
]);

$request = new HttpRequest("GET https://api.imgur.com/3/image/{$params['id']}");

$response = $request
    ->header('Authorization', "Client-ID 34030ab1f5ef12d")
    ->send();

$providers['context']
    ->httpResponse()
    ->body(['result' => $response->body()])
    ->send();
```
