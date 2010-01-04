derv


author: merkler
email:  derv82@gmail.com
date:   dec 13, 2009

=================================================

::OVERVIEW::

Pirni [available from Cydia] reads network packets by arpspoofing. 
Pirni is a great app, and has some filtering options [tcp, ports, 
etc] but cannot extract specific information from the packets.
The pirni FAQ suggests saving the packets to a file and then 
viewing them on a computer at a later time using WireShark or a 
similar packet-viewing app.



::THESE SCRIPTS::

derv is a collection of bash shell scripts which both start pirni 
with the correct settings and also filter through the outputted 
packet file every 5 seconds.

This prevents the pirni output file from getting too large, and 
also allows the user [you] to view network data in almost-realtime.



::USAGE::

b4.sh
----------------------
null       No arguments
           Starts pirni; targets entire network.
           
IPaddress  Starts pirni; targets specific IP address


derv.sh
----------------------
null No arguments
     Defaults to password sniffing only.

-d : Run Once & Do NOT Delete Packets
     Normally, derv.sh will read the packets as they are received, 
     parse out important information, then delete the packets.  
     This frees up disk space on the system while giving pseudo-
     realtime feedback on network traffic.
     
     If you want to keep the packets for later viewing, but would 
     like to see what derv.sh can find, this option is for you.  
     The log.pcap file containing the packets will not be altered.
     
     derv.sh will go through the log.pcap output file once, print 
     out any found information, and then exit.
     
-p : Plaintext Password Viewing
     This will display any plaintext passwords that are passed.
     This only works with HTTP plaintext passwords currently.
     
-u : URL Viewing
     This option displays all urls being visited by the 
     victim[s].  This is good when targetting a specific IP.
     
-c : Cookie Injection
     This option is kind of complicated.  It will look for
     cookies contained in packets. If the cookie is to a specific
     website in the list (amazon, facebook, twitter) then the 
     script ADDS this cookie to the iPhone's cookies file:
     
     /private/var/mobile/Library/Cookies/Cookies.plist
     
     This allows you to open safari (even while the script is
     running in the background) and impersonate the victims
     on those specific sites.
     
     Example: If derv displays that a cookie was found and
     injected (facebook.com), hit the home button [exit], open
     Safari, and navigate to 'facebook.com'.  If the cookie
     intercepted was a login cookie, you should be 'logged in'
     as the victim.

     Note: Your normal Safari cookies are backed up and will not
           be altered; more info is at the end of this readme.



::INSTALLATION::

Files needed for DERV to work:
 - MobileTerminal
 - pirni
 - sed
 - grep

All of the above files should be available from cydia.

Also required are the 5 derv *.sh files:
 - b4.sh           - initializing script
 - derv.sh         - main script
 - derv-cookie.sh  - cookie-injection script
 - derv-pw.sh      - password-reader script
 - derv-url.sh     - url-reader script

All of the derv *.sh files need to be in the same folder. I suggest 
/private/var/mobile because it's the first directory that 
MobileTerminal defaults to on load.



::EXECUTION::

To run DERV, you will need to use two separate MobileTerminal 
terminals. (To run two separate scripts).

(To switch between windows, drag left or right with 2 fingers)


STEP 0: SETUP

You *HAVE* to be root in BOTH terminal windows!


To log in as root, type 

su

Then enter your password [default password is 'alpine']

This needs to be done in the two terminal windows you will be using.



STEP 1: RUNNING THE FIRST SCRIPT

In one terminal window, run the initilization script:

./b4.sh

This will start pirni, targetting the entire network. The script 
automatically aprspoofs the Gateway [router].

To target individual ip addresses, type:

./b4.sh 192.168.1.39 

[...Or whatever IP address you want to target]

If successful, pirni will display packet sniffing information, and 
the number of packets received.

** The packets captured will be dumepd to the file log.pcap **



STEP 2: RUNNING THE SECOND SCRIPT

In the next terminal window, you need to run

./derv.sh

This will start the script which reads the packet file [generated 
by pirni] every 5 seconds and parses information from it.

By default, derv will only show plaintext passwords if no arguments 
are passed. There are other options:

./derv.sh -p    = same as no arguments: only shows passwords

./derv.sh -u    = only displays visted urls

./derv.sh -c    = only looks for and injects cookies

./derv.sh -d    = reads packet file once, doesn't delete it.
                 \_ useful if you want to keep the packets

You can use these three options in any order and combination.
For example:

./derv.sh -p -c    = grabs passwords and injects cookies,




STEP 3: WAITING FOR PACKETS

While pirni is gathering packets and derv is filtering pirni's
output, you can just sit and watch everything unfold.




STEP 4: HAD ENOUGH

To stop derv, type CTRL+C [in MobileTerminal, swipe up-right]

Don't forget to CTRL+C [up-right swipe] on the other terminal
window as well! [to stop packet forwarding].




STEP 5: RESTORE COOKIES [OPTIONAL]

After you're done using injected cookies, you can revert Safari 
back to its original cookies by typing:

./derv.sh restore

When derv injects cookies into Safari, it creates a backup of your 
current cookies before writing the sniffed cookies. The 'restore' 
command deletes the victim's cookies and restores your original 
cookies [the cookies you had before running derv.sh].



::CONCLUSION::

This script has a long way to go, but it works well with what it 
has.  

If it doesn't work for you...

Try changing the permissions on the files:

chmod 777 *.sh


Or you may need to change the default wifi interface for pirni..
in b4.sh, change 

pirni -i en0 
to 
pirni -i [your wifi interface, i.e. en1]
