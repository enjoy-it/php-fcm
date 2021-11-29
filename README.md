# phpFCM
PHP application server implementation for Firebase Cloud Messaging.
- supports device and topic messages
- currently this app server library only supports sending Messages/Notifications via HTTP.
- thanks to guzzle our library answers in PSR7 compatible response objects
- see the full docs on firebase cloud messaging here : https://firebase.google.com/docs/cloud-messaging/
- Firebase Cloud Messaging HTTP Protocol: https://firebase.google.com/docs/cloud-messaging/http-server-ref#send-downstream for in-depth description


#Setup
The recommended way of installing is using Composer.

command line
```
composer require enjoy-it/php-fcm
```

composer.json
```
"require": {
    "enjoy-it/php-fcm": "*"
}
```

#Send to Device
also see https://firebase.google.com/docs/cloud-messaging/downstream
```php
use enjoy-it\phpFCM\Client;
use enjoy-it\phpFCM\Message;
use enjoy-it\phpFCM\Recipient\Device;
use enjoy-it\phpFCM\Notification;

require_once 'vendor/autoload.php';

$apiKey = 'YOUR SERVER KEY';
$client = new Client();
$client->setApiKey($apiKey);
$client->injectHttpClient(new \GuzzleHttp\Client());

$note = new Notification('test title', 'testing body');
$note->setIcon('notification_icon_resource_name')
    ->setColor('#ffffff')
    ->setBadge(1);

$message = new Message();
$message->addRecipient(new Device('your-device-token'));
$message->setNotification($note)
    ->setData(array('someId' => 111));

$response = $client->send($message);
var_dump($response->getStatusCode());
```

#Send to topic
also see https://firebase.google.com/docs/cloud-messaging/topic-messaging
```php
use enjoy-it\phpFCM\Client;
use enjoy-it\phpFCM\Message;
use enjoy-it\phpFCM\Recipient\Topic;
use enjoy-it\phpFCM\Notification;

require_once 'vendor/autoload.php';


$apiKey = 'YOUR SERVER KEY';
$client = new Client();
$client->setApiKey($apiKey);
$client->injectHttpClient(new \GuzzleHttp\Client());

$message = new Message();
$message->addRecipient(new Topic('your-topic'));
//select devices where has 'your-topic1' && 'your-topic2' topics
$message->addRecipient(new Topic(['your-topic1', 'your-topic2']));
$message->setNotification(new Notification('test title', 'testing body'))
    ->setData(array('someId' => 111));

$response = $client->send($message);
var_dump($response->getStatusCode());
```

#Subscribe device to the topic
```php
use enjoy-it\phpFCM\Client;

$apiKey = '_YOUR_SERVER_KEY_';
$client = new Client();
$client->setApiKey($apiKey);

$response = $client->addTopicSubscription('_SOME_TOPIC_ID_', ['_FIRST_TOKEN_', '_SECOND_TOKEN_']);
var_dump($response->getStatusCode());
var_dump($response->getBody()->getContents());
```
#Remove device to the topic
```php
use enjoy-it\phpFCM\Client;

$apiKey = '_YOUR_SERVER_KEY_';
$client = new Client();
$client->setApiKey($apiKey);

$response = $client->removeTopicSubscription('_SOME_TOPIC_ID_', ['_FIRST_TOKEN_', '_SECOND_TOKEN_']);
var_dump($response->getStatusCode());
var_dump($response->getBody()->getContents());
```