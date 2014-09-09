stack-request-id
=====
Middleware for adding a request id to your Symfony Requests

[![Build Status](https://travis-ci.org/qandidate-labs/stack-request-id.svg?branch=master)](https://travis-ci.org/qandidate-labs/stack-request-id)

## Installation
First, add this project to your project's composer.json

```
$ composer require qandidate/stack-request-id ~0.1.0
```

## Setting up
Update your `app.php` to include the middleware:

Before:
```
use Symfony\Component\HttpFoundation\Request;

$kernel = new AppKernel($env, $debug);
$kernel->loadClassCache();

$request = Request::createFromGlobals();
$response = $kernel->handle($request);
$response->send();
$kernel->terminate($request, $response);
```

After:
```
use Qandidate\Stack\RequestId;
use Qandidate\Stack\UuidRequestIdGenerator;
use Symfony\Component\HttpFoundation\Request;

$kernel = new AppKernel($env, $debug);

// Stack it!
$generator = new UuidRequestIdGenerator(1337);
$stack = new RequestId($kernel, $generator);

$kernel->loadClassCache();

$request = Request::createFromGlobals();
$response = $stack->handle($request);
$response->send();
$kernel->terminate($request, $response);
```

## Adding the request id to your monolog logs
If you use Symfony's [MonologBundle] you can add the request id to your monolog logs by adding the following service definition to your services.xml file:

```XML
<service id="qandidate.stack.request_id.monolog_processor" class="Qandidate\Stack\RequestId\MonologProcessor">
  <tag name="kernel.event_listener" event="kernel.request" method="onKernelRequest" priority="255" />
  <tag name="monolog.processor" />
</service>

```

[MonologBundle]: https://github.com/symfony/MonologBundle
