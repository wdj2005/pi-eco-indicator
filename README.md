# agile-blinkt-indicator
Display upcoming Octopus Agile prices on the Pimoroni Blinkt! display for Raspberry Pi

# Hardware needed

- Pimoroni Blinkt!, https://shop.pimoroni.com/products/blinkt
- A Raspberry Pi of any flavour, as long as you can connect the Blinkt! to it. This has been tested on a Pi Zero W and a Pi 3B+.

# Software needed

- This has been tested on Raspberry Pi OS Buster only.
- You will need the Pimoroni Blinkt! Python library, https://github.com/pimoroni/blinkt
- Install the Blinkt! library like so:
```
curl https://get.pimoroni.com/blinkt | bash
```

# How to use this code

This code runs unprivileged - no sudo required. Copy it to your home directory in a folder of your choice. It will drop a SQLite database file in there when it runs.

Make the files executable:

```
chmod +x *.py
```

You should initially run manually to check everything works, replacing X with your DNO region:

(https://en.wikipedia.org/wiki/Distribution_network_operator):  
A = East England  
B = East Midlands  
C = London  
D = North Wales, Merseyside and Cheshire  
E = West Midlands  
F = North East England  
G = North West England  
P = North Scotland  
N = South and Central Scotland  
J = South East England  
H = Southern England  
K = South Wales  
L = South West England  
M = Yorkshire  

```
./store-prices.py --region X
```

The code will tell you what it's doing and whether it worked. You can run this as many times as you like without causing too many problems. 

Then, a separate command to update the display:

```
./update-blinkt.py
```

This will also tell you what it's doing, as well as showing you the colours it is setting.
If it's all a bit much, you can blank the display:

```
./clear-blinkt.py
```

# Running automatically
I really can't be bothered to make a systemd timer/service for this. `cron` is so much easier!
Run `crontab -e` and do something like this (don't forget to update your region code:)

```
@reboot sleep 30; cd /home/pi/agile-blinkt-indicator && /usr/bin/python3 store-prices.py --region X; update-blinkt.py
*/30 * * * * sleep 5; cd /home/pi/agile-blinkt-indicator && /usr/bin/python3 update-blinkt.py
30 16 * * * cd /home/pi/agile-blinkt-indicator && /usr/bin/python3 store-prices.py
30 18 * * * cd /home/pi/agile-blinkt-indicator && /usr/bin/python3 store-prices.py
30 20 * * * cd /home/pi/agile-blinkt-indicator && /usr/bin/python3 store-prices.py
```
- line 1: wait 30 seconds at startup, get new prices and update the display
- line 2: wait till 5 seconds past every half hour and update the display
- lines 3, 4, and 5: update the price database at 4.30pm, 6.30pm, and 8.30pm to cover late arrival of data

If it stops working, log in and run it manually and see what it moans about!

# To Do:

- better retry if data is late, 3 cron jobs is hacky
- colours could depend on daily average rather than fixed values
- configuration options?
- logging of issues
