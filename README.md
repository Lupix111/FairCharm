# FairCharm

<img width="972" height="651" alt="FairCharm" src="https://github.com/user-attachments/assets/00c2e63d-2ba9-4805-85c5-c2b35edb9aee" />

A pocket camera that fits in your palm and can hang from your bag like a charm! It's fair because it's open-source and free for everyone. *The open camera you actually own.*

> [!NOTE]
> **Project status:** the PCB and the firmware are currently being tested on real hardware. Battery life, the power-cut behaviour and WiFi range are still to be verified, so take the numbers in this README as "expected", not "measured". I'll update everything as soon as I have real data!

**The people at PCBWay decided to help me out with the printed circuit board, shoutout to Aran and the fine people that work at [PCBWay](https://www.pcbway.com), they're providing me with a tight and clean PCB that will make this project free of any tangled and messed up wirenest. Go to PCBWay to get the PCB for the best price ever!**

Just an ESP32-CAM project that came to my mind when I saw a certain company make these pocket cameras with a 1.6 megapixel camera that are kinda trash and way too stupidly expensive.

## What can it do?

- Takes photos with the OV2640 sensor of the ESP32-CAM (up to UXGA, 1600x1200, so roughly 2 megapixels) and saves them as JPG on a microSD card.
- Beeps to tell you what's going on (ready, photo taken, error, going to sleep...). No screen needed!
- Creates its own WiFi network. Connect your phone, open a web page and swipe through your photos in a little carousel. No app, no cloud, no account.
- Can optionally send every photo to a **Telegram** chat or a **Discord** channel as soon as you take it.
- Can be updated over WiFi (**OTA**), so you don't need to unscrew it and plug cables every time there's a new firmware.
- Goes to sleep by itself when you're not using it to save battery.

## What you need

For this project you're gonna need a grand total of 9 pieces of hardware (not counting the wires):

- 1x **ESP32-CAM** (the AI-Thinker one with the OV2640 camera and PSRAM).
- 1x **Charging module with a boost converter to 5V**: the **CKCS PB0A** (USB-C, charges a single LiPo cell and boosts it to 5V). I initially planned to use a TP4056 but quickly found out that the ESP32-CAM needs 5V to function properly. [I now am the proud owner of 10 useless TP4056 modules]
- 1x **500mAh LiPo battery**, single cell (1S, 3.7V). The small flat ones like the 502035 or 503035 sizes are perfect.
- 1x **Big red button** (or any colour really), the 12x12mm clicky ones. This is the shutter!
- 1x **SMD Passive buzzer, MTP-5020**, it has to be *passive*, an active one has its own oscillator and won't play the different beeps. 
- 1x **microSD card**, formatted as FAT32. The ESP32-CAM datasheet officially says up to 4GB, bigger cards usually work too but test yours before trusting it.
- 1x **FairCharm PCB** (optional but highly suggested, it's much easier to use the PCB than doing it by hand).
- 1x **small 6x6 tactile switch** for the power button. 
- 1x **SMD or THT Capacitor, 10v 330uF, 6.3x5.4**.
- You'll also need some **3D printed parts** so you're either gonna have to buy them OR ask a friend that got a printer.

There's also a **template to customize the back and the front** of the camera, you just need GIMP and some sort of idea on what to put there, after that it's only scissors, glue and...voilà! And there's the **STL files** for the body of the camera. 

> [!WARNING]
> **Charging safety, please read!** The PB0A charges at up to **2.1A** by default. That is a LOT for a 500mAh pouch cell (small LiPos like these are usually charged at around 0.3C to 1C, so roughly 150 to 500mA). The module can only lower that limit through its I2C interface, which this project doesn't use (yet). Until that's solved, please measure the real charging current with a USB power meter on your first charge, don't charge it unattended, and stop immediately if the battery gets warm or puffy. If you want a worry-free setup, use a bigger battery or a cell that is rated for fast charging.

## Using it

1. Put the microSD card in the ESP32-CAM.
2. Press the **power button** (the one on the K pin of the charging module). The module has no on/off switch, that's the button that turns the 5V on.
3. Wait for **2 short beeps**. That's the "I'm ready let's go" signal.
4. Press the **big red button** to take a photo. You'll hear 2 rising beeps when it's saved.
5. Done! The photos are on the SD card as 'photo_00001.jpg', 'photo_00002.jpg', and so on. The counter keeps going even after a reboot, so old photos never get overwritten.

The small flash LED on the ESP32-CAM stays **dimly lit** while the camera is on, it's on purpose: it's the "yes, I'm awake" light (it shares a pin with the shutter button).

### What do the beeps mean?

| Sound | Meaning |
|-------|---------|
| 2 short beeps | Ready to shoot |
| 2 rising beeps | Photo saved |
| 1 short high beep (after a photo) | Photo uploaded to Telegram / Discord |
| 1 long low beep (after a photo) | Upload failed (the photo is still safe on the SD card) |
| 2 low beeps | Error: the camera or the SD card didn't start, or the photo couldn't be saved |
| 3 falling beeps | Going to sleep |

### Sleeping and waking up

After **60 seconds** without doing anything the camera goes into deep sleep. About 30 seconds later the charging module notices that almost no current is being used and cuts the 5V completely (that's its default behaviour, and it's actually great for the battery).

- Press the **big red button** to wake it up (works while it's still in deep sleep, so in the first ~30 seconds, or when it's plugged into USB-C).
- If the power was cut, press the **power button** on the charging module to turn it on again.

If your phone is connected to the camera's WiFi the sleep timer is 5 minutes instead of 1, so you have time to browse your photos or update the firmware.
(but you can totally change this and give yourself more time)

### Seeing your photos on your phone

1. Connect to the WiFi network called **'ESP32-CAM'**. The default password is **'12345678'**. Please change it, it's in the firmware ('ap_ssid' and 'ap_password')!
2. Open **http://192.168.4.1** in your browser.
3. You'll see the carousel: newest photo first, ◀ goes to the older one and ▶ to the newer one. You can also use the arrow keys on a computer or swipe on a phone. The **Download** button saves the current photo.

The ESP32 is a tiny chip, so photos take around 1 to 3 seconds to load. Be patient with it, it's doing its best!

## Building it

### With the PCB (the easy way)

Solder the parts to the PCB, plug in the ESP32-CAM, solder the battery wires to the battery pads of the charging module and you're done. No wirenest.

> [!WARNING]
> The CKCS PB0A has **no reverse polarity protection**. If you connect the battery the wrong way, the module is dead, dead-dead-dead. The battery symbol on the module has a tiny "pin": that's the **positive** terminal (BAT+). Check your battery wires with a multimeter, cheap LiPos often come with the red and black wires the other way around from what you'd expect!

### By hand (the hard way)

If you really wanna do it by hand, this is how everything connects:

| From | To |
|------|----|
| PB0A 'OUT+' | ESP32-CAM '5V' |
| PB0A 'OUT-' | ESP32-CAM 'GND' |
| Battery '+' / '-' | PB0A battery pads (mind the polarity!) |
| PB0A 'K' | 10kΩ resistor, then the power button, then 'GND' |
| Shutter button | ESP32-CAM 'IO4' and 'GND' |
| Buzzer | ESP32-CAM 'IO12', through a 330Ω resistor, to 'GND' |

For the buzzer, put a small diode (like a 1N4007 or a 1N4148) in parallel to it, with the stripe (cathode) on the side of the pin. It protects the pin from voltage spikes. Also put a bulk capacitor (something like 220uF) between 5V and GND, close to the ESP32-CAM. When you unplug the USB-C cable the charging module has a tiny power gap that can reboot the camera, the capacitor smooths it out.

**Don't connect the shutter button and the power button together!** The charging module reads two quick presses on its button as "turn off", so they have to be two separate buttons.

### Pin map

| Function | ESP32-CAM pin | Notes |
|----------|---------------|-------|
| Shutter button | GPIO4 (to GND) | Internal pull-up. It's also the flash LED line |
| Buzzer | GPIO12 | Boot pin: it must be LOW when the chip starts, the buzzer to GND takes care of it |
| SD card | 1-bit mode | Frees GPIO4, 12 and 13 (writing speed is a bit lower, still plenty for photos) |
| Power button | 'K' pin of the PB0A | With a 10kΩ resistor in series |
| Programming header (J1) | IO0, GND, TX, RX | Only needed to flash the firmware the first time |
| Not used | GPIO13, GPIO16 | **Don't touch GPIO16**, it's wired to the PSRAM |

## Flashing the firmware

### 1. Set up the Arduino IDE

Install the **ESP32 boards package** (by Espressif Systems) in the Arduino IDE. You don't need any extra library, everything the firmware uses comes with it.

Then use these settings:

| Setting | Value |
|---------|-------|
| Board | AI Thinker ESP32-CAM |
| CPU Frequency | 240MHz (WiFi/BT) |
| Flash Mode | QIO |
| Flash Frequency | 80MHz |
| **Partition Scheme** | **Minimal SPIFFS (1.9MB APP with OTA/128KB SPIFFS)** |
| Upload Speed | 115200 |

The partition scheme is the important one, VERY IMPORTANT! With the default "Huge APP" there's no room for OTA updates.

### 2. Create your 'secrets.h' (optional)

Copy 'secrets.example.h' to 'secrets.h' (same folder as 'test_cam.ino') and fill in what you need. Everything is optional: without 'secrets.h' the camera works fine, it just won't have OTA, Telegram or Discord. An empty field means "feature off".


### 3. First upload (with a cable)

The very first time you need a cable, after that you can use OTA.

- **Easiest:** flash the ESP32-CAM *before* putting it on the PCB, using its dedicated programmer board (the ESP32-CAM-MB).
- **On the PCB:** use the programming header 'J1' with a USB-serial adapter (3.3V logic). Connect 'GND', 'TX' and 'RX' (TX to the adapter's RX and the other way around), put a jumper between 'IO0' and 'GND', and turn the camera on with the power button. Upload the sketch, then remove the jumper and power-cycle the camera. **Don't connect the adapter's 5V pin**, the camera is powered by the battery.

### 4. Updating over WiFi (OTA)

Set an 'OTA_PASSWORD' in 'secrets.h'. Without a password OTA stays off on purpose: the default WiFi password is public, so otherwise anyone nearby could flash your camera!

Then you have two ways:

- **Arduino IDE:** connect your computer to the 'ESP32-CAM' network, pick the network port 'esp32-cam' and upload as usual. It will ask for your OTA password.
- **Web page (works from a phone too):** export the binary ('Sketch > Export Compiled Binary'), open **http://192.168.4.1/update**, log in with the user 'admin' and your OTA password, pick the '.bin' and press the button.

> [!CAUTION]
> Don't update with a nearly empty battery, and don't flash a firmware that breaks the WiFi: you'd lose OTA and you'd need the cable again. That's why the 'J1' programming header exists!

## Telegram and Discord

The camera has to join a WiFi network with internet (your home router or your phone's hotspot) to send the photos. Put its name and password in 'secrets.h' ('STA_SSID' and 'STA_PASSWORD'). The camera's own network stays on, so you can still browse your photos.

### Telegram

1. On Telegram, talk to **@BotFather**, send '/newbot' and follow the steps. It will give you a **token**.
2. Open a chat with your new bot and send it any message (like '/start').
3. Open 'https://api.telegram.org/bot<YOUR_TOKEN>/getUpdates' in a browser and look for '"chat":{"id":...}'. That number is your **chat id**.
4. Put the token and the chat id in 'secrets.h' ('TELEGRAM_BOT_TOKEN' and 'TELEGRAM_CHAT_ID').

Telegram recompresses normal photos. If you want the original file, set 'TELEGRAM_AS_DOCUMENT' to 'true' in the firmware.

### Discord

1. In your Discord server go to the channel settings, then **Integrations**, then **Webhooks** and create a new one.
2. Copy the **webhook URL** and put it in 'secrets.h' ('DISCORD_WEBHOOK_URL').

### How it behaves

Every photo is uploaded in the background, so you can keep shooting while the previous one is being sent. You'll hear a high beep when it's done or a low one if it failed. If the camera can't find your WiFi (say you're out and about) it tries for about 15 seconds, then gives up. Your photo is always saved on the SD card first.

## Known limitations

- **No security checks on the cloud connection:** the camera encrypts the connection to Telegram and Discord but doesn't verify the server's certificate. For a hobby camera that's fine, but don't use it for anything sensitive.
- **Photos taken offline aren't sent later:** they stay on the SD card and you get them from the carousel.
- **The web server is small:** it serves one thing at a time, so sometimes a page takes a few seconds, and the shutter button doesn't react while a photo is being sent to your phone.
- **WiFi range:** the antenna is a PCB trace and it doesn't like metal or copper nearby (the battery included). Range is still to be measured on the real hardware.
- **Photos on the carousel:** only '.jpg' files with simple names (letters, numbers, '_', '-', '.' and spaces) show up.
- **Charging current:** see the safety note above.

## Ideas for later

- Video recording (AVI / Motion JPEG).
- Thumbnails in the carousel, to make browsing faster.
- Limiting the battery charging current properly.
- Sending the photos that were taken offline as soon as the WiFi is back.

Got another idea? Open an issue!

## Troubleshooting

Open the Arduino IDE **Serial Monitor at 115200 baud** and look at what the camera says.

- 'Camera init failed': check that the camera ribbon cable is fully inserted, and that the power is good (5V).
- 'SD mount failed': the card has to be FAT32 and well seated. Try another card.
- Upload doesn't work: after taking a photo, look for 'Server response:' in the Serial Monitor. A '200' (Telegram) or '204' (Discord) means it worked. Anything else comes with a short error from the server, like "chat not found" if the chat id is wrong.
- The camera reboots when you unplug USB-C: add the bulk capacitor (see "By hand").

## Contributing

Pull requests, issues and weird ideas are all welcome! This is an open project, so if you build one, make it better, or break it in an interesting way, tell me about it.

## Author

Made by [@Lupix111](https://github.com/Lupix111).

## Thanks to

- **Aran and the people at [PCBWay](https://www.pcbway.com)** for sponsoring the PCB.
- The write-up about the [CKCS PB0A module on done.land](https://done.land/components/power/powersupplies/battery/chargers/charge-discharge/ip5306/pb0a/), which explained everything the module's seller forgot to.

Now go and take some photos.
