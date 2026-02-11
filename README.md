# BLE vs UDP vs TCP
Technical Analysis: BLE vs UDP vs TCP for iOS-Connected Wearables


Why TCP Works When BLE and UDP Failed for On-Body Wireless Sensors

When building wearable sensors that communicate with an iPhone, I tested three approaches with very different results.

BLE: Failed at Body Distance
BLE operates at 2.4 GHz, which the human body significantly attenuates. Research shows:

* Body shadowing causes 15-20 dB signal loss in typical scenarios¹
* Cross-body transmission (one side to the other) can see 60-80 dB attenuation²
* Water absorbs 2.4 GHz at ~330 dB/m, and the human body is ~60-70% water³

When sensors are strapped to ankles, the signal path to a phone in hand/pocket passes through or around legs and torso—enough obstruction to cause connection drops.

UDP over WiFi Hotspot: Failed Silently
Sensors connected to iPhone's Personal Hotspot and sent UDP packets. The iOS app's NWListener showed "ready" but never received data.
According to Apple's official documentation, iOS Local Network Privacy works asymmetrically:

Receiving incoming UDP unicast: does NOT require permission⁴
Sending UDP: requires permission⁴
Listening for TCP connections: does NOT require permission⁴

The likely issue: without the app sending any outbound traffic first, there's no NAT state or permission trigger. The UDP packets may arrive at the iPhone but have no established path to the app.
TCP over WiFi Hotspot: Worked Immediately
With TCP, the sensor initiates a connection to the iOS app's NWListener. Apple explicitly states:

"Using NWListener to listen for incoming TCP connections does not require local network access. If you start a listener and someone connects to it then the connection will go through."⁵

The TCP handshake creates connection state that routes all subsequent packets correctly.

References:

1. PMC article "Analysis of Human Body Shadowing Effect on Wireless Sensor Networks Operating in the 2.4 GHz Band"
2. Electronics Weekly, "Wearable Electronics: Keep Body-Worn Wireless Devices Connected"
3. ScienceDirect, "Signal Attenuation" chapter
4. Apple Developer Forums, Local Network Privacy FAQ-2
5. Apple Developer Forums, iOS 14.0.1 UDP Receive discussion (Quinn "The Eskimo!")


