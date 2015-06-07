# Requirements #

### Pirni, Mobile Terminal, and the **derv** scripts can only be installed and run on _Jailbroken_ iPhones and iPod Touches! ###
### If you are interested in Jailbreaking your device, [click here](http://www.iphonedownloadblog.com/2009/10/11/jailbreak-iphone-blackra1n-tutorial/) ###

Required jailbreak apps for **derv**:
  * [MobileTerminal](http://code.google.com/p/mobileterminal/)
  * [Pirni](http://code.google.com/p/n1mda-dev/wiki/PirniUsageGuide)
  * root access
  * Gawk
  * network-cmds

The above apps can be installed using Cydia, Rock, or Icy.  Simply search for them and install.

If you don't know what "root access" is, you probably already have it.

The following applications _should_ be included in the installation of Mobile Terminal and Pirni:
  * Bourne-Again SHell
  * grep
  * sed
  * libpcap
  * libnet

If these applications are not available from Mobile Terminal, attempt to install them using Cydia or Rock.


# Installation #

### Step 0 : Introduction ###

Unfortunately, the **derv** scripts are not available through Cydia or Rock at this time.

However, since **derv** is only a few shell scripts, they simply need to be copied to the device and then executed.

If you already have [OpenSSH](http://www.appleiphoneschool.com/openssh/) installed on your device, you could copy the derv scripts using programs like [WinSCP](http://winscp.net/eng/index.php), [KiTTy](http://kitty.9bis.com/), or some other SSH client.

Otherwise, you could use [iPhoneBrowser](http://code.google.com/p/iphonebrowser/) to easily copy the files onto the device.

### Step 1 : Copy the shell scripts to your device ###

My preferred location to copy the scripts to is:
```
/private/var/mobile/
```
Because Mobile Terminal defaults to this directory when it is loaded.

The five scripts you need to copy are:
  * b4.sh
  * derv.sh
  * derv-pw.sh
  * derv-cookie.sh
  * derv-url.sh

Once these files are copied, you can exit the iPhoneBrowser / SSH client.

These files only need to be copied to the device once. After they have been copied, the files will remain on the device even after reboot.


### Step 2 : Open Mobile Terminal ###

![http://imgur.com/rbknR.png](http://imgur.com/rbknR.png)

Once these scripts are copied to the iPhone/iPod, we need to execute them.
From your iPhone/iPod's home screen, open Mobile Terminal by tapping on it.

### Step 3 : Login as root ###

![http://i.imgur.com/ftOg1.png](http://i.imgur.com/ftOg1.png)

To login as root, type
```
su
```
followed by root's password.  Note: The screen will not acknowledge that you are typing the password, but it is receiving the key presses.

_The default password for root is **alpine**_

_Note: Having a default password for root is dangerous! You should change the password as soon as possible using the command:_
```
passwd
```

### Step 4 : Make sure we are in the correct directory ###

![http://i.imgur.com/7KCDL.png](http://i.imgur.com/7KCDL.png)

Type
```
ls
```
to make sure that all five derv scripts are in the current directory.


If they aren't found in the current directory, they _could_ be in the directory:
```
/var/mobile/derv/
```

Navigate until you are in the directory containing the derv scripts.

### Step 5 : Change permissions ###

![http://i.imgur.com/rRZYm.png](http://i.imgur.com/rRZYm.png)

By default, the scripts will not have proper file permissions to be run by root.

We can fix this problem with a one-liner:
```
chmod 777 *.sh
```

We only have to run this command once; after the permissions have been set, they remain that way, even after reboot.

### Step 6 : Run the first script ###

![http://i.imgur.com/BnJkK.png](http://i.imgur.com/BnJkK.png)

**b4.sh** is the script which initializes Pirni with the appropriate filters, router IP address, and dump file name.

To start this script, simply type:
```
./b4.sh
```
Be sure to include the dot and slash before the **b4.sh** (the ./ tells Mobile Terminal to run the file as a bash script).

This will start Pirni and show how many packets are captured.

Note: **b4.sh** can _also_ receive the target's IP address as an argument.  This is useful if you don't want to target an entire network; just one device on the network.

To do this, type:
```
./b4.sh 192.168.1.2
```
where **192.168.1.2** is the IP address of your target.


### Step 7 : Open a new Terminal and login as root (again) ###

Now, with the b4.sh script running, we need to open a **new** Terminal window.
To do this, simply swipe two fingers (at the same time) to the left (or right).  If done correctly, you should see a new terminal window.

![http://i.imgur.com/ftOg1.png](http://i.imgur.com/ftOg1.png)

We will need to login as root in this new terminal window as well.

In case you don't remember how to login as root, type:
```
su
```
then your password ... default is **alpine**, but you've already changed that, haven't you? :)

### Step 8 : Run the second script ###

![http://i.imgur.com/DSXX9.png](http://i.imgur.com/DSXX9.png)

In this new terminal window, now that you are logged in as root, you can run derv.sh!
This script accepts arguments, so you can narrow-down what you want filtered from the sniffed packets.
```
./derv.sh [options]
```

The options include:
  * -u       _prints out URLs as they are visited_
  * -p       _prints out plain-text passwords as they are received_
  * -c       _intercepts and injects certain cookies into the device's Safari's cookies file_
  * restore  _restores cookies_
  * -d       _filters through the packet file **once** and does not delete anything. Useful if you want to see what's in the packet dump file, but don't want to lose the data._


All of these options (except **restore**) can be used in combination with one another:

For example, if you wanted **derv** to only display sniffed plain-text passwords and URLs, then you would type:
```
./derv.sh -p -u
```

Or to have **derv** only read the packet file once, not delete the packet file, and inject found cookies into the device's Safari, you would type:
```
./derv.sh -d -c
```


### Step 9 : Stop! ###

![http://i.imgur.com/u0ywi.png](http://i.imgur.com/u0ywi.png)
![http://i.imgur.com/EfhGo.png](http://i.imgur.com/EfhGo.png)

When you're done collecting what you need, you will need to stop both **derv** and **pirni**.  This can be done by swiping up-right in Terminal, which is the equivalent of typing Control+C in Linux.

You will need to swipe up-right / ctrl+c in both terminal windows. Remember: Drag two fingers left or right to switch between Terminal windows.

After you are done, if you used the **-c** option, you can restore your Safari cookies by typing
```
./derv.sh restore
```
this will restore your cookies to their original state.

### Step 10 : Exit ###

MobileTerminal has a tendency to run in the background, using up resources.

Once you are done with Terminal, tap-and-hold your finger in the terminal window until a menu appears, then select EXIT.  Lastly, confirm that you want to exit Terminal.