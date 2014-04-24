tortbleed
=========

Modification of Samiux's (http://samiux.blogspot.com/) Heartbleed Reload (from 4/11) scanner to allow direct scanning of .onion addresses.

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
```
$ ./tortbleed.py -c 1 3g2upl4pq6kufc4m.onion
Starting Tor Daemon & Connecting to Tor Network...
Connected to Tor Relay.
My ip is 109.163.234.7 (edwardsnowden0.torservers.net)

====================================
Server: 3g2upl4pq6kufc4m.onion
Run number: 1
====================================

Sending Client Hello...
Waiting for Server Hello...
 ... received message: type = 22, ver = 0302, length = 66
 ... received message: type = 22, ver = 0302, length = 2483
 ... received message: type = 22, ver = 0302, length = 331
 ... received message: type = 22, ver = 0302, length = 4
Sending heartbeat request...
Unexpected EOF receiving record header - server closed connection
No heartbeat response received, server likely not vulnerable.

====================================

Done, exiting.
```
```
$ ./tortbleed.py -c 1 192.168.1.43
Connecting to non-onion address...

====================================
Server: 192.168.1.43
Run number: 1
====================================

Sending Client Hello...
Waiting for Server Hello...
 ... received message: type = 22, ver = 0302, length = 58
 ... received message: type = 22, ver = 0302, length = 437
 ... received message: type = 22, ver = 0302, length = 397
 ... received message: type = 22, ver = 0302, length = 4
Sending heartbeat request...
 ... received message: type = 24, ver = 0302, length = 16384
Received heartbeat response:
  0000: 02 40 00 D8 03 02 53 43 5B 90 9D 9B 72 0B BC 0C  .@....SC[...r...
  0010: BC 2B 92 A8 48 97 CF BD 39 04 CC 16 0A 85 03 90  .+..H...9.......
  0020: 9F 77 04 33 D4 DE 00 00 66 C0 14 C0 0A C0 22 C0  .w.3....f.....".
				--snipped output remaining output for readme brevity--

WARNING: server returned more data than it should - server is vulnerable!

====================================

Done, exiting.
```
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

3 - "Help, there's lag in the data returned from the server for .onion URL's!" 
This is due to the delay introduced in general when transmitting / receiving over the Tor network.
Additionally, some exit nodes / relays introduce a bandwidth throttle for outgoing traffic to
limit the impact on the non-Tor network traffic of the host.  This is not the fault of the script,
but rather of the Tor network and/or Relay operator.  Try running the script again to potentially
hit a new exit node / relay and potentially improve that performance (or worsen it! :P)

Good luck!

Mike <michael@prattlecorp.com>
