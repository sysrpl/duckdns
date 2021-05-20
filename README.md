# Get a DDNS going with DuckDNS - Step by Step
## DDNS? What is that?
A dynamic DNS. DNS stands for Domain Name Server, which, in other words, is basically the server that answer your queries when you search for a hostname on the internet.

Example: If you search for "google.com" there will _always_ (probably) a server that points out to that hostname and tells you the information the computer needs; the IP address.

If you want to give it a test, you only have to open your terminal and type "ping google.com" and you'll see the real IP address appear! In my case it's 216.58.201.174 .

Anyways.. A DDNS is useful if you want to link or put a hostname to an IP that is not STATIC.

You see.. Google and other companies usually have STATIC IP addresses. It's easier to control and use. Only issue is that these are expensive. For someone who wants to get something static without having to ask their ISP for a bigger contract (and much more expensive) that involves your IP being static, you should look for dynamic DNS services.

What these do is the same as a normal DNS, they link a hostname to an IP address, but a DDNS has a twist.

A DDNS allows you to have a dynamic ip in combination to the static hostname linked!

## But how does it work?
Okay, so basically you sometimes need some kind of account linked to your persona to use that service, and sometimes it's not free (it's still way cheaper than having an STATIC PUBLIC IP on our router). You will also need the "server" to interact every x amount of time with the Dynamic DNS server so that they can make sure you always have the proper IP linked to the STATIC url.

## Okay, so how do i do this?

Since you will need a server anyways or some device to host whatever you want to have, you should use that device or a similar one to interact with this server through a script. It depends on the service you use, but in our case i will be using DuckDNS.

If we visit DuckDNS's website, we will see something like this;

![img](https://i.imgur.com/8UbTQhw.png)

What we will need to do is basically create an account, or login if we already have one. After that, something like this should appear;

![img](https://i.imgur.com/xHg1ej6.png)

We can create a domain here, call it however we want and give it our current public ip.

Done! or are we? I mean, it's not going to update anything currently. The second our ISP changes our Dynamic Public IP, this will not work anymore.

That's why i mentioned an automated script before. DuckDNS allows us to use a really simple script using cron (crontab). After we create our first domain, we need to head up to the "install" tab and follow the steps.

![img](https://i.imgur.com/xDzWKUU.png)

> **In my case i will be using "pi" or "linux cron", they're almost the same.**

Following the tutorial we just have to do these steps;

1. First of all we need to execute this command to make sure we have crontab running already.
> ps -ef | grep cr[o]n

2. After that, we should test if we have curl installed writting "curl" onto the terminal. If it returns some error mentioning curl not being installed we shall install it.
On Ubuntu/Debian systems you can install it like this;
> sudo apt install curl -y

3. If we already have it installed, or we just installed it right now, we now have to create the script itself. We can create anywhere where our user has the ability to create and modify files. In my case i'll create it here; **~/duckdns/duck.sh**

    To create those we will execute the following commands;
> cd ~

> mkdir duckdns

> cd duckdns

> touch duck.sh

4. After that, we have to modify our file. We can use whatever we want as a text editor but i will be using **nano**. We open the file and put this following information;
        
        echo url="https://www.duckdns.org/update?domains=**EXAMPLE**&token=**TOKEN**&ip=" | curl -k -o ~/duckdns/duck.log -K -

    As you can see, you have two values that are generic (aka **EXAMPLE** and **TOKEN**). These values are unique to the persona that is going to use it.

    The **example** value is basically the name or domain you used.
    The **token** value is the value shown in the initial webpage of DuckDNS after logging in for the first time. You can see it here;

![img](https://i.imgur.com/v6on1ng.png)

5. After this, we shall add executable permissions on the script and then add it to our crontab. First, the permissions;
    > chmod 700 ~/duckdns/duck.sh

    Then we do;
    > crontab -e

    And we add this one liner at the end of the file that will open after executing **crontab -e**;
    > */5 * * * * ~/duckdns/duck.sh >/dev/null 2>&1

    After this, we should execute the script once manually just to see it working;
    > cd ~/duckdns/
    
    > ./duck.sh

    If we then find a new file created called "duck.log" with "OK" inside, everything worked properly!

## What now?

Well you probably want to setup port forwarding on your router to make use of your new DDNS hostname.
