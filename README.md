# GraphButton

a circuit that posts to the Open Graph 

**What you’ll learn:** How to create a circuit that posts an action to your profile on the Open Graph with a WiFly module.

**What you'll need:**
* [A WiFly Module](https://www.sparkfun.com/products/10822) ($35)
* [An Arduino](https://www.sparkfun.com/products/11021) ($30)
* A switch or some wire
* A resistor (~10k ohms)

## Prerequisites

Completed the [hardware tutorial](https://github.com/lifegraph/hw-tutorial) and have a set up Arduino that can talk to the internet. 

## Set up your Arduino & WiFly module

The Arduino and WiFly shield should already be set up from the [hardware tutorial](https://github.com/lifegraph/hw-tutorial).

For more questions regarding getting started with Arduino, such as the Arduino IDE, how to use libraries in Arduino, etc. refer to http://arduino.cc/en/Guide/HomePage.

## Adding the Button

You'll need to connect a button between digital pin 12 and ground on the Arduino as well. Pushing down this button will make the Arduino post to Facebook. 

We'll also add a pull up resistor between pin 12 and +5v, otherwise the pin might read 0 at random times. The general recommended resistance is around 10k but it'll work with other resistances as well. You just don't want a resistance that is too low (<100 ohms) or else it'll short between Vcc and Ground.

![circuit](https://raw.github.com/lifegraph/graphbutton-wifly/master/imgs/circuit.png)

## Making HTTP Requests

We'll be using [WiFlyHQ](https://github.com/harlequin-tech/WiFlyHQ) as our library for interfacing with the WiFly module. The setup of the library is covered in the [hardware tutorial](https://github.com/lifegraph/hw-tutorial).

We'll also be using the [Lifegraph Arduino library](https://github.com/lifegraph/arduino-lifegraph) along with the WiFlyHQ library. The Lifegraph library allows us to easily use Facebook's API without having to worry about properly formatting HTTP requests ourselves. The library also gives us a way to process the data that Facebook sends back to us.

In order to install the library, you'll need to:

1. [download the zip file here](https://github.com/lifegraph/arduino-lifegraph/archive/master.zip)
2. Unzip the file
3. Rename the folder from arduino-lifegraph-master to Lifegraph
4. Put the folder where the rest of your Arduino libraries are. This is probably underneath `~/Documents/Arduino/libraries`
5. Restart the Arduino IDE

Now open the Arduino IDE and you should see the Lifegraph library as an option when you go to Sketch -> Import Library

## The Arduino code

Then open up `graphbutton.ino` from the [graphbutton example in this repo](https://github.com/lifegraph/graphbutton-wifly/blob/master/graphbutton/graphbutton.ino). You'll need to change the network name and the password 

```ino
const char mySSID[] = "your_ssid";
const char myPassword[] = "your_password";
```

We'll also need an authentication token from Facebook in order to get the right notification information. For a temporary access token, follow these steps:

1. Go to the Graph API Explorer: [https://developers.facebook.com/tools/explorer](https://developers.facebook.com/tools/explorer)
2. request a token with "manage_notifications" and "publish_stream" permissions.

After you get your access token, you'll need to stick it in the `graphbutton.ino` as well:

```ino
const char access_token[] = "...";
```

However, these tokens expire every hour. In order to get a longer lasting token you'll need to make your own Facebook application and get an auth token from there.

## Further examples

* [Notification light](https://github.com/lifegraph/notificationlight) - a light that lights up when you have a new facebook notification

