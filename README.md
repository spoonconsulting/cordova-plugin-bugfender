# cordova-plugin-bugfender
This plugin adds Bugfender support for Cordova applications under iOS and Android. Also works on Cordova-based projects such as Phonegap and Ionic.

## Requirements
For iOS, you will need macOS and [CocoaPods](http://cocoapods.org/) installed on your system. iOS deployment target version is required to be 8.0 at least.

You can set the minimum version by adding this preference to the `<platform name="ios">` tag in your `config.xml`:

```
 <preference name="deployment-target" value="8.0" />
```

## Installing in Cordova
In the command line, run (replace `XXX` with your app key):

```
cordova plugin add cordova-plugin-bugfender --variable BUGFENDER_APP_KEY=XXX --save
```

**If using TypeScript:** you also need to declare the Bugfender global variable in the files where you need it:

```
declare var Bugfender: any;
```

Then use `Bugfender.log()` to log anything you want. Syntax is the same as `console.log()`.

For a basic use of Bugfender, that's all you need to do. Read on to learn more advanced options.

## Installing in Ionic
In the command line, run (replace `XXX` with your app key):

```
ionic cordova plugin add cordova-plugin-bugfender --variable BUGFENDER_APP_KEY=XXX --save
```

Wherever you want to use Bugfender, import it:

```
import { Bugfender } from 'cordova-plugin-bugfender/www/bugfender';
```

Then use `Bugfender.log()` to log anything you want. Syntax is the same as `console.log()`.

For a basic use of Bugfender, that's all you need to do. Read on to learn more advanced options.

## Configuration variables

### Application key
The `BUGFENDER_APP_KEY` variable idicates the Bugfender application to log to. You can get one at [bugfender.com](https://bugfender.com).

### Automated logger configuration (optional)
The `BUGFENDER_AUTOMATIC` variable is a comma separated set of the following values:

* `NONE`: no logging is done automatically. Only logs you manually send via the `log`, `error`, `warn`, `info` and `trace` methods are sent to the server.
* `UI`: user interaction and screen changes are logged automatically. Please note only interactions with native elements will be logged.
* `CRASH`: crashes are logged automatically.
* `LOG`: (Android only) anything logged to Logcat is fetched and sent automatically. This might include log messages generated by the system or libraries in your project.
* `ALL`: all automated loggers are enabled. As of today, this is equivalent to `UI,LOG,CRASH`, but more loggers could be included in the future. This enables all of them.

You can specify this variable when installing the plugin as a `--variable`, for example `cordova plugin add cordova-plugin-bugfender --variable BUGFENDER_APP_KEY=XXX --variable BUGFENDER_AUTOMATIC=NONE --save`.

## Reference

### Manual logging
You can use Bugfender in a similar way you would use the `console` object.

You can log strings, numbers or booleans:

```
Bugfender.log('Hello! This is three:', 3, 'And this is true:', true);
```

You can log objects, they will be converted to a JSON string:

```
Bugfender.log('Current score:', {user: 'John', points: 100});
```

You can specify a format argument as first parameter:

```
Bugfender.log("%s has %d points", "Sam", 100);
```

Format arguments available are as follows:

| Substitution string | Description |
| ---- | ---- |
| %o or %O | Outputs a JavaScript object which will be serialized as JSON |
| %d or %i | Outputs an integer. Number formatting is supported, for example  console.log("Foo %.2d", 1.1) will output the number as two significant figures with a leading 0: Foo 01 |
| %s | Outputs a string. |
| %f | Outputs a floating-point value. Formatting is supported, for example  console.log("Foo %.2f", 1.1) will output the number to 2 decimal places: Foo 1.10 |

The following logging levels are available:

* `error([fmt,] message,...)`
* `warn([fmt,] message,...)`
* `info([fmt,] message,...)`
* `trace([fmt,] message,...)`
* `log([fmt,] message,...)` (equivalent to `info`)

### Device associated data
Using these functions you can associate key-value pairs to a device, like a dictionary.

* `setDeviceKey(key, value)`: sets a key-value pair. You can set strings, numbers or booleans
* `removeDeviceKey(key)`: removes a key-value pair

For example:

```
// when user logs in
Bugfender.setDeviceKey('email', 'test@example.com');
...
// when user logs out
Bugfender.removeDeviceKey('email');
```

### Send logs programatically
You can override the Bugfender website settings with the following functions.

* `sendIssue(title,text)`: Sends an issue. Sending an issue forces the logs of the current session being sent to the server, and marks the session so that it is highlighted in the web console. The `text` parameter is interpreted as Markdown.
* `forceSendOnce`: Synchronizes all logs with the server once, regardless if this device is enabled or not. This method is useful when an error condition is detected and the logs should be sent to the server for analysis, regardless if the device is enabled in the Bugfender Console. Logs are synchronized only once. After that, the logs are again sent according to the enabled flag in the Bugfender Console. This command can be called anytime, and will take effect the next time the device is online.
* `setForceEnabled(enabled)`: Synchronizes all logs with the server all the time, regardless if this device is enabled or not. This method is useful when the logs should be sent to the server regardless if the device is enabled in the Bugfender Console. Logs are synchronized continuously while `setForceEnabled` is `true`. This command can be called anytime, and will take effect the next time the device is online.

### Device identifier
When using Bugfender for the first time, a device identifier is generated. The device identifier is unique to the device and installation. It is not related to the device in any way (UDID, MAC address, Android ID, ...). Uninstalling and installing the application again will generate a new device identifier.

* `getDeviceIdentifier(callback)`: Gets the Bugfender internal device identifier. The result is provided in a callback function, the only argument being a string.

For example:

```
Bugfender.getDeviceIdentifier(function(deviceId){
	alert("Bugfender device id: " + deviceId);
});
```

### Disk usage
Bugfender caches logs in the mobile device internal memory temporarily if there is no Internet connection to send logs. There is the possibility to limit the amount of disk space that this cache can take. If the cache becomes full, the oldest logs will be discarded.

* `setMaximumLocalStorageSize(size)`: Set the maximum space available to store local logs. This value is represented in bytes. There’s a limit of 50 MB. By default it's configured to 5 MB.

For example:

```
// set cache size limit to 10 MB
Bugfender.setMaximumLocalStorageSize(10*1024*1024);
```
