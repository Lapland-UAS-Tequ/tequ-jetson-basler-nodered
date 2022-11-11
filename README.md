This repository is developed in Fish-IoT project

https://www.tequ.fi/en/project-bank/fish-iot/ 

---

# tequ-jetson-basler-nodered

This repository is developed with fresh Jetpack 5.0.2 and Jetson AGX Orin Developer Kit. Other Jetson boards might work, but are not tested.

Please first configure your Jetson using following repositiories:

Basic configuration for Jetson:
https://github.com/Lapland-UAS-Tequ/tequ-jetson-setup

Triton Inference Server configuration:
https://github.com/Lapland-UAS-Tequ/tequ-setup-triton-inference-server

Basler Camera configuration:
https://github.com/Lapland-UAS-Tequ/tequ-jetson-basler


# Install Node-RED nodes
```
cd ~/.node-red
```

```
npm install node-red-contrib-image-info &&
npm install node-red-contrib-image-output &&
npm install node-red-contrib-moment &&
npm install node-red-contrib-multipart-stream-encoder &&
npm install node-red-contrib-pipe2jpeg &&
npm install node-red-node-daemon && 
npm install node-red-node-smooth && 
npm install node-red-node-exif &&
npm install canvas &&
npm install numjs &&
npm install sharp &&
npm install piscina &&
npm install uuid 
```


