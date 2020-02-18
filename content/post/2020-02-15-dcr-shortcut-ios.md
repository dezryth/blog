---
title: Use Siri Shortcuts with Decred
author: Scott
type: post
date: 2020-02-15T15:40:00+00:00
url: /2020/02/15/siri-shortcuts-and-decred
categories:
  - Decred
thumbnailImage: /img/dcr-shortcuts.jpg
---
![DCR and Shortcuts](/img/dcr-shortcuts.jpg)

You are a savvy cryptocurrency user. You want a convenient way to check your balance or view status of your tickets without logging into your full-node or VSP. With a little set-up, you can do this from the convenience of your phone in as little as one touch or a "Hey Siri.." command. Follow along with this post and you'll not only have this capability, but you'll be equipped to get creative and do all sorts of impressive things with Siri Shortcuts and running scripts over SSH. If this sounds too complicated, it's not! Reach out for help or do a little googling.

tl;dr - We're going to be creating a simple script using Python, allowing SSH connections from your phone to your server, and setting up a Siri shortcut to run the script. I'm not going into super nitty gritty details. Learning to teach yourself will take you far in this world!

## Requirements

### Full node with the latest version of Decred software

* I'm assuming you're using a linux distro to run your node. If not you may need to do a little extra googling to figure out how to modify steps here to your needs.
* If you aren't running your own full node, I highly suggest you start. If your DCR is controlled by a third party or you don't control your keys, you're opening yourself up to losing your Decred in the event of a breach.
* (Your software doesn't truly need to be the latest version, but you should keep it updated!)

### iPhone with static IP address

* This tutorial can be done with an Android phone, but I specifically cover how to use Siri Shortcuts with an iPhone to run the scripts on your full node. If you don't know how to assign a static IP address, I recommend this [guide](https://www.howtogeek.com/184310/ask-htg-should-i-be-setting-static-ip-addresses-on-my-router/) from howtogeek.com, which tends to offer comprehensive guides for neat stuff like this in an easily comprehensible way.

## Steps

### 1. The Script on the Server

Create a file on your server at ``/home/yourusername``, which should be the first directory you find yourself in immediately after logging in. Call it ``balance.py``.

```python
import json
import sys
from pprint import pprint

jdata = sys.stdin

data = json.load(jdata)

print "Your balance is " + str(round(data["cumulativetotal"], 2)) + " DCR"

jdata.close()
```

What this script does is take the json data piped into it (with the command you will see later) and print a human readable string: "Your balance is X DCR".

### 2. The Security

Go set up the static address assignment for your phone with the guide from the requirements section. You will need your phones MAC address.

We only want to allow ssh connections from IPs we specifically trust on our network to our full node.

If you're using a linux distro, firewall rules are simple with ufw. Use this [guide](https://www.digitalocean.com/community/tutorials/ufw-essentials-common-firewall-rules-and-commands#allow-incoming-ssh-from-specific-ip-address-or-subnet) to enable your phone to connect over SSH by allowing its IP address, which should not change because you've set up your router to assign your phone a static IP address when it's on your network.

### 3. The Siri Shortcut

With Shortcuts, Apple has allowed some power-user functionality that can really extend the utility of your phone. The shortcut we are creating here is very simple. Add a 'Run Script over SSH' step, and follow it up with a 'Notification' step.

In your script step, populate the name of your server, your username and password, and the command to run your script:

``dcrctl --wallet getbalance | python balance.py``

![Script Step](/img/shortcut-1.jpg)

In your notification step, you pass in the Shell Script Result from the previous step as your variable. You can then set your Title for the notification.

![Notification Step](/img/shortcut-2.jpg)

You're done! Now try running it and tweak whatever settings to your hearts content. When you run it, you should see something similar to below:

![Woohoo!](/img/shortcut-notification.jpg)

## No need to stop here

You've learned everything you need to create some advanced features accessible to you conveniently. Maybe you'll decide to figure out how to make this shortcut work from anywhere in the world. Create more scripts for different tasks. You can even return information for things completely unrelated to Decred if you want, though I'd recommend using a separate server. Do you frequently pay a particular address? You could script it and make it simple to issue your sendtoaddress command with a button press. You are running your own bank account, in a sense. You can build yourself utilities to bring the convenience you get from your fiat banking account apps, and then share the source with the world to enable more people to take back control of their personal finances.

Just keep in mind, the security of your server is important, and you want to be very conscious of any security trade-offs you make for convenience.

---
Now that you're done with this, give your grandparents a call. You know they'd appreciate it! I am bad with this myself. Staying in touch with family is important.
Check out the app [Uphabit](https://uphabit.com/) to set reminders to stay in touch with the important relationships in your life. They didn't pay me to mention this, but I use the free version of their app, so figured I'd give them a shout-out.
