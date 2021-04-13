# Getting Started with MdNotesCCG

## Getting Started

### Introduction

API for Markdown Notes app.

### Building

The generated code has dependencies over external libraries like UniRest. These dependencies are defined in the `composer.json` file that comes with the SDK. To resolve these dependencies, we use the Composer package manager which requires PHP greater than 5.3.2 installed in your system. Visit [https://getcomposer.org/download/](https://getcomposer.org/download/) to download the installer file for Composer and run it in your system. Open command prompt and type `composer --version`. This should display the current version of the Composer installed if the installation was successful.

* Using command line, navigate to the directory containing the generated files (including `composer.json`) for the SDK.
* Run the command `composer install`. This should install all the required dependencies and create the `vendor` directory in your project directory.

![Building SDK - Step 1](https://apidocs.io/illustration/php?workspaceFolder=MdNotesCCG&step=installDependencies)

#### Configuring CURL Certificate Path in php.ini

:information_source: **Note** This is for Windows users only.

CURL used to include a list of accepted CAs, but no longer bundles ANY CA certs. So by default it will reject all SSL certificates as unverifiable. You will have to get your CA's cert and point curl at it. The steps are as follows:

1. Download the certificate bundle (.pem file) from [https://curl.haxx.se/docs/caextract.html](https://curl.haxx.se/docs/caextract.html) on to your system.
2. Add curl.cainfo = "PATH_TO/cacert.pem" to your php.ini file located in your php installation. “PATH_TO” must be an absolute path containing the .pem file.

```
[curl]; A default value for the CURLOPT_CAINFO option. This is required to be an
; absolute path.
curl.cainfo = PATH_TO/cacert.pem
```

### Installation

The following section explains how to use the MdNotesCCGLib library in a new project.

#### 1. Open Project in an IDE

Open an IDE for PHP like PhpStorm. The basic workflow presented here is also applicable if you prefer using a different editor or IDE.

![Open project in PHPStorm - Step 1](https://apidocs.io/illustration/php?workspaceFolder=MdNotesCCG&step=openIDE)

Click on `Open` in PhpStorm to browse to your generated SDK directory and then click `OK`.

![Open project in PHPStorm - Step 2](https://apidocs.io/illustration/php?workspaceFolder=MdNotesCCG&step=openProject0)

#### 2. Add a new Test Project

Create a new directory by right clicking on the solution name as shown below:

![Add a new project in PHPStorm - Step 1](https://apidocs.io/illustration/php?workspaceFolder=MdNotesCCG&step=createDirectory)

Name the directory as "test".

![Add a new project in PHPStorm - Step 2](https://apidocs.io/illustration/php?workspaceFolder=MdNotesCCG&step=nameDirectory)

Add a PHP file to this project.

![Add a new project in PHPStorm - Step 3](https://apidocs.io/illustration/php?workspaceFolder=MdNotesCCG&step=createFile)

Name it "testSDK".

![Add a new project in PHPStorm - Step 4](https://apidocs.io/illustration/php?workspaceFolder=MdNotesCCG&step=nameFile)

Depending on your project setup, you might need to include composer's autoloader in your PHP code to enable auto loading of classes.

```php
require_once "vendor/autoload.php";
```

It is important that the path inside require_once correctly points to the file `autoload.php` inside the vendor directory created during dependency installations.

![Add a new project in PHPStorm - Step 5](https://apidocs.io/illustration/php?workspaceFolder=MdNotesCCG&step=projectFiles)

After this you can add code to initialize the client library and acquire the instance of a Controller class. Sample code to initialize the client library and use the Controller methods is given in the subsequent sections.

#### 3. Run the Test Project

To run your project you must set the Interpreter for your project. Interpreter is the PHP engine installed on your computer.

Open `Settings` from `File` menu.

![Run Test Project - Step 1](https://apidocs.io/illustration/php?workspaceFolder=MdNotesCCG&step=openSettings)

Select `PHP` from within `Languages & Frameworks`.

![Run Test Project - Step 2](https://apidocs.io/illustration/php?workspaceFolder=MdNotesCCG&step=setInterpreter0)

Browse for Interpreters near the `Interpreter` option and choose your interpreter.

![Run Test Project - Step 3](https://apidocs.io/illustration/php?workspaceFolder=MdNotesCCG&step=setInterpreter1)

Once the interpreter is selected, click `OK`.

![Run Test Project - Step 4](https://apidocs.io/illustration/php?workspaceFolder=MdNotesCCG&step=setInterpreter2)

To run your project, right click on your PHP file inside your Test project and click on `Run`.

![Run Test Project - Step 5](https://apidocs.io/illustration/php?workspaceFolder=MdNotesCCG&step=runProject)

### Initialize the API Client

The following parameters are configurable for the API Client:

| Parameter | Type | Description |
|  --- | --- | --- |
| `oAuthClientId` | `string` | OAuth 2 Client ID |
| `oAuthClientSecret` | `string` | OAuth 2 Client Secret |
| `environment` | Environment | The API environment. <br> **Default: `Environment.PRODUCTION`** |
| `timeout` | `int` | Timeout for API calls |
| `oAuthToken` | `?Models\OAuthToken` | OAuth 2 token |

The API client can be initialized as follows:

```php
$client = new MdNotesCCGLib\MdNotesCCGClient([
    // Set authentication parameters
    'oAuthClientId' => 'OAuthClientId',
    'oAuthClientSecret' => 'OAuthClientSecret',

    // Set the environment
    'environment' => 'production',
]);
```

You must now authorize the client.

### Authorization

The SDK uses *OAuth 2.0 Authorization* to authorize the client.

The `authorize()` method will exchange the OAuth client credentials for an *access token*. The access token is an object containing information for authorizing client requests and refreshing the token itself.

```php
try {
    $client->auth()->authorize();
} catch (MdNotesCCGLib\Exceptions\OAuthProviderException $ex) {
    // handle exception
}
```

The client can now make authorized endpoint calls.

#### Storing an access token for reuse

It is recommended that you store the access token for reuse.

```php
// store token
$_SESSION['access_token'] = MdNotesCCGLib\Configuration::$oAuthToken;
```

#### Creating a client from a stored token

To authorize a client from a stored access token, just set the access token in Configuration along with the other configuration parameters before creating the client:

```php
// load token later...
MdNotesCCGLib\Configuration::$oAuthToken = $_SESSION['access_token'];

// Set other configuration, then instantiate client
$client = new MdNotesCCGLib\MdNotesCCGClient();
```

#### Complete example

```php
<?php
require_once __DIR__.'/vendor/autoload.php';

session_start();

// Client configuration
$client = new MdNotesCCGLib\MdNotesCCGClient([
    // Set authentication parameters
    'oAuthClientId' => 'OAuthClientId',
    'oAuthClientSecret' => 'OAuthClientSecret',

    // Set the environment
    'environment' => 'production',
]);



// try to restore access token from session
if (isset($_SESSION['access_token']) && $_SESSION['access_token']) {
    MdNotesCCGLib\Configuration::$oAuthToken = $_SESSION['access_token'];
} else {
    try {
        // obtain a new access token
        $token = $client->auth()->authorize();
        $_SESSION['access_token'] = $token;
    } catch (MdNotesCCGLib\Exceptions\OAuthProviderException $ex) {
        // handle exception
        die();
    }
}

// the client is now authorized; you can use $client to make endpoint calls
```

### Test the SDK

Unit tests in this SDK can be run using PHPUnit.

1. First install the dependencies using composer including the `require-dev` dependencies.
2. Run `vendor\bin\phpunit --verbose` from commandline to execute tests. If you have installed PHPUnit globally, run tests using `phpunit --verbose` instead.

You can change the PHPUnit test configuration in the `phpunit.xml` file.

## Client Class Documentation

### MdNotesCCG Client

The gateway for the SDK. This class acts as a factory for the Controllers and also holds the configuration of the SDK.

### Controllers

| Name | Description |
|  --- | --- |
| getUserController() | Provides access to UserController |
| getServiceController() | Provides access to ServiceController |
| getOAuthAuthorizationController() | Provides access to OAuthAuthorizationController |

## API Reference

### List of APIs

* [User](#user)
* [Service](#service)

### User

#### Overview

##### Get singleton instance

The singleton instance of the `UserController` class can be accessed from the API Client.

```
$userController = $client->getUserController();
```

#### Get User

```php
function getUser(): User
```

##### Response Type

[`User`](#user-1)

##### Example Usage

```php
$result = $userController->getUser();
```

### Service

#### Overview

##### Get singleton instance

The singleton instance of the `ServiceController` class can be accessed from the API Client.

```
$serviceController = $client->getServiceController();
```

#### Get Status

```php
function getStatus(): ServiceStatus
```

##### Response Type

[`ServiceStatus`](#service-status)

##### Example Usage

```php
$result = $serviceController->getStatus();
```

## Model Reference

### Structures

* [Note](#note)
* [User](#user-1)
* [Service Status](#service-status)
* [O Auth Token](#o-auth-token)

#### Note

##### Class Name

`Note`

##### Fields

| Name | Type | Tags | Description | Getter | Setter |
|  --- | --- | --- | --- | --- | --- |
| `id` | `int` | Required | - | getId(): int | setId(int id): void |
| `title` | `string` | Required | - | getTitle(): string | setTitle(string title): void |
| `body` | `string` | Required | - | getBody(): string | setBody(string body): void |
| `userId` | `int` | Required | - | getUserId(): int | setUserId(int userId): void |
| `createdAt` | `string` | Required | - | getCreatedAt(): string | setCreatedAt(string createdAt): void |
| `updatedAt` | `string` | Required | - | getUpdatedAt(): string | setUpdatedAt(string updatedAt): void |

##### Example (as JSON)

```json
{
  "id": 112,
  "title": "title4",
  "body": "body6",
  "user_id": 208,
  "created_at": "created_at2",
  "updated_at": "updated_at4"
}
```

#### User

##### Class Name

`User`

##### Fields

| Name | Type | Tags | Description | Getter | Setter |
|  --- | --- | --- | --- | --- | --- |
| `id` | `int` | Required | - | getId(): int | setId(int id): void |
| `name` | `string` | Required | - | getName(): string | setName(string name): void |
| `email` | `string` | Required | - | getEmail(): string | setEmail(string email): void |
| `createdAt` | `string` | Required | - | getCreatedAt(): string | setCreatedAt(string createdAt): void |
| `updatedAt` | `string` | Required | - | getUpdatedAt(): string | setUpdatedAt(string updatedAt): void |

##### Example (as JSON)

```json
{
  "id": 112,
  "name": "name0",
  "email": "email6",
  "created_at": "created_at2",
  "updated_at": "updated_at4"
}
```

#### Service Status

##### Class Name

`ServiceStatus`

##### Fields

| Name | Type | Tags | Description | Getter | Setter |
|  --- | --- | --- | --- | --- | --- |
| `app` | `string` | Required | - | getApp(): string | setApp(string app): void |
| `moto` | `string` | Required | - | getMoto(): string | setMoto(string moto): void |
| `notes` | `int` | Required | - | getNotes(): int | setNotes(int notes): void |
| `users` | `int` | Required | - | getUsers(): int | setUsers(int users): void |
| `time` | `string` | Required | - | getTime(): string | setTime(string time): void |
| `os` | `string` | Required | - | getOs(): string | setOs(string os): void |
| `phpVersion` | `string` | Required | - | getPhpVersion(): string | setPhpVersion(string phpVersion): void |
| `status` | `string` | Required | - | getStatus(): string | setStatus(string status): void |

##### Example (as JSON)

```json
{
  "app": "app2",
  "moto": "moto8",
  "notes": 134,
  "users": 202,
  "time": "time0",
  "os": "os8",
  "php_version": "php_version4",
  "status": "status8"
}
```

#### O Auth Token

OAuth 2 Authorization endpoint response

##### Class Name

`OAuthToken`

##### Fields

| Name | Type | Tags | Description | Getter | Setter |
|  --- | --- | --- | --- | --- | --- |
| `accessToken` | `string` | Required | Access token | getAccessToken(): string | setAccessToken(string accessToken): void |
| `tokenType` | `string` | Required | Type of access token | getTokenType(): string | setTokenType(string tokenType): void |
| `expiresIn` | `?int` | Optional | Time in seconds before the access token expires | getExpiresIn(): ?int | setExpiresIn(?int expiresIn): void |
| `scope` | `?string` | Optional | List of scopes granted<br>This is a space-delimited list of strings. | getScope(): ?string | setScope(?string scope): void |
| `expiry` | `?int` | Optional | Time of token expiry as unix timestamp (UTC) | getExpiry(): ?int | setExpiry(?int expiry): void |

##### Example (as JSON)

```json
{
  "access_token": "access_token8",
  "token_type": "token_type2",
  "expires_in": null,
  "scope": null,
  "expiry": null
}
```

### Enumerations

* [O Auth Provider Error](#o-auth-provider-error)

#### O Auth Provider Error

OAuth 2 Authorization error codes

##### Class Name

`OAuthProviderErrorEnum`

##### Fields

| Name | Description |
|  --- | --- |
| `INVALID_REQUEST` | The request is missing a required parameter, includes an unsupported parameter value (other than grant type), repeats a parameter, includes multiple credentials, utilizes more than one mechanism for authenticating the client, or is otherwise malformed. |
| `INVALID_CLIENT` | Client authentication failed (e.g., unknown client, no client authentication included, or unsupported authentication method). |
| `INVALID_GRANT` | The provided authorization grant (e.g., authorization code, resource owner credentials) or refresh token is invalid, expired, revoked, does not match the redirection URI used in the authorization request, or was issued to another client. |
| `UNAUTHORIZED_CLIENT` | The authenticated client is not authorized to use this authorization grant type. |
| `UNSUPPORTED_GRANT_TYPE` | The authorization grant type is not supported by the authorization server. |
| `INVALID_SCOPE` | The requested scope is invalid, unknown, malformed, or exceeds the scope granted by the resource owner. |

### Exceptions

* [O Auth Provider](#o-auth-provider)

#### O Auth Provider

OAuth 2 Authorization endpoint exception

##### Class Name

`OAuthProviderException`

##### Fields

| Name | Type | Tags | Description | Getter | Setter |
|  --- | --- | --- | --- | --- | --- |
| `error` | [`string (OAuthProviderError)`](#o-auth-provider-error) | Required | Error code | getError(): string | setError(string error): void |
| `errorDescription` | `?string` | Optional | Human-readable text providing additional information on error.<br>Used to assist the client developer in understanding the error that occurred. | getErrorDescription(): ?string | setErrorDescription(?string errorDescription): void |
| `errorUri` | `?string` | Optional | A URI identifying a human-readable web page with information about the error, used to provide the client developer with additional information about the error | getErrorUri(): ?string | setErrorUri(?string errorUri): void |

##### Example (as JSON)

```json
{
  "error": "invalid_request",
  "error_description": null,
  "error_uri": null
}
```

## Utility Classes Documentation

### FileWrapper

Thrown when there is a network error or HTTP response status code is not okay.

### Constructor Args

| Name | Type | Description |
|  --- | --- | --- |
| $realFilePath | string | The path of the file to wrap. |
| $mimeType | ?mimeType | The mime-type to be sent with the file. |
| $filename | ?string | The name to be used when sending the file. |

### Methods

| Name | Type | Description |
|  --- | --- | --- |
| getFilename() | ?string | Get name of the file to be used in the upload data. |
| getMimeType() | ?string | Get the mime-type to be sent with the file. |

## Common Code Documentation

### ApiException

Thrown when there is a network error or HTTP response status code is not okay.

#### Methods

| Name | Type | Description |
|  --- | --- | --- |
| getHttpRequest() | HttpRequest | Returns the HTTP request. |
| getHttpResponse() | ?HttpResponse | Returns the HTTP response. |
| hasResponse() | bool | Is the response available? |

### HttpRequest

Represents a single Http Request.

#### Methods

| Name | Type | Description |
|  --- | --- | --- |
| getHttpMethod() | string | The HTTP method of the request. |
| getQueryUrl() | string | The endpoint URL for the API request. |
| getHeaders() | array | Request headers. |
| getParameters() | array | Input parameters for the body. |

### HttpResponse

Http response received.

#### Methods

| Name | Type | Description |
|  --- | --- | --- |
| getStatusCode() | int | The status code of the response. |
| getHeaders() | array | Response headers. |
| getRawBody() | string | Raw body of the response. |

