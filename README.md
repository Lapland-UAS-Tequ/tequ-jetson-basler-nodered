This repository is developed in Fish-IoT project

https://www.tequ.fi/en/project-bank/fish-iot/ 

---

# tequ-jetson-basler-nodered

This repository is developed with fresh Jetpack 5.0.2 and Jetson AGX Orin Developer Kit. Other Jetson boards might work, but are not tested. Example flow should work with any Basler camera and can easily be modified to work with multiple cameras.


Please first configure your Jetson setup using following repositiories:

Basic configuration for Jetson:
https://github.com/Lapland-UAS-Tequ/tequ-jetson-setup

Triton Inference Server configuration:
https://github.com/Lapland-UAS-Tequ/tequ-setup-triton-inference-server

Basler Camera configuration:
https://github.com/Lapland-UAS-Tequ/tequ-jetson-basler

# Functionality
- Starts local RTSP-server to serve encoded camera stream 
- Starts GStreamer that connects to Basler camera using its serial number and predefined configuration file
- GStreamer reads camera video stream and publishes encoded H264/H265 video stream to RTSP server and JPEG stream to local TCP port
- Object detection to video stream using Triton Inference Server
- Annotates detected objects to video stream

Video streams will be available in following addresses:

rtsp://JETSON_IP:8554/CAMERA_SERIAL_NUMBER

http://JETSON_IP:1880/camera/id/CAMERA_SERIAL_NUMBER/stream/1

http://JETSON_IP:1880/camera/id/CAMERA_SERIAL_NUMBER/annotated/1



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
npm install node-red-contrib-msg-speed &&
npm install canvas &&
npm install numjs &&
npm install sharp &&
npm install piscina &&
npm install uuid 
```


