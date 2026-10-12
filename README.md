# FairCharm

<img width="972" height="651" alt="FairCharm" src="https://github.com/user-attachments/assets/00c2e63d-2ba9-4805-85c5-c2b35edb9aee" />

A pocket camera that fits in your palm and can hang from your bag like a charm! It's fair because it's open-source and free for everyone.

**The people at PCBWay decided to help me out with the printed ciruitboard, shoutout to Aran and the fine people that work at PCBWay, they're providing me with a tight and clean PCB that will make this project free of any tangled and messed up wirenest. Go to PCBWay to get the PCB for the best price ever!**

Just an ESP32-CAM project that came to my mind when I saw a certain company make these poket cameras with a 1.6 megapixel camera that are kinda trash and way too stupidly expensive. 
For this project you're gonna need a grand total of 4 pieces of hardware (not counting the wires):
- 1xESP32-CAM.
- Charging module with a boost converter to 5V (initially planned to use a TP4056 but quickly found out that the ESP32-CAM needs 5v to function properly)
  [I now am the prowd owner of 10 useless TP4056 modules]
- 1x500mAh LiPo battery.
- 1x Big red (or any colour really) button, the 12x12mm clicky ones.
- 1x Passive Buzzer (new feature of the v1.4 Body!)
- 1x FairCharm PCB (optional but highly suggested, it's much easier to use the PCB than doing it by hand) 

You'll also need some 3D printed parts so you're either gonna have to buy them OR ask a friend that got one.
 
I'll post the .ino file soon, as of right now there's the template to customize the back and the front of the camera, you just need GIMP and some sort of idea on what to put there, after that it's only scissors, glue and...voilà! And there's the STL files for the body of the camera.
