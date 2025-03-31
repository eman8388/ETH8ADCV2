
![snapshot](https://github.com/eman8388/ETH8ADCV2/blob/main/screenshot/Screenshot%202025-03-31%20alle%2008.20.15.png)

# ETH8ADCV2 VST3 Plug-In
Open-source example of a communication .vst3 application for the **ETHlink8ADC** Ethernet audio interface. 
Project repository: [ETHlink8ADC](https://github.com/eman8388/ETHlink8DAC).

**Built for macOS M-series machines.**

---

## Main Features

- Optimized for **macOS M-series** and fully tested on **Reaper** and **Ableton Live**.
- Developed in **C** using the **Steinberg VST3 SDK**—fast, lightweight, and stable.
- **Plug & Play**: Just load the VST3 on a **stereo or multichannel track**.
- **Ultra-low latency** communication: latency is manualy set via delay parameter in the UI depending on network quality (packet delay can go from 8 to 2048 packet).
- **No drivers required**, no setup hassle.
- **Automatic reconnection**: If the connection drops, simply reconnect the device or network interface—no need to restart anything.
- The **DAW’s sample rate and buffer size** automatically configure the device’s settings.

---

## Installation 

Simply copy [ETH8ADCV2.vst3](https://github.com/eman8388/ETH8ADCV2/blob/main/ETH8ADCV2_VST3/ETH8ADCV2.vst3) file in the vst3 system folder (or wherever you want) and rescan the vst3 plugin in your DAW. 

---

## How It Works

This is a simple example of a **communication VST3 plugin** using **Berkeley Packet Filter (BPF)** to transmit and receive **raw Ethernet frames** through the **ETHlink8ADC** device.

1. Connect the device to your **local network**.
2. Attach the **Ethernet adapter** to your computer.
3. Insert the **VST3 plugin** into a track in your favorite **DAW** (one instance per device on the network).
4. From the UI:
   - Select the **network interface** connected to the device.
   - Choose the **serial number** of the target device.
   - Choose the **delay** (packet delay) depending on your network infrastructure quality.
   - Press **Connect**—if the communication starts, the **red LED** on the UI will turn on, and the **status LED** on the device will turn **green**.
   - The **Pass** button enables **pass-through mode**, allowing audio to bypass the plugin and be summed to the received audio.

The remaining UI elements are **self-explanatory** .

### For Reaper USER
    - In Reaper, VST preprocessing is default ENABLE, for obvius reason should be disable.
    - When record the track, be sure to record the OUTPUT (post fx).

---

## 🛠️ Developer Insights

> (USELESS! DON’T READ THIS SECTION! 🙃)

The best setup for this communication method is a **simple Layer 2 Ethernet switch-based network** with as few switches in series as possible.

### Porting to Other Systems

- **Linux**: Easy! Linux natively supports **BPF** or **RAW sockets**, making direct raw Ethernet frame transmission straightforward.
- **Windows**: Not so easy! Windows lacks native support for raw Ethernet frame transmission. However, I tested two potential solutions (**not published for legal reasons**):
  1. **Using third-party network libraries and drivers (Npcap)** – Easy to implement and works very well.
  2. **Developing a custom filter driver** to expose an IOControl interface to userspace – More complex, potentially more powerful, but with no significant performance gains over Npcap.


### The Big Question: **Why Not Just Use UDP & Sockets?**

Good question! 

From the device’s perspective, there’s no problem using standard **UDP/IP communication**. The device could use a ready-made **LWIP TCP/IP stack**, allowing it to communicate seamlessly with the computer over a traditional network. This would provide advantages like **Wi-Fi support** (since raw Ethernet frames aren’t compatible with Wi-Fi unless you have expensive APs that allow Layer 2 protocol translation). However, **Layer 2 communication is the fastest option** for an embedded system because it bypasses **LWIP overhead**, allowing direct interaction with the **MAC driver** of the MCU. This also enables **DMA-based packet transmission with system interrupts**, eliminating the need for a dedicated **RTOS task**. Additionally, using raw Ethernet allows compatibility with **affordable PoE switches**.

However, the **real problem is on the computer side**. After months of research, the key reasons for avoiding **UDP/IP** are:

### Performance & Control Issues

1. **OS TCP/IP stacks introduce latency**
   - Every OS processes all **IP, ICMP, and ARP** packets through the firewall, adding **delay and CPU overhead**.
   - Even if you bypass some parts of the stack, **developers can’t fully control socket behavior**:
     - **Watermarks & buffers**: The OS decides the actual buffer sizes and queue thresholds, ignoring developer-set values.
     - **Queue management**: The OS dynamically handles socket queues, which can cause **latency spikes** and **sync issues**.

2. **Device Identification Complexity**
   - With raw Ethernet, devices are identified by **MAC address + serial number**.
   - With UDP, devices are identified by **IP addresses**, which are **not static**.
   - Identifying a device’s IP address requires complex and unreliable methods:
     - **ICMP Broadcast Ping** – The best solution, but **blocked by most modern OSs**.
     - **UDP Broadcast Ping** – Another option, but also restricted.
     - **ARP Table Query** – OSs update their ARP tables **infrequently**, making this unreliable.
     - **Other workarounds** exist but come with significant downsides.


Using **raw Ethernet communication** simplifies device identification, reduces **latency**, and removes **OS-imposed restrictions** on networking parameters. This approach provides the **best real-time performance** without requiring expensive hardware or additional software layers.

### Building, modify and testing

The zip [ETH8ADCV2_VST_DEV](https://github.com/eman8388/ETH8ADCV2/blob/main/ETH8ADCV2_VST3_DEV.zip) contein ONLY the file wich contain the important sorce code (for build the vst3 you need to download vst3sdk, create a new project and copy the code from the folder in the proper directory) and the grapic element. 


### 📜 Legal Disclaimer

This project uses the **Steinberg VST3 SDK** under the **Steinberg VST3 License Agreement**. 
VST is a trademark of **Steinberg Media Technologies GmbH**.
For more details, visit the official Steinberg website: [Steinberg VST](https://www.steinberg.net/en/company/developer.html).

---

Eugenio Mancini\
 [mancini97email@gmail.com](mailto\:mancini97email@gmail.com)