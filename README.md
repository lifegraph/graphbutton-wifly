# GraphButton

a circuit that posts to the Open Graph 

## Prerequisites

Completed the [hardware tutorial](https://github.com/lifegraph/hw-tutorial) and have a set up Arduino that can talk to the internet. 

**What you’ll learn:** How to create a circuit that posts an action to your profile on the Open Graph with a WiFly module.

**What you'll need:**
* [A WiFly Module](https://www.sparkfun.com/products/10822) ($35)
* [An Arduino](https://www.sparkfun.com/products/11021) ($30)
* A switch or some wire
* A resistor (~10k ohms)

## Set up your Arduino & WiFly module

The Arduino and WiFly shield should already be set up from the [hardware tutorial](https://github.com/lifegraph/hw-tutorial).

## Adding the Button

You'll need to connect a button between digital pin 12 and ground on the Arduino as well. Pushing down this button will make the Arduino post to Facebook. 

We'll also add a pull up resistor between pin 12 and +5v, otherwise the pin might read 0 at random times. The general recommended resistance is around 10k but it'll work with other resistances as well. You just don't want a resistance that is too low (<100 ohms) or else it'll short between Vcc and Ground.

![circuit](https://raw.github.com/lifegraph/graphbutton-wifly/master/imgs/circuit.png)

## Making HTTP Requests

We'll be using [WiFlyHQ](https://github.com/harlequin-tech/WiFlyHQ) as our library for interfacing with the WiFly module. The setup of the library is covered in the [hardware tutorial](https://github.com/lifegraph/hw-tutorial).

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

## Further examples

* [Notification light](https://github.com/lifegraph/notificationlight) - a light that lights up when you have a new facebook notification

