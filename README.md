tortbleed
=========

Modification of Samiux's Heartbleed Reload scanner to allow direct scanning of .onion addresses.

==========System Requirements==========

- SocksiPy ( Available here: https://github.com/mikedougherty/SocksiPy ).

- urllib2 ( Available here, amongst others for python2.7: 
  https://github.com/python-git/python/blob/master/Lib/urllib2.py ).

- socket ( Available here, amongst others for python2.7: 
  https://github.com/python-git/python/blob/master/Lib/socket.py ).

- A local tor server configured to allow you to connect through it to endpoints.  You do not 
  necessarily need to configure it as a relay, so long as your IP is configured in a 
  'SocksPolicy accept' rule.  Being configured as a relay will not have a negative impact, 
  though, should you have that set-up.

- Python 2.7, as this was the only version I tested this script against.  Please feel free to 
  modify it to work with whatever version you might be using other than 2.7.

==========Setup & Usage==========

You must do the following, or this scanner just wont work:

1. Ensure you have installed and setup Tor on a system accessible to you to ensure you can 
   bounce through it via this script.

2. Edit your copy of torwrapper.py to suit your Tor instance ip / hostname and port to access, 
   on lines 11 & 12.

3. Copy the edited torwrapper.py file to your Python site-packages directory.

4. Ensure you have installed the SocksiPy, socket, and urllib2 site-packages properly.

Once those steps are completed, you can then run the scanner.  The usage output is below, along 
with an example with output:

# ./tortbleed.py <snipped for privacy>.onion
Starting Tor Daemon & Connecting to Tor Network...
Connected to Tor Relay.
My ip is 188.138.9.49 (atlantic480.us.unmetered.com)
Sending Client Hello...
Waiting for Server Hello...
 ... received message: type = 22, ver = 0302, length = 66
 ... received message: type = 22, ver = 0302, length = 2483
 ... received message: type = 22, ver = 0302, length = 331
 ... received message: type = 22, ver = 0302, length = 4
Sending heartbeat request...
Unexpected EOF receiving record header - server closed connection
No heartbeat response received, server likely not vulnerable

# ./tortbleed.py 192.168.1.43
Connecting to non-onion address...
Sending Client Hello...
Waiting for Server Hello...
 ... received message: type = 22, ver = 0302, length = 58
 ... received message: type = 22, ver = 0302, length = 961
 ... received message: type = 22, ver = 0302, length = 525
 ... received message: type = 22, ver = 0302, length = 4
Sending heartbeat request...
 ... received message: type = 24, ver = 0302, length = 16384
Received heartbeat response:
  0000: 02 40 00 D8 03 02 53 43 5B 90 9D 9B 72 0B BC 0C  .@....SC[...r...
  0010: BC 2B 92 A8 48 97 CF BD 39 04 CC 16 0A 85 03 90  .+..H...9.......
  0020: 9F 77 04 33 D4 DE 00 00 66 C0 14 C0 0A C0 22 C0  .w.3....f.....".
  0030: 21 00 39 00 38 00 88 00 87 C0 0F C0 05 00 35 00  !.9.8.........5.
  0040: 84 C0 12 C0 08 C0 1C C0 1B 00 16 00 13 C0 0D C0  ................
  0050: 03 00 0A C0 13 C0 09 C0 1F C0 1E 00 33 00 32 00  ............3.2.
  0060: 9A 00 99 00 45 00 44 C0 0E C0 04 00 2F 00 96 00  ....E.D...../...
  0070: 41 C0 11 C0 07 C0 0C C0 02 00 05 00 04 00 15 00  A...............
  0080: 12 00 09 00 14 00 11 00 08 00 06 00 03 00 FF 01  ................
  0090: 00 00 49 00 0B 00 04 03 00 01 02 00 0A 00 34 00  ..I...........4.
  00a0: 32 00 0E 00 0D 00 19 00 0B 00 0C 00 18 00 09 00  2...............
  00b0: 0A 00 16 00 17 00 08 00 06 00 07 00 14 00 15 00  ................
  00c0: 04 00 05 00 12 00 13 00 01 00 02 00 03 00 0F 00  ................
  00d0: 10 00 11 00 23 00 00 00 0F 00 01 01 00 00 00 00  ....#...........
  --snipped output for readme brevity-- 
WARNING: server returned more data than it should - server is vulnerable!

#

==========Troubleshooting==========

You may get tracebacks because I haven't included any error handling.
The two most common I found during testing are explained below.

1 - socks.Socks5Error: ((2, 'connection not allowed by ruleset'),)
This occurs when the Tor Relay you are connected to for this particular scan doesn't allow 
outgoing connections to the .onion you are attempting to scan.  Most often, attempting another 
run of the same command will connect you to another Relay and allow this to go through.  If, 
however, you hit the same error, another few attempts may be required.

2 - socks.Socks5Error: ((91, 'request rejected or failed'),)
This happens when connection to the .onion endpoint fails to successfully complete through the 
Tor Relay you are connected through.  This can be for a number of reasons, and may be worth 
trying once more.  Same result usually means problems with the .onion server (either not having 
the port you're attempting to scan on enabled, or other connection-related issues).

Good luck!

- Mike <michael@prattlecorp.com>
