## DPSetupWindow

DPSetupWindow makes it easy to add setup style modal sheets or windows to your
Objective-C and Cocoa applications on Mac OS. Simply create a set of view
controllers and DPSetupWindow will take care of moving between them, as each
performs its validation and allows the process to continue.

![Example Setup Window](http://f.cl.ly/items/2h3j241C1p0K1O423i0o/DPSetupWindow.png)

Inspriation for this was taken from Mac OS installer applications and Mail.app's
account configuration process.

#### Usage

`DPSetupWindow` is designed to be very easy to use. The example application
distributed with the source code should provide enough documentation on how to
use it, but here is an overview.

Firstly create the setup window with an array of view controller for it to use.
A callback must also be provided, which will be called when the window closes,
passing back a boolean representing whether the user reached the end of the
process or not.

```objective-c
DPSetupWindow *setupFlow = [[DPSetupWindow alloc] initWithViewControllers: @[
	firstViewController,
	secondViewController,
	thirdViewController
] completionHandler:(void (^)(BOOL completed) {
	[setupFlow orderOut:self];
}];
```

There are two ways to use the setup window, either as a modal sheet where the
developer can control all aspects as would be normal, or as a standard window.

```objective-c
// Modal sheet
[[NSApplication sharedApplication] beginSheet:setupFlow 
															 modalForWindow:[self window]
																modalDelegate:nil
															 didEndSelector:NULL
																	contextInfo:NULL];

// Regular window
[[self setupFlow] makeKeyAndOrderFront:self];
```

The window can be customised with a background image, the default being your
application's icon.

```objective-c
[setupFlow setBackgroundImage:[NSImage imageNamed:@"NSUserAccounts"]];
```

Each view controller in the setup process must implement the optional
`DPSetupWindowStageViewController` protocol.

This protocol gives the view controller the ability to stop the user from going
backwards or forwards by implementing the `canContinue` and `canGoBack` boolean
properties. Button titles can also be overridden, for example you may want the
last stage to say "Finish" instead of "Continue", in which case the last view
controller should contain the following.

```objective-c
- (NSString *)continueButtonTitle {
	return @"Finish";
}
```

In order to make the setup process consistent with other similar windows, a
fixed size of window is used, and therefore the views owned by the view 
controllers have a fixed size of 400×330 points.