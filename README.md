# GraphButton, a circuit that posts to the Open Graph 

This tutorial is written to use **the WiFly Module**.

This is a tutorial for posting to the open graph using the same backend as the [GraphButton](https://github.com/lifegraph/graphbutton-imp). The only difference in this is that we'll be using a [Sparkfun WiFly Module ($35)](https://www.sparkfun.com/products/10822) instead of the Electric Imp.

**What you’ll learn:** How to create a circuit that posts an action to your profile on the Open Graph with a WiFly module.

**What you'll need:**
* [A WiFly Module](https://www.sparkfun.com/products/10822) ($35)
* [An Arduino](https://www.sparkfun.com/products/11021) ($30)
* A switch or some wire
* A resistor (~10k ohms)

## Set up your Arduino and WiFly

Make sure you have the Arduino Software installed: http://arduino.cc/en/main/software

Open up the Arduino IDE. Make sure your Arduino works by going to File -> Examples -> Basic -> Blink. Upload the code to your Arduino by selecting the correct usb port on Tools->Serial Port and select the correct arduino board from Tools->board. Upload your the blink code by clicking on the upload button. 

This will make a light on your Arduino blink and is a super basic test of whether everything is working. 

## Adding the Button

You'll need to connect a button between digital pin 12 and ground on the Arduino as well. Pushing down this button will make the Arduino post to Facebook. 

We'll also add a pull up resistor between pin 12 and +5v, otherwise the pin might read 0 at random times. The general recommended resistance is around 10k but it'll work with other resistances as well. You just don't want a resistance that is too low or else it'll short between 


and Ground.

![circuit](https://raw.github.com/lifegraph/graphbutton-wifly/master/imgs/circuit.png)

## Soldering the WiFly (XBee form factor)

There are 4 pins that you need to connect from the WiFly module to the Arduino: 3.3v, GND, TX (for transmitting), and RX (for receiving).

These 4 pins correspond to the following on the WiFly module

![WiFly](https://raw.github.com/lifegraph/graphbutton-wifly/master/imgs/wifly.png)

* Pin 1 &mdash; 3.3v. Connect this to the **3.3v pin** on the Arduino.
* Pin 2 &mdash; This is the Transmitter pin for the WiFly. Connect it to Digital pin 2 on the Arduino.
* Pin 3 &mdash; This is the Receiver pin for the WiFly. Connect it to Digital pin 3 on the Arduino.
* Pin 10 &mdash; Connect this to GND.

We recommend using female to female header pins because the WiFly is rather small and soldering directly pin to pin can be frustrating. 
Note that after soldering, if everything is correct, a red light will blink on the WiFly. 

![WiFly all wired up](http://i.imgur.com/EDxmchO.png)

## Making HTTP Requests

We'll be using [WiFlyHQ](https://github.com/harlequin-tech/WiFlyHQ) as our library for interfacing with the WiFly module. This allows us to talk to the WiFly over serial.

In order to setup WiFlyHQ, you'll need to download it to your Arduino libaries. To do this, go to [WiFlyHQ](https://github.com/harlequin-tech/WiFlyHQ), click on the
"zip" button to download a zip file of the directory, then extract the files into your Arduino libraries folder. On OSX this is typically in `~/Documents/Arduino/libaries/`. If you don't have a library folder, you'll need to make one. 

```
cd ~/Documents/Arduino/libraries;
git clone https://github.com/harlequin-tech/WiFlyHQ;
```

After you add the library, you'll need to restart the Arduino IDE for it to pick up the library. If you've added it in the right place, you should be able to see the WiFlyHQ library if you go to Sketch -> Import Library.

After you have the library working, you'll need to open up the [httpclient example in this repo](https://github.com/lifegraph/graphbutton-wifly/blob/master/httpclient/httpclient.ino) and open it up with the Arduino IDE. 

In `httpclient.ino`, you'll need to change the SSID (name of your network) and the password to work with your own WiFi network:

```ino
const char mySSID[] = "your_ssid";
const char myPassword[] = "your_password";
```

Next we can go to [http://graphbutton.herokuapp.com/](http://graphbutton.herokuapp.com/) and log in with Facebook. Once you log in, you'll see a link like

![GraphButton](http://i.imgur.com/JyhsFCA.png)

You want to copy the path string, i.e. `/action/17383ae...`, and paste that into `httpclient.ino`, so your code should look like:

```ino
wifly.println("POST /action/60ce6bdda1e131973c722d0906524b2ed24c44a6 HTTP/1.1");
wifly.println("Host: graphbutton.herokuapp.com:80");
wifly.println("Content-type: application/json");
```

Now when you push down on the button, you should see the following output

![Arduino output](https://raw.github.com/lifegraph/graphbutton-wifly/master/imgs/arduino-output.png)

And if you go to Facebook you should see the following action

![Facebook action](https://raw.github.com/lifegraph/graphbutton-wifly/master/imgs/facebook-action.png)

## Running your own server backend

The source code for the GraphButton server is open source, so you can fork it and start your own. To clone the repository:

```
$ git clone https://github.com/lifegraph/graphbutton-imp
$ heroku create
$ heroku config:add HOST=<heroku host>
```

### Creating a Facebook app

1. Log into https://developers.facebook.com/apps. Create a new application and put in "GraphButton Test" for the name and "lifegraphlabs" as the namespace. Don't select heroku as your host. We'll do that later.

2. Copy and paste your <heroku host> into the App Domains field.

3. Check "Website with Facebook Login" and put in the site URL for the heroku host you already created as "http://<heroku host>/"

4. Save your app

5. The app ID and SECRET are at the top of our Facebook App. Copy app ID and app SECRET. paste to 

    ```
    $ heroku config:add FB_KEY=the key
    $ heroku config:add FB_SECRET=the secret
    $ heroku config:add FB_ACTION=the action
    ```

6. Click on "Open Graph" in the settings panel on the left. Click Getting Started. 

7. Select “press” as the Action type and “button” as the Object type. Select "Number of units" for the aggregation type. The Object type must be button or else the POST payload has to change in the app code.


## Want to learn more?

[Lifegraph Labs](http://www.lifegraphlabs.com) has [Tutorials](http://lifegraphlabs.com/how-to) to connect the real world with the digital, [Tools](http://lifegraphlabs.com/tools) to get you started quickly, and [Ideas](http://lifegraphlabs.com/ideas) of awesome things you could build right now. [Go there now!](http://www.lifegraphlabs.com) 

