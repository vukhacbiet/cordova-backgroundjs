Cordova BackgroundJS
=====================

Whoa! Now you can background JavaScript operations on iOS with Cordova!

Uses a sleep countdown timer paired with a UIBackgroundTaskIdentifier. The magic looks like this:

```objc
	- (void)doBackgroundTimeLoop
	{
		__block UIBackgroundTaskIdentifier task;
		UIApplication* app = [UIApplication sharedApplication];
		task = [app beginBackgroundTaskWithExpirationHandler:^{
			[app endBackgroundTask:task];
			task = UIBackgroundTaskInvalid;
		}];

		while(YES){
			@synchronized(self){
				backgroundSecondsCounter--;
				NSLog(@"Remaining background seconds: %i", backgroundSecondsCounter);
				if (backgroundSecondsCounter <= 0)
					break; // Exit loop
			}
			[NSThread sleepForTimeInterval:1];
		}

		// End this background task now
		[app endBackgroundTask:task];
		task = UIBackgroundTaskInvalid;
	}
```

While the background task is active, anything goes! You can run JavaScript in the background.

Add the BackgroundJS plugin to your Phonegap project:

```sh
cordova plugin add https://github.com/vukhacbiet/cordova-backgroundjs
```


...enable the plugin in `config.xml`:

```xml
    <feature name="BackgroundJS">
      <param name="ios-package" value="BackgroundJS" />
    </feature>
```

...and request some background time from JavaScript!

BackgroundJS is available in JavaScript via:

```javascript
BackgroundJS
```


Get a block of secondsi (e.g. use a background thread for 10 seconds):
```js
BackgroundJS.setBackgroundSeconds(10);
```
Run in the background indefinitely:

```js
BackgroundJS.lockBackgroundTime();
```

Stop running background tasks immediately:

```js
BackgroundJS.unlockBackgroundTime();
```

Be careful when using this, as Apple specifications are picky.
If you're not using background audio or tracking location, your app may be rejected for background tasking guidelines.
Read up, and write some code!

ALSO... Please don't kill my battery life just because you backgrounded more than you had to.
You're not a special snowflake (yet) so play by the rules. Code smart, and make your app work right!


