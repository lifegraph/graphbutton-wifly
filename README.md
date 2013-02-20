# GraphButtonWiFly, a circuit that posts to the Open Graph using a WiFly Module

This is a tutorial for posting to the open graph using the same backend as the [GraphButton](https://github.com/lifegraph/graphbutton-imp). The only difference in this is that we'll be using a [Sparkfun WiFly Module ($35)](https://www.sparkfun.com/products/10822) instead of the Electric Imp.

**What you’ll learn:** How to create a circuit that posts an action to your profile on the Open Graph with a WiFly module.

**What you'll need:**
* An Electric Imp
* An Electric Imp breakout board, or an Arduino + an Electric Imp Shield
* A switch or some wire

## Setting up an Arduino 

Make sure you have the Arduino Software installed: http://arduino.cc/en/main/software

Open up the Arduino IDE. Make sure your Arduino works by going to File -> Examples -> Basic -> Blink. Upload the code to your Arduino by selecting the correct usb port on Tools->Serial Port and select the correct arduino board from Tools->board. Upload your the blink code by clicking on the upload button. 

This will make a light on your Arduino blink and is a super basic test of whether everything is working. 

## Adding the Button

You'll need to connect a button between digital pin 12 and ground on the Arduino as well. Pushing down this button will make the Arduino post to Facebook. 

We'll also add a pull up resistor between pin 12 and Vcc, otherwise the pin might read 0 at random times. The general recommended resistance is around 10k but it'll work with other resistances as well. You just don't want a resistance that is too low or else it'll short between Vcc and Ground.

## Setting up the WiFly

There are 4 pins that you need to connect from the WiFly module to the Arduino: Vcc, GND, TX, and RX.

These 4 pins correspond to the following on the WiFly module

![WiFly](https://raw.github.com/lifegraph/graphbutton-wifly/master/imgs/wifly.png)

* Pin 1 - Vcc. **Note**: This must be go into the 3.3v pin
* Pin 2 - TX. Digital pin 2 on the Arduino.
* Pin 3 - RX. Digital pin 3 on the Arduino.
* Pin 10 - GND.

We'll be using [WiFlyHQ](https://github.com/harlequin-tech/WiFlyHQ) as our library for interfacing with the WiFly module. This allows us to talk to the WiFly over serial.

First off, you'll need to change the SSID and the password of the module to work with your own WiFi network:

```ino
const char mySSID[] = "your_ssid";
const char myPassword[] = "your_password";
```

Next we can go to [http://graphbutton.herokuapp.com/](http://graphbutton.herokuapp.com/) and log in with Facebook. Once you log in, you'll see a link like

![GraphButton](https://raw.github.com/lifegraph/graphbuttonwifly/master/imgs/graphbutton.png)

You want to copy the part that says '60ce6bdda1e131973c722d0906524b2ed24c44a6' and paste that into `httpclient.ino`

so your code should look like

```ino
wifly.println("POST /action/60ce6bdda1e131973c722d0906524b2ed24c44a6 HTTP/1.1");
wifly.println("Host: graphbutton.herokuapp.com:80");
wifly.println("Content-type: application/json");
```

Now when you push down on the button, you should see the following output

![Arduino output](https://raw.github.com/lifegraph/graphbutton-wifly/master/imgs/arduino-output.png)

And if you go to Facebook you should see the following action

![Facebook action](https://raw.github.com/lifegraph/graphbutton-wifly/master/imgs/facebook-action.png)

## Running your own server

The source code for the GraphButton server is open source, so you can fork it and start your own. To clone the repository:

```
$ git clone https://github.com/lifegraph/graphbutton
$ heroku create
$ heroku config:add HOST=<heroku host>
```

### Creating a Facebook app

Log into https://developers.facebook.com/apps. Create a new application. "GraphButton Test", "lifegraphlabs" is the namespace
Copypasta your <heroku host> into the App Domains entry.
Website with Facebook Login, site URL: http://<heroku host>/
Save
Copy app ID and app SECRET. paste to 

```
$ heroku config:add FB_KEY=the key
$ heroku config:add FB_SECRET=the secret
$ heroku config:add FB_ACTION=the action
```

Open Graph on the left. Click Getting Started. “press” a “button”. Probably select Number of units.
Must be “button” or else the POST payload has to be changed
Save changes and next
Define object type: button. Savenext
Data to Display, click “Press”..
Click number, Save and Finish

Click your Press action “get code”
copy “lifegraphlabs:press“ for the path
