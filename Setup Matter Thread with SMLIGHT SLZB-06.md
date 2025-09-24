This has been sitting as a draft for a Reddit topic for months - finally added some screenshots and posted it, but it kept being deleted over and over.
Not sure why, no mods would answer me. But I found it hard to find a guide that worked. And from my research on forums, so did many others. So here is the best I could do;



Recently, I bought a **SMLIGHT SLZB-06** because I wanted to get **Matter over Thread** working. The plan was to buy a Tado X, but since I had no Matter devices or a Thread network yet, I grabbed a simple power plug (**Onvis B0C4H1JYHN**) to test things out. Unfortunately, Matter over Thread devices were a bit pricier than their Zigbee counterparts, but I figured it was worth it to experiment.

I followed the [official guide](https://smlight.tech/manual/slzb-06/guide/thread-matter/) from SMLIGHT (they also have a [YouTube video](https://www.youtube.com/watch?v=WwYVRuVpAJI)), but things didn’t go smoothly. A quick Google search revealed plenty of people struggling with the same issue - Matter over Thread wouldn’t work properly with the **SMLIGHT SLZB-06**.

So, I dug through multiple forum posts, tried different fixes, and combined everything into this post—merging official instructions with my own troubleshooting notes and discoveries. Hopefully, this helps others who are just getting started with **Matter & Thread** like me.

# 🌐 Understanding Where Matter & Thread Fit

If you’re new to Matter and Thread, here’s a quick breakdown of where they sit in the smart home tech stack:

* **Hardware** → The physical devices in your home (switches, bulbs, vacuums, etc.).
* **Data Link Layer** → The communication tech (Ethernet, Wi-Fi, Thread) that connects devices.
* **Network Layer** → Protocols like TCP, UDP, and IPv6 that move data between devices.
* **Application Layer** → This is where **Matter** operates, defining how devices talk to each other.
* **Ecosystems** → Platforms like Alexa, Google Home, SmartThings, HomeKit, and Home Assistant that integrate everything.

# 🛠 My Network Setup

I use **Ubiquiti Unifi** equipment and created a separate VLAN specifically for **Home Assistant**. The **SMLIGHT SLZB-06** is also in this VLAN.

Additionally, I set up a new Wi-Fi network in the same VLAN with **"Enhanced IoT Connectivity"** enabled. This helps optimize connections for smart home devices.

**Why this setup?**  
✅ My phone can connect to this Wi-Fi for pairing Matter over Thread devices (scanning the QR code).  
✅ Even if your phone is on another VLAN, it *should* still work if **mDNS** is enabled and you have **IPv6** properly configured on all affected VLANs.

# 🔧 My Unifi Network Settings

**Network settings:**

![img](cvadf7zgqbme1 "Unifi Network Settings IPv4")

![img](akj1n90jqbme1 "Unifi Network Settings - IPv6")

![img](p33l07boqbme1 "Unifi Network Wifi settings 1/2")

# 📡 My Wi-Fi Settings

![img](62ndlttqqbme1 "Unifi Network Wifi settings 2/2")

I kept my IoT Wi-Fi **2.4GHz-only** since most smart home devices prefer it.

I don’t have IPv6 from my ISP (as far as I know), but I enabled it on the VLAN anyway. I also turned on **mDNS**, though it may not be necessary since my phone is in the same VLAN as Home Assistant and the SMLIGHT SLZB-06.

If you need to set up **ULA IPv6 ranges**, here are some valid ones:

* `fd8a:123b:c4d5:10::/64`
* `fd1c:e56f:78a9:20::/64`
* `fd9b:234c:d6e7:30::/64`
* `fd00:aaaa:bbbb:10::/64`
* `fd11:2222:3333:20::/64`
* `fd22:4444:5555:30::/64`
* `fd33:6666:7777:40::/64`
* `fd44:8888:9999:50::/64`
* `fd55:aaaa:cccc:60::/64`
* `fd66:bbbb:dddd:70::/64`
* `fd77:cccc:eeee:80::/64`
* `fd88:dddd:ffff:90::/64`
* `fd99:eeee:aaaa:100::/64`
* `fdaa:ffff:bbbb:110::/64`
* `fdbb:aaaa:cccc:120::/64`
* `fdcc:bbbb:dddd:130::/64`
* `fddd:cccc:eeee:140::/64`
* `fdee:dddd:ffff:150::/64`

# 📋 Home Assistant Network Settings

When scanning a Matter QR code in **Home Assistant**, your phone **discovers the device and pairs over Wi-Fi**. After setup, the device no longer needs to stay connected to Wi-Fi.

**💡 Important:**  
For me, **scanning the QR code failed every time** *until I enabled IPv6*. It *never* worked without IPv6.

# 🚀 Setting Up Matter over Thread

Below is the **official guide from SMLIGHT**, with added screenshots and notes from my experience.

# Prerequisites

✅ Home Assistant (latest version preferred).  
✅ SLZB-06 (or SLZB-07) with **OpenThread Border Router firmware**.  
✅ A Matter device (e.g., Onvis B0C4H1JYHN EU power plug).  
✅ An Android smartphone (iPhones may not work for pairing).

# Flashing OpenThread Border Router Firmware

* Plug the **SMLIGHT SLZB-06** into your network.
* Find its IP in **Unifi** → Open the IP in your browser.
* Select **Matter-over-Thread Mode** → Wait for firmware to update.

📸 **SLZB-06:**

![img](bgh6dzyhrbme1 "I'm running firmware v2.7.1")

**Setting Up the Thread Border Router Add-on (USB Connection)**

You can choose, do it over USB or over the network. Scroll further down for information on how to do it over the network.  I set it up using the USB cable at first, but then removed the cable and edited the *OpenThread Border Router Add-on* to update the USB connection from the actual connection to /dev/ttyS3 and unplugged the USB cable.

1. Plug a **USB cable** into the SMLIGHT SLZB-06 and your Home Assistant device.
2. Install the **OpenThread Border Router Add-on** in Home Assistant.
3. Configure the add-on:
   * **Port:** Select the USB device.
   * **Baud Rate:** 460k (or check your firmware).
   * **Flash Firmware:** **No** (it’s already flashed).
   * **Start the Add-on** and enable **Start on boot + Watchdog**.
4. Check the logs to confirm it’s running.

# Setting Up the Thread Border Router Add-on (Network Connection)

If you prefer network mode instead of USB:

1. Install the **OpenThread Border Router Add-on** in Home Assistant.
2. Configure it:
   * **Port:** Select any USB device (a workaround for HA).
   * **Network Device:** [`192.168.1.10:6638`](http://192.168.1.10:6638) (your SMLIGHT’s IP & port).
3. Start the add-on and enable **Start on boot + Watchdog**.

**Setting Up Matter Integration in Home Assistant**

1. Go to **Settings > Devices & Services** → **Add Integration** → Search for *Matter*.
2. Keep **Use the official Matter Server Supervisor add-on** checked → **Submit**.
3. Start the Matter Server add-on → Enable **Start on boot + Watchdog**.

# Final Steps: Adding the Matter Device

1. Open **Home Assistant** → **Settings > Devices & Services** → **Devices Tab**.
2. Click **+ ADD DEVICE** → Choose **Add Matter Device**.
3. Scan the **QR code** on your device (or enter pairing code manually).
4. If asked, choose **"Open with Home Assistant"** when prompted.
5. Follow the pairing process:
   * **Connecting to device...**
   * **Generating Matter credentials...**
   * **Connecting device to network...**
   * **Checking network connectivity...**
   * **Connecting device to Home Assistant...**
   * **Device connected!** 🎉

# 🎯 Conclusion

Matter over Thread was **not plug-and-play** for me, and **it only worked after setting up IPv6**. If you're struggling, make sure:  
✅ Your network has IPv6 enabled.  
✅ Your phone is on the same VLAN (or mDNS is working).  
✅ You're using the latest firmware for SLZB-06.

Hope this helps someone! Let me know if you have any questions or if you got it working differently. 🚀
