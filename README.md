## Analog - PHP 5.3+ micro logging package

* Copyright: (c) 2012 Johnny Broadway
* License: http://www.opensource.org/licenses/mit-license.php

A [MicroPHP](http://microphp.org/) logging package based on the idea of using closures
for configurability and extensibility. It functions as a static class, but you can
completely control the writing of log messages through a closure function
(aka [anonymous functions](http://ca3.php.net/manual/en/functions.anonymous.php)).

By default, this class will write to a file named `sys_get_temp_dir() . '/analog.txt'`
using the format `"machine - date - level - message\n"`, making it usable with no
customization necessary.

Analog also comes with a dozen pre-written handlers in the Analog/Handlers folder,
with examples for each in the examples folder. These include:

* Buffer - Buffer messages to send all at once (works with File, Mail, Stderr, and Variable handlers)
* File - Append messages to a file
* FirePHP - Send messages to [FirePHP](http://www.firephp.org/) browser plugin
* LevelBuffer - Buffer messages and send only if sufficient error level reached
* Mail - Send email notices
* Mongo - Save to MongoDB collection
* Multi - Send different log levels to different handlers
* Null - Do nothing
* Post - Send messages over HTTP POST to another machine
* Stderr - Send messages to STDERR
* Syslog - Send messages to syslog
* Variable - Buffer messages to a variable reference.

So while it's a micro class, it's highly extensible and very capable out of the box too.

### Rationale

I wrote this because I wanted something very small and simple like
[KLogger](https://github.com/katzgrau/KLogger), and preferably not torn out
of a wider framework if possible. After searching, I wasn't happy with the
single-purpose libraries I found. With KLogger for example, I didn't want an
object instance but rather a static class, and I wanted more flexibility in
the back-end.

I also found some that had the flexibility also had more complexity, for example
[Monolog](https://github.com/Seldaek/monolog) is 25 source files (not incl. tests).
With closures, this seemed to be a good balance of small without sacrificing
flexibility.

> What about Analog, the logfile analyzer? Well, since it hasn't been updated
> since 2004, I think it's safe to call a single-file PHP logging class the
> same thing without it being considered stepping on toes :)

### Usage

Standard usage:

```php
<?php

require_once ('Analog.php');

// Default logging to /tmp/analog.txt
Analog::log ('Log this error');

// Log to a MongoDB log collection
Analog::handler (function ($info) {
	static $conn = null;
	if (! $conn) {
		$conn = new Mongo ('localhost:27017');
	}
	$conn->mydb->log->insert ($info);
});

// Log an alert
Analog::log ('The sky is falling!', Analog::ALERT);

// Log some debug info
Analog::log ('Debugging info', Analog::DEBUG);

?>
```

PSR-0 usage:

```php
<?php

require_once ('SplClassLoader.php');

$loader = new SplClassLoader ('Analog', 'lib');
$loader->register ();

use \Analog\Analog;

Analog::log ('Log this error');

Analog::handler (\Analog\Handler\FirePHP::init ());

Analog::log ('Take me to your browser');

?>
```

For more examples, see the [examples](https://github.com/jbroadway/analog/tree/master/examples) folder.
