# EFArduinoFSK

[![CI Status](http://img.shields.io/travis/ezefranca/EFArduinoFSK.svg?style=flat)](https://travis-ci.org/ezefranca/EFArduinoFSK)
[![Version](https://img.shields.io/cocoapods/v/EFArduinoFSK.svg?style=flat)](http://cocoapods.org/pods/EFArduinoFSK)
[![License](https://img.shields.io/cocoapods/l/EFArduinoFSK.svg?style=flat)](http://cocoapods.org/pods/EFArduinoFSK)
[![Platform](https://img.shields.io/cocoapods/p/EFArduinoFSK.svg?style=flat)](http://cocoapods.org/pods/EFArduinoFSK)

Dependencies for iOS Development, using [Sofmodem Arduino library](https://code.google.com/p/arms22/downloads/detail?name=SoftModem-005.zip&can=2&q=), with FSK communication.

[![imagem](https://raw.githubusercontent.com/ezefranca/EFArduinoFSK/master/images/fsk.png)](http://ironbark.xtelco.com.au/subjects/DC/lectures/7/)

## Example

To run the example project, clone the repo, and run `pod install` from the Example directory first.

## Requirements

## Installation

This libraries have a propouse work with an Arduino using a Sofmodem Shield* to communicate with iOS using FSK. Currently, the source code of the SoftModem is not made as a framework. If you want to use SoftModem in your project, you have two ways:


## Adding EFArduinoFSK to your project

### CocoaPods

[CocoaPods](http://cocoapods.org) is the recommended way to add EFArduinoFSK to your project.

Add a pod entry for EFArduinoFSK to your Podfile 

```ruby
pod 'EFArduinoFSK', '~> 0.0.2'
```

Install the pod(s) by running 

```ruby
pod install
```

### Source files

Alternatively you can directly adding source files to your project.

1. Download the [latest code version](https://github.com/ezefranca/FSK-Arduino-iOS/archive/master.zip) or add the repository as a git submodule to your git-tracked project. 
2. The Folder /FSK must be copied from the source code of your project. The following is the list of source code related to FSK. Please copy these to the project source code.

```objectivec
* AudioQueueObject.h
* AudioQueueObject.m
* AudioSignalAnalyzer.h
* AudioSignalAnalyzer.m
* AudioSignalGenerator.h
* AudioSignalGenerator.m
* CharReceiver.h
* FSKModemConfig.h
* FSKByteQueue.h
* FSKRecognizer.h
* FSKRecognizer.mm
* FSKSerialGenerator.h
* FSKSerialGenerator.m
* lockfree.h
* MultiDelegate.h
* MultiDelegate.m
* PatternRecognizer.h
```

SoftModem uses the following two framework for audio input and output. Please add them to your project.

![Image](https://raw.githubusercontent.com/ezefranca/EFArduinoFSK/master/images/framework.png)

```objectivec
* AudioToolbox.framework
* AVFoundation.framework
```


Initialization
=====

First, set the category of application with AVAudioSession class. To do voice recording and playback, AVAudioSessionCategoryPlayAndRecord need to be set.

```objectivec
AVAudioSession *session = [AVAudioSession sharedInstance];   
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(interruption:) name:
AVAudioSessionInterruptionNotification object:nil];

[session setCategory:AVAudioSessionCategoryPlayAndRecord error:nil];
[session setCategory:AVAudioSessionCategoryPlayback error:nil];
[session setActive:YES error:nil];
```

#### interruption selector method

```objectivec
- (void) interruption:(NSNotification*)notification
{
NSDictionary *interuptionDict = notification.userInfo;
NSUInteger interuptionType = (NSUInteger)[interuptionDict valueForKey:AVAudioSessionInterruptionTypeKey];

if (interuptionType == AVAudioSessionInterruptionTypeBegan)
[self beginInterruption];
#if __CC_PLATFORM_IOS >= 40000
else if (interuptionType == AVAudioSessionInterruptionTypeEnded)
[self endInterruptionWithFlags:(NSUInteger)[interuptionDict valueForKey:AVAudioSessionInterruptionOptionKey]];
#else
else if (interuptionType == AVAudioSessionInterruptionTypeEnded)
[self endInterruption];
#endif
}
```

Next, for analysis of the voice, make instance of class AudioSignalAnalyzer, FSKRecognizer and AudioSignalAnalyzer parses the input waveform from the microphone to detect the falling and rising edge of the waveform. FSKRecognizer restores the data bits based on the results of the analysis of AudioSignalAnalyzer.

```objectivec
recognizer = [[FSKRecognizer alloc] init];     
[recognizer addReceiver:self];
analyzer = [[AudioSignalAnalyzer alloc] init]; 
[analyzer addRecognizer:recognizer];
```

Then create an instance of a class FSKSerialGenerator for sound output. FSKSerialGenerator converts the data bits to audio signal and output.

```objectivec
generator = [[FSKSerialGenerator alloc] init]; 
[generator play];
```
#### Receiving

Register the class that implements the CharReceiver protocol to the FSKRecognizer class, and AVAudioSessionDelegate.

```objectivec
@interface YourClass : NSObject <AVAudioSessionDelegate, CharReceiver>
```
Register FSKRecognizer class at initialization.

```objectivec
YourClass *yourClassInstance;
[recognizer addReceiver: yourClassInstance];
```
receivedChar: method is called　when one byte of data is received.

```objectivec
- (void) receivedChar: (char) input
{
// Receive handling
}
```
#### Sending

Sending data is much easier than receiving data. FSKSerialGenerator class's writeByte: method to sends a single byte.

```objectivec
[generator writeByte: 0xff];
```

#### Links and Credits

[arms22](http://arms22.blog91.fc2.com/) - Creator of Softmodem hardware, libraries for Arduino and ARC version lib for iOS.

[Arduino Libraries](https://code.google.com/p/arms22/downloads/detail?name=SoftModem-005.zip&can=2&q=)

[iOS 4/5 ARC version](https://github.com/9labco/IR-Remote)

[FSK Wikipedia](http://en.wikipedia.org/wiki/Frequency-shift_keying)

[FSK Explanation](http://ironbark.xtelco.com.au/subjects/DC/lectures/7/)


## License

This code is distributed under the terms and conditions of the [MIT license](LICENSE). 


