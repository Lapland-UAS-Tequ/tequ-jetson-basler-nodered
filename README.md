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

# Node-RED flow functionality
- Starts local RTSP-server to serve encoded camera stream 
- Starts GStreamer that connects to Basler camera using its serial number and predefined configuration file
- GStreamer reads camera video stream and publishes encoded H264/H265 video stream to RTSP server and JPEG stream to local TCP port
- Object detection to video stream using Triton Inference Server and "ssd-example-model" that detects common things
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

# Install Node-RED flow

```[
    {
        "id": "04508fd4d91dd935",
        "type": "tab",
        "label": "RTSP",
        "disabled": false,
        "info": "",
        "env": []
    },
    {
        "id": "332da02c8e396138",
        "type": "tab",
        "label": "GStreamer",
        "disabled": false,
        "info": "",
        "env": []
    },
    {
        "id": "0d2c119e1f2d1adf",
        "type": "tab",
        "label": "MJPEG",
        "disabled": false,
        "info": "",
        "env": []
    },
    {
        "id": "88f8f06219122b1d",
        "type": "tab",
        "label": "Inference",
        "disabled": false,
        "info": "",
        "env": []
    },
    {
        "id": "83a7a965.1808a8",
        "type": "subflow",
        "name": "[IMG] Annotate",
        "info": "",
        "category": "Tequ-API Client",
        "in": [
            {
                "x": 120,
                "y": 140,
                "wires": [
                    {
                        "id": "d05bfd8e.a02e"
                    }
                ]
            }
        ],
        "out": [
            {
                "x": 1080,
                "y": 140,
                "wires": [
                    {
                        "id": "4e5f5c6c.bcf214",
                        "port": 0
                    }
                ]
            }
        ],
        "env": [
            {
                "name": "box_colors",
                "type": "json",
                "value": "{\"fish\":\"#FFFFFF\",\"pike\":\"#006400\",\"perch\":\"#008000\",\"smolt\":\"#ADD8E6\",\"salmon\":\"#0000FF\",\"trout\":\"#0000FF\",\"cyprinidae\":\"#808080\",\"zander\":\"#009000\",\"bream\":\"#008800\"}",
                "ui": {
                    "type": "input",
                    "opts": {
                        "types": [
                            "json"
                        ]
                    }
                }
            },
            {
                "name": "image_settings",
                "type": "json",
                "value": "{\"quality\":0.8}",
                "ui": {
                    "type": "input",
                    "opts": {
                        "types": [
                            "json"
                        ]
                    }
                }
            },
            {
                "name": "image_type",
                "type": "str",
                "value": "image/jpeg",
                "ui": {
                    "type": "select",
                    "opts": {
                        "opts": [
                            {
                                "l": {
                                    "en-US": "JPG"
                                },
                                "v": "image/jpeg"
                            },
                            {
                                "l": {
                                    "en-US": "PNG"
                                },
                                "v": "image/png"
                            }
                        ]
                    }
                }
            },
            {
                "name": "bbox_lineWidth",
                "type": "num",
                "value": "5",
                "ui": {
                    "type": "spinner",
                    "opts": {
                        "min": 0,
                        "max": 10
                    }
                }
            },
            {
                "name": "bbox_text_color",
                "type": "str",
                "value": "white",
                "ui": {
                    "type": "select",
                    "opts": {
                        "opts": [
                            {
                                "l": {
                                    "en-US": "white"
                                },
                                "v": "white"
                            },
                            {
                                "l": {
                                    "en-US": "black"
                                },
                                "v": "black"
                            },
                            {
                                "l": {
                                    "en-US": "blue"
                                },
                                "v": "blue"
                            },
                            {
                                "l": {
                                    "en-US": "green"
                                },
                                "v": "green"
                            },
                            {
                                "l": {
                                    "en-US": "yellow"
                                },
                                "v": "yellow"
                            },
                            {
                                "l": {
                                    "en-US": "red"
                                },
                                "v": "red"
                            },
                            {
                                "l": {
                                    "en-US": "orange"
                                },
                                "v": "orange"
                            }
                        ]
                    }
                }
            },
            {
                "name": "bbox_font",
                "type": "str",
                "value": "30px Arial",
                "ui": {
                    "type": "select",
                    "opts": {
                        "opts": [
                            {
                                "l": {
                                    "en-US": "5px Arial"
                                },
                                "v": "5 px Arial"
                            },
                            {
                                "l": {
                                    "en-US": "10px Arial"
                                },
                                "v": "10px Arial"
                            },
                            {
                                "l": {
                                    "en-US": "15px Arial"
                                },
                                "v": "15px Arial"
                            },
                            {
                                "l": {
                                    "en-US": "20px Arial"
                                },
                                "v": "20px Arial"
                            },
                            {
                                "l": {
                                    "en-US": "25px Arial"
                                },
                                "v": "25px Arial"
                            },
                            {
                                "l": {
                                    "en-US": "30px Arial"
                                },
                                "v": "30px Arial"
                            },
                            {
                                "l": {
                                    "en-US": "35px Arial"
                                },
                                "v": "35px Arial"
                            },
                            {
                                "l": {
                                    "en-US": "40px Arial"
                                },
                                "v": "40px Arial"
                            },
                            {
                                "l": {
                                    "en-US": "45px Arial"
                                },
                                "v": "45px Arial"
                            },
                            {
                                "l": {
                                    "en-US": "50px Arial"
                                },
                                "v": "50px Arial"
                            }
                        ]
                    }
                }
            },
            {
                "name": "label_offset_x",
                "type": "num",
                "value": "0",
                "ui": {
                    "type": "input",
                    "opts": {
                        "types": [
                            "num"
                        ]
                    }
                }
            },
            {
                "name": "label_offset_y",
                "type": "num",
                "value": "30",
                "ui": {
                    "type": "input",
                    "opts": {
                        "types": [
                            "num"
                        ]
                    }
                }
            },
            {
                "name": "threshold",
                "type": "num",
                "value": "0.75",
                "ui": {
                    "type": "spinner",
                    "opts": {
                        "min": 0,
                        "max": 1
                    }
                }
            }
        ],
        "meta": {
            "module": "[IMG] Annotate",
            "version": "0.0.1",
            "author": "juha.autioniemi@lapinamk.fi",
            "desc": "Annotates prediction results from [AI] Detect subflows.",
            "license": "MIT"
        },
        "color": "#87A980",
        "icon": "font-awesome/fa-pencil-square-o",
        "status": {
            "x": 1080,
            "y": 340,
            "wires": [
                {
                    "id": "7fd4f6bf24348b12",
                    "port": 0
                }
            ]
        }
    },
    {
        "id": "fa7f491898fed374",
        "type": "subflow",
        "name": "gst-jetson",
        "info": "",
        "category": "Tequ-API Client",
        "in": [
            {
                "x": 120,
                "y": 200,
                "wires": [
                    {
                        "id": "54dc40fe44895f9a"
                    }
                ]
            }
        ],
        "out": [
            {
                "x": 1100,
                "y": 160,
                "wires": [
                    {
                        "id": "ab1595a359c8a71b",
                        "port": 0
                    },
                    {
                        "id": "dd701ced077fb628",
                        "port": 0
                    },
                    {
                        "id": "361321e30bc9bba9",
                        "port": 0
                    },
                    {
                        "id": "d86014e70dadc4b2",
                        "port": 0
                    }
                ]
            }
        ],
        "env": [
            {
                "name": "serial",
                "type": "num",
                "value": "40257292",
                "ui": {
                    "type": "input",
                    "opts": {
                        "types": [
                            "num"
                        ]
                    }
                }
            },
            {
                "name": "model",
                "type": "str",
                "value": "daA3840-45uc",
                "ui": {
                    "type": "input",
                    "opts": {
                        "types": [
                            "str"
                        ]
                    }
                }
            },
            {
                "name": "pfs_file",
                "type": "str",
                "value": "/home/tequ/40257292.pfs",
                "ui": {
                    "type": "input",
                    "opts": {
                        "types": [
                            "str"
                        ]
                    }
                }
            },
            {
                "name": "mjpeg_width",
                "type": "num",
                "value": "1920",
                "ui": {
                    "type": "input",
                    "opts": {
                        "types": [
                            "num"
                        ]
                    }
                }
            },
            {
                "name": "mjpeg_height",
                "type": "num",
                "value": "1080",
                "ui": {
                    "type": "input",
                    "opts": {
                        "types": [
                            "num"
                        ]
                    }
                }
            },
            {
                "name": "mjpeg_framerate",
                "type": "num",
                "value": "5",
                "ui": {
                    "type": "input",
                    "opts": {
                        "types": [
                            "num"
                        ]
                    }
                }
            },
            {
                "name": "mjpeg_font_size",
                "type": "num",
                "value": "8",
                "ui": {
                    "type": "input",
                    "opts": {
                        "types": [
                            "num"
                        ]
                    }
                }
            },
            {
                "name": "rtsp_width",
                "type": "num",
                "value": "3840",
                "ui": {
                    "type": "input",
                    "opts": {
                        "types": [
                            "num"
                        ]
                    }
                }
            },
            {
                "name": "rtsp_height",
                "type": "num",
                "value": "2160",
                "ui": {
                    "type": "input",
                    "opts": {
                        "types": [
                            "num"
                        ]
                    }
                }
            },
            {
                "name": "rtsp_font_size",
                "type": "str",
                "value": "4",
                "ui": {
                    "type": "input",
                    "opts": {
                        "types": [
                            "str"
                        ]
                    }
                }
            },
            {
                "name": "rtsp_framerate",
                "type": "num",
                "value": "10",
                "ui": {
                    "type": "input",
                    "opts": {
                        "types": [
                            "num"
                        ]
                    }
                }
            },
            {
                "name": "rtsp_encoder",
                "type": "str",
                "value": "nvv4l2h265enc",
                "ui": {
                    "type": "select",
                    "opts": {
                        "opts": [
                            {
                                "l": {
                                    "en-US": "nvv4l2h265enc"
                                },
                                "v": "nvv4l2h265enc"
                            },
                            {
                                "l": {
                                    "en-US": "nvv4l2h264enc"
                                },
                                "v": "nvv4l2h264enc"
                            }
                        ]
                    }
                }
            },
            {
                "name": "video_type",
                "type": "str",
                "value": "video/x-raw",
                "ui": {
                    "type": "input",
                    "opts": {
                        "types": [
                            "str"
                        ]
                    }
                }
            },
            {
                "name": "video_format",
                "type": "str",
                "value": "YUY2",
                "ui": {
                    "type": "input",
                    "opts": {
                        "types": [
                            "str"
                        ]
                    }
                }
            },
            {
                "name": "tcp_port",
                "type": "num",
                "value": "50001",
                "ui": {
                    "type": "input",
                    "opts": {
                        "types": [
                            "num"
                        ]
                    }
                }
            },
            {
                "name": "rtsp_server",
                "type": "str",
                "value": "rtsp://localhost:8554/",
                "ui": {
                    "type": "input",
                    "opts": {
                        "types": [
                            "str"
                        ]
                    }
                }
            }
        ],
        "meta": {
            "module": "tequ-basler-gst-jetson",
            "version": "0.0.1",
            "author": "juha.autioniemi@lapinamk.fi",
            "desc": "Launch and control GStreamer pipelines.",
            "license": "MIT"
        },
        "color": "#3FADB5",
        "inputLabels": [
            "command in"
        ],
        "outputLabels": [
            "info out"
        ],
        "icon": "node-red/bridge.svg",
        "status": {
            "x": 1060,
            "y": 360,
            "wires": [
                {
                    "id": "6abd0b160a7877dd",
                    "port": 0
                }
            ]
        }
    },
    {
        "id": "21dbb5f62f40368e",
        "type": "subflow",
        "name": "Parse JPEG",
        "info": "Parses JPEG image from MJPEG stream and adds metadata to msg.\r\n\r\nReads or adds:\r\n - Basic image info (width,height,size) \r\n - Exif data\r\n - Local timestamp, UTC timestamp, Unix timestamp \r\n - Thumbnail of the original image\r\n - Coordinates (if given)\r\n\r\n Output message is formatted to Tequ-API compatible format.\r\n\r\n",
        "category": "Tequ-API Client",
        "in": [
            {
                "x": 60,
                "y": 60,
                "wires": [
                    {
                        "id": "4026f137d0f6ce24"
                    }
                ]
            }
        ],
        "out": [
            {
                "x": 820,
                "y": 320,
                "wires": [
                    {
                        "id": "cfd1798c21f67f4b",
                        "port": 0
                    }
                ]
            }
        ],
        "env": [
            {
                "name": "latitude",
                "type": "num",
                "value": "66.503059",
                "ui": {
                    "type": "input",
                    "opts": {
                        "types": [
                            "num",
                            "env"
                        ]
                    }
                }
            },
            {
                "name": "longitude",
                "type": "num",
                "value": "25.726967",
                "ui": {
                    "type": "input",
                    "opts": {
                        "types": [
                            "num",
                            "env"
                        ]
                    }
                }
            }
        ],
        "meta": {
            "module": "tequ-parse-mjpeg",
            "version": "0.0.1",
            "author": "juha.autioniemi@lapinamk.fi",
            "desc": "Parse JPEG from stream and add metadata to image",
            "license": "MIT"
        },
        "color": "#3FADB5",
        "inputLabels": [
            "MJPEG stream in"
        ],
        "outputLabels": [
            "image out"
        ],
        "icon": "font-awesome/fa-image",
        "status": {
            "x": 780,
            "y": 400,
            "wires": [
                {
                    "id": "64555399f8f3f8de",
                    "port": 0
                }
            ]
        }
    },
    {
        "id": "d9dd49bd747346d4",
        "type": "subflow",
        "name": "gst WD",
        "info": "",
        "category": "Tequ-API Client",
        "in": [
            {
                "x": 80,
                "y": 60,
                "wires": [
                    {
                        "id": "4676b6d77fb4c9fd"
                    }
                ]
            }
        ],
        "out": [
            {
                "x": 720,
                "y": 180,
                "wires": [
                    {
                        "id": "67250f6441a6d8f2",
                        "port": 0
                    }
                ]
            }
        ],
        "env": [],
        "meta": {},
        "color": "#3FADB5",
        "inputLabels": [
            "Stream data in"
        ],
        "outputLabels": [
            "Restart cmd out"
        ],
        "icon": "font-awesome/fa-search",
        "status": {
            "x": 660,
            "y": 240,
            "wires": [
                {
                    "id": "42e72deef2a5b5b1",
                    "port": 0
                }
            ]
        }
    },
    {
        "id": "fd51dfdc3367d25c",
        "type": "subflow",
        "name": "Triton req",
        "info": "Sends request to NVIDIA Triton Inference Server.\r\n\r\nExpects \"msg.tensor\" to be present in input\r\n\r\n\r\n \"msg.tensor.data\" and \r\n\r\n\r\n\r\n",
        "category": "Tequ-API Client",
        "in": [
            {
                "x": 100,
                "y": 100,
                "wires": [
                    {
                        "id": "b63e9a83f3001f21"
                    }
                ]
            }
        ],
        "out": [
            {
                "x": 700,
                "y": 100,
                "wires": [
                    {
                        "id": "7451b5b3b174fc54",
                        "port": 0
                    }
                ]
            }
        ],
        "env": [
            {
                "name": "server_url",
                "type": "str",
                "value": "localhost:8000",
                "ui": {
                    "type": "input",
                    "opts": {
                        "types": [
                            "str",
                            "env"
                        ]
                    }
                }
            },
            {
                "name": "model_name",
                "type": "str",
                "value": "tequ-fish-species-detection",
                "ui": {
                    "type": "input",
                    "opts": {
                        "types": [
                            "str",
                            "env"
                        ]
                    }
                }
            },
            {
                "name": "request_timeout",
                "type": "num",
                "value": "10000",
                "ui": {
                    "type": "input",
                    "opts": {
                        "types": [
                            "num",
                            "env"
                        ]
                    }
                }
            }
        ],
        "meta": {
            "module": "tequ-triton-request",
            "version": "0.0.1",
            "author": "juha.autioniemi@lapinamk.fi",
            "desc": "Send request to Triton Inference server.",
            "license": "MIT"
        },
        "color": "#3FADB5",
        "icon": "font-awesome/fa-globe",
        "status": {
            "x": 700,
            "y": 180,
            "wires": [
                {
                    "id": "5368623e2deefd7e",
                    "port": 0
                }
            ]
        }
    },
    {
        "id": "fde3bd1da88bc03c",
        "type": "subflow",
        "name": "Image to Tensor",
        "info": "Converts input image (msg.payload) to Tensor.\r\n\r\nOutputs results in \"msg.tensor\".",
        "category": "Tequ-API Client",
        "in": [
            {
                "x": 60,
                "y": 60,
                "wires": [
                    {
                        "id": "557647c8b195bdea"
                    }
                ]
            }
        ],
        "out": [
            {
                "x": 380,
                "y": 60,
                "wires": [
                    {
                        "id": "557647c8b195bdea",
                        "port": 0
                    }
                ]
            }
        ],
        "env": [],
        "meta": {
            "module": "tequ-image-to-tensor",
            "version": "0.0.1",
            "author": "juha.autioniemi@lapinamk.fi",
            "desc": "Converts image to tensor.",
            "license": "MIT"
        },
        "color": "#3FADB5",
        "icon": "node-red/swap.svg",
        "status": {
            "x": 380,
            "y": 140,
            "wires": [
                {
                    "id": "752a76d98d97af39",
                    "port": 0
                }
            ]
        }
    },
    {
        "id": "9215deec580c5391",
        "type": "subflow",
        "name": "Post-process",
        "info": "Post process result from Triton request.\r\n",
        "category": "Tequ-API Client",
        "in": [
            {
                "x": 100,
                "y": 120,
                "wires": [
                    {
                        "id": "1e80d9526ad0c4ee"
                    }
                ]
            }
        ],
        "out": [
            {
                "x": 970,
                "y": 120,
                "wires": [
                    {
                        "id": "1e80d9526ad0c4ee",
                        "port": 0
                    }
                ]
            }
        ],
        "env": [
            {
                "name": "model_name",
                "type": "str",
                "value": "ssd-example-model",
                "ui": {
                    "type": "input",
                    "opts": {
                        "types": [
                            "str",
                            "env"
                        ]
                    }
                }
            },
            {
                "name": "server_url",
                "type": "str",
                "value": "localhost:8000",
                "ui": {
                    "type": "input",
                    "opts": {
                        "types": [
                            "str",
                            "env"
                        ]
                    }
                }
            },
            {
                "name": "threshold",
                "type": "num",
                "value": "0.5",
                "ui": {
                    "type": "input",
                    "opts": {
                        "types": [
                            "num",
                            "env"
                        ]
                    }
                }
            }
        ],
        "meta": {
            "module": "tequ-triton-post-process",
            "version": "0.0.1",
            "author": "juha.autioniemi@lapinamk.fi",
            "desc": "Post-process Triton request.",
            "license": "MIT"
        },
        "color": "#3FADB5",
        "inputLabels": [
            "Triton request result in"
        ],
        "outputLabels": [
            "Processed output"
        ],
        "icon": "font-awesome/fa-compress",
        "status": {
            "x": 900,
            "y": 300,
            "wires": [
                {
                    "id": "b93022c83cb00303",
                    "port": 0
                }
            ]
        }
    },
    {
        "id": "c19ac6bd.2a9d08",
        "type": "function",
        "z": "83a7a965.1808a8",
        "name": "Annotate with  canvas",
        "func": "const img = msg.payload;\nconst objects = msg.data.properties.computer_vision.result\nconst labels = msg.data.properties.computer_vision.labels\n\nconst image_type = env.get(\"image_type\");\nconst image_settings = env.get(\"image_settings\");\nconst bbox_lineWidth = env.get(\"bbox_lineWidth\");\nconst bbox_text_color = env.get(\"bbox_text_color\");\nconst label_offset_x = env.get(\"label_offset_x\");\nconst label_offset_y = env.get(\"label_offset_y\");\nconst bbox_font = env.get(\"bbox_font\");\nconst COLORS = env.get(\"box_colors\");\n\n\n//Define threshold\nlet threshold = 0;\n\nconst global_settings = global.get(\"settings\") || undefined\nlet thresholdType = \"\"\n\nif(global_settings !== undefined){\n    if(\"threshold\" in global_settings){\n        threshold = global_settings[\"threshold\"]\n        thresholdType = \"global\";\n    }\n}\n\nelse if(\"threshold\" in msg){\n    threshold = msg.threshold;\n    thresholdType = \"msg\";\n    if(threshold < 0){\n        threshold = 0\n    }\n    else if(threshold > 1){\n        threshold = 1\n    }\n}\n\nelse{\n    threshold = env.get(\"threshold\");\n    thresholdType = \"env\";\n}\n\nmsg.thresholdUsed = threshold;\nmsg.thresholdTypeUsed = thresholdType;\n\nasync function annotateImage(image) {\n  const localImage = await canvas.loadImage(image);  \n  const cvs = canvas.createCanvas(localImage.width, localImage.height);\n  const ctx = cvs.getContext('2d');  \n  ctx.drawImage(localImage, 0, 0); \n  \n  objects.forEach((obj) => {\n        if(labels.includes(obj.class) && obj.score >= threshold){\n            let [x, y, w, h] = obj.bbox;\n            ctx.lineWidth = bbox_lineWidth;\n            ctx.strokeStyle = COLORS[obj.class];\n            ctx.strokeRect(x, y, w, h);\n            ctx.fillStyle = bbox_text_color;\n            ctx.font = bbox_font;\n            ctx.fillText(obj.class+\" \"+Math.round(obj.score*100)+\" %\",x+label_offset_x,y+label_offset_y);\n        }\n      });\n  \n  return cvs.toBuffer(image_type, image_settings);\n}\n\nif(objects.length > 0){\n    msg.data.properties.object.data.annotated.image = await annotateImage(img)   \n    //node.done()\n    msg.objects_found = true\n}\nelse{\n    msg.objects_found = false\n}\n\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [
            {
                "var": "canvas",
                "module": "canvas"
            }
        ],
        "x": 440,
        "y": 140,
        "wires": [
            [
                "a801355d.9f7ac8"
            ]
        ]
    },
    {
        "id": "d05bfd8e.a02e",
        "type": "change",
        "z": "83a7a965.1808a8",
        "name": "timer",
        "rules": [
            {
                "t": "set",
                "p": "start",
                "pt": "msg",
                "to": "",
                "tot": "date"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 230,
        "y": 140,
        "wires": [
            [
                "c19ac6bd.2a9d08"
            ]
        ]
    },
    {
        "id": "a801355d.9f7ac8",
        "type": "change",
        "z": "83a7a965.1808a8",
        "name": "end timer",
        "rules": [
            {
                "t": "set",
                "p": "data.properties.object.data.annotated.annotation_ms",
                "pt": "msg",
                "to": "$millis() - msg.start",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "payload.annotation.objects_found",
                "pt": "msg",
                "to": "objects_found",
                "tot": "msg"
            },
            {
                "t": "delete",
                "p": "annotated_image",
                "pt": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 660,
        "y": 140,
        "wires": [
            [
                "c20a6448.e6f218"
            ]
        ]
    },
    {
        "id": "4e5f5c6c.bcf214",
        "type": "change",
        "z": "83a7a965.1808a8",
        "name": "delete useless",
        "rules": [
            {
                "t": "delete",
                "p": "annotated_image",
                "pt": "msg"
            },
            {
                "t": "delete",
                "p": "start",
                "pt": "msg"
            },
            {
                "t": "delete",
                "p": "objects_found",
                "pt": "msg"
            },
            {
                "t": "delete",
                "p": "resize_start",
                "pt": "msg"
            },
            {
                "t": "delete",
                "p": "thresholdTypeUsed",
                "pt": "msg"
            },
            {
                "t": "delete",
                "p": "threshold",
                "pt": "msg"
            },
            {
                "t": "delete",
                "p": "thresholdUsed",
                "pt": "msg"
            },
            {
                "t": "delete",
                "p": "start",
                "pt": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 880,
        "y": 140,
        "wires": [
            []
        ]
    },
    {
        "id": "c20a6448.e6f218",
        "type": "switch",
        "z": "83a7a965.1808a8",
        "name": "objects found?",
        "property": "objects_found",
        "propertyType": "msg",
        "rules": [
            {
                "t": "true"
            },
            {
                "t": "false"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 2,
        "x": 220,
        "y": 260,
        "wires": [
            [
                "5fae1e95eb3561e9"
            ],
            [
                "0ec56ca8f000a540"
            ]
        ]
    },
    {
        "id": "a9379cd1321a02da",
        "type": "function",
        "z": "83a7a965.1808a8",
        "name": "",
        "func": "node.status({ fill: \"green\", shape: \"dot\", text: msg.thresholdTypeUsed + \" \" + msg.thresholdUsed + \" in \" + msg.data.properties.object.data.annotated.total_ms+\" ms\"})",
        "outputs": 0,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 1000,
        "y": 260,
        "wires": []
    },
    {
        "id": "0ec56ca8f000a540",
        "type": "function",
        "z": "83a7a965.1808a8",
        "name": "",
        "func": "node.status({fill:\"green\",shape:\"dot\",text:msg.thresholdTypeUsed+\" \"+msg.thresholdUsed+\" No objects to annotate\"})\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 500,
        "y": 300,
        "wires": [
            [
                "4e5f5c6c.bcf214"
            ]
        ]
    },
    {
        "id": "7fd4f6bf24348b12",
        "type": "status",
        "z": "83a7a965.1808a8",
        "name": "",
        "scope": null,
        "x": 860,
        "y": 340,
        "wires": [
            []
        ]
    },
    {
        "id": "5fae1e95eb3561e9",
        "type": "change",
        "z": "83a7a965.1808a8",
        "name": "timer",
        "rules": [
            {
                "t": "set",
                "p": "resize_start",
                "pt": "msg",
                "to": "",
                "tot": "date"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 490,
        "y": 240,
        "wires": [
            [
                "f5809fcdc54a540e"
            ]
        ]
    },
    {
        "id": "f5809fcdc54a540e",
        "type": "function",
        "z": "83a7a965.1808a8",
        "name": "resize",
        "func": "let input = msg.data.properties.object.data.annotated.image;\n\nlet resized = await sharp(input)\n  .metadata()\n  .then(({ width }) => sharp(input)\n    .resize({ width: 200 })\n    .toBuffer()\n);\n\nmsg.data.properties.object.data.annotated.thumbnail = resized.toString('base64');\nmsg.data.properties.object.data.annotated.image = (msg.data.properties.object.data.annotated.image).toString('base64')\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [
            {
                "var": "sharp",
                "module": "sharp"
            }
        ],
        "x": 630,
        "y": 240,
        "wires": [
            [
                "e0f13103c7faabff"
            ]
        ]
    },
    {
        "id": "e0f13103c7faabff",
        "type": "change",
        "z": "83a7a965.1808a8",
        "name": "end timer",
        "rules": [
            {
                "t": "set",
                "p": "data.properties.object.data.annotated.thumbnail_ms",
                "pt": "msg",
                "to": "$millis() - msg.resize_start",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "data.properties.object.data.annotated.total_ms",
                "pt": "msg",
                "to": "$millis() - msg.start",
                "tot": "jsonata"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 780,
        "y": 240,
        "wires": [
            [
                "4e5f5c6c.bcf214",
                "a9379cd1321a02da"
            ]
        ]
    },
    {
        "id": "d9a2420055236c2b",
        "type": "change",
        "z": "fa7f491898fed374",
        "name": "",
        "rules": [
            {
                "t": "set",
                "p": "kill",
                "pt": "msg",
                "to": "SIGTERM",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 490,
        "y": 120,
        "wires": [
            [
                "99ac100731d2d825"
            ]
        ]
    },
    {
        "id": "be99e21d6ee788c1",
        "type": "function",
        "z": "fa7f491898fed374",
        "name": "construct gst pipeline",
        "func": "let serial = env.get(\"serial\");\nlet model = env.get(\"model\");\nlet pfs_file = env.get(\"pfs_file\");\n\nlet rtsp_width = env.get(\"rtsp_width\");\nlet rtsp_height = env.get(\"rtsp_height\");\nlet rtsp_framerate = env.get(\"rtsp_framerate\");\nlet rtsp_encoder = env.get(\"rtsp_encoder\");\nlet rtsp_font_size = env.get(\"rtsp_font_size\");\nlet rtsp_server = env.get(\"rtsp_server\");\n\nlet mjpeg_width = env.get(\"mjpeg_width\");\nlet mjpeg_height = env.get(\"mjpeg_height\");\nlet mjpeg_framerate = env.get(\"mjpeg_framerate\");\nlet mjpeg_font_size = env.get(\"mjpeg_font_size\");\n\nlet video_format = env.get(\"video_format\");\nlet video_type = env.get(\"video_type\");\nlet tcp_port = env.get(\"tcp_port\");\n\n\n\nlet gst_msg = {}\nlet gst_pipeline = \n    'pylonsrc device-serial-number=\"' + serial + '\" pfs-location=' + pfs_file + ' ! tee name = video_out' + ' ' +\n    'video_out. ! clockoverlay time-format=\"%d-%m-%Y %H:%M:%S - ' + serial + '\" font-desc=\"Sans, ' + mjpeg_font_size + '\" ! videoscale ! \"' + video_type + ',width=' + mjpeg_width + ',height=' + mjpeg_height + ',framerate=' + mjpeg_framerate + '/1,format=' + video_format + '\" ! nvvidconv ! queue ! nvjpegenc ! taginject tags = \"application-name=tequ-gst-1.0,copyright=Lapland_UAS,description=serialnumber_' + serial + ',artist=Tequ,device-manufacturer=Basler,device-model='+model+'\" ! queue ! jifmux ! queue ! tcpclientsink port='+tcp_port+' '+\n    'video_out. ! clockoverlay time-format=\"%d-%m-%Y %H:%M:%S - ' + serial + '\" font-desc=\"Sans, ' + rtsp_font_size+'\" ! videoscale ! \"' + video_type + ',width=' + rtsp_width + ',height=' + rtsp_height + ',framerate=' + rtsp_framerate + '/1,format=' + video_format + '\" ! nvvidconv ! ' +rtsp_encoder+' ! queue ! rtspclientsink location = '+rtsp_server+serial+''\ngst_msg.payload = gst_pipeline;\n\nnode.status({fill:\"blue\",shape:\"ring\",text:\"Launching...\"});\n\nreturn gst_msg;",
        "outputs": 1,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 520,
        "y": 320,
        "wires": [
            [
                "99ac100731d2d825",
                "ab1595a359c8a71b"
            ]
        ]
    },
    {
        "id": "99ac100731d2d825",
        "type": "exec",
        "z": "fa7f491898fed374",
        "command": "gst-launch-1.0",
        "addpay": "payload",
        "append": "",
        "useSpawn": "true",
        "timer": "",
        "winHide": false,
        "oldrc": false,
        "name": "gst-launch",
        "x": 710,
        "y": 120,
        "wires": [
            [
                "361321e30bc9bba9"
            ],
            [
                "dd701ced077fb628"
            ],
            [
                "d86014e70dadc4b2"
            ]
        ]
    },
    {
        "id": "8ba82764fc44edf8",
        "type": "delay",
        "z": "fa7f491898fed374",
        "name": "",
        "pauseType": "delay",
        "timeout": "5",
        "timeoutUnits": "seconds",
        "rate": "1",
        "nbRateUnits": "1",
        "rateUnits": "second",
        "randomFirst": "1",
        "randomLast": "5",
        "randomUnits": "seconds",
        "drop": false,
        "allowrate": false,
        "outputs": 1,
        "x": 480,
        "y": 240,
        "wires": [
            [
                "be99e21d6ee788c1"
            ]
        ]
    },
    {
        "id": "f6110566b977602b",
        "type": "change",
        "z": "fa7f491898fed374",
        "name": "",
        "rules": [
            {
                "t": "set",
                "p": "kill",
                "pt": "msg",
                "to": "SIGTERM",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 490,
        "y": 180,
        "wires": [
            [
                "8ba82764fc44edf8",
                "99ac100731d2d825"
            ]
        ]
    },
    {
        "id": "6abd0b160a7877dd",
        "type": "status",
        "z": "fa7f491898fed374",
        "name": "",
        "scope": [
            "be99e21d6ee788c1",
            "99ac100731d2d825",
            "54dc40fe44895f9a"
        ],
        "x": 760,
        "y": 360,
        "wires": [
            []
        ]
    },
    {
        "id": "54dc40fe44895f9a",
        "type": "function",
        "z": "fa7f491898fed374",
        "name": "cmd?",
        "func": "let topic = msg.topic;\n\nif(topic == \"kill\"){\n    node.status({fill:\"red\",shape:\"ring\",text:\"cmd: kill\"});\n    return [msg,null,null]\n}\nelse if (topic == \"restart\") {\n    node.status({ fill: \"yellow\", shape: \"ring\", text: \"cmd: restart\" });\n    return [null, msg, null]\n}\nelse if (topic == \"start\") {\n    node.status({ fill: \"blue\", shape: \"ring\", text: \"cmd: start\" });\n    return [null, null, msg]\n}\n\nreturn msg;",
        "outputs": 3,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 230,
        "y": 200,
        "wires": [
            [
                "d9a2420055236c2b"
            ],
            [
                "f6110566b977602b"
            ],
            [
                "be99e21d6ee788c1"
            ]
        ]
    },
    {
        "id": "ab1595a359c8a71b",
        "type": "change",
        "z": "fa7f491898fed374",
        "name": "",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "pipeline-info",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 770,
        "y": 320,
        "wires": [
            []
        ]
    },
    {
        "id": "dd701ced077fb628",
        "type": "change",
        "z": "fa7f491898fed374",
        "name": "stderr",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "stderr",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 870,
        "y": 120,
        "wires": [
            []
        ]
    },
    {
        "id": "361321e30bc9bba9",
        "type": "change",
        "z": "fa7f491898fed374",
        "name": "stdout",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "stdout",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 870,
        "y": 80,
        "wires": [
            []
        ]
    },
    {
        "id": "d86014e70dadc4b2",
        "type": "change",
        "z": "fa7f491898fed374",
        "name": "return code",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "return code",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 890,
        "y": 160,
        "wires": [
            []
        ]
    },
    {
        "id": "4026f137d0f6ce24",
        "type": "pipe2jpeg",
        "z": "21dbb5f62f40368e",
        "name": "",
        "x": 180,
        "y": 60,
        "wires": [
            [
                "24296ef596b98b25"
            ]
        ]
    },
    {
        "id": "e0bb72dfae4ee3a3",
        "type": "image-info",
        "z": "21dbb5f62f40368e",
        "name": "",
        "x": 190,
        "y": 320,
        "wires": [
            [
                "e4dd6a59044e7bfa"
            ]
        ]
    },
    {
        "id": "e4dd6a59044e7bfa",
        "type": "exif",
        "z": "21dbb5f62f40368e",
        "name": "",
        "mode": "normal",
        "property": "payload",
        "x": 170,
        "y": 380,
        "wires": [
            [
                "8f854870.b60a68"
            ]
        ]
    },
    {
        "id": "f89675e462ce03bb",
        "type": "moment",
        "z": "21dbb5f62f40368e",
        "name": "",
        "topic": "",
        "input": "",
        "inputType": "date",
        "inTz": "ETC/UTC",
        "adjAmount": 0,
        "adjType": "days",
        "adjDir": "add",
        "format": "",
        "locale": "en-US",
        "output": "utc_timestamp",
        "outputType": "msg",
        "outTz": "Europe/Helsinki",
        "x": 220,
        "y": 140,
        "wires": [
            [
                "0aa0578efb06e6b3"
            ]
        ]
    },
    {
        "id": "0aa0578efb06e6b3",
        "type": "moment",
        "z": "21dbb5f62f40368e",
        "name": "",
        "topic": "",
        "input": "utc_timestamp",
        "inputType": "msg",
        "inTz": "ETC/UTC",
        "adjAmount": 0,
        "adjType": "days",
        "adjDir": "add",
        "format": "YYYY-MM-DD HH:mm:ss",
        "locale": "en-US",
        "output": "local_timestamp",
        "outputType": "msg",
        "outTz": "Europe/Helsinki",
        "x": 220,
        "y": 200,
        "wires": [
            [
                "684d7d4f12541303"
            ]
        ]
    },
    {
        "id": "684d7d4f12541303",
        "type": "moment",
        "z": "21dbb5f62f40368e",
        "name": "",
        "topic": "",
        "input": "utc_timestamp",
        "inputType": "msg",
        "inTz": "ETC/UTC",
        "adjAmount": 0,
        "adjType": "days",
        "adjDir": "add",
        "format": "x",
        "locale": "en-US",
        "output": "unix_timestamp",
        "outputType": "msg",
        "outTz": "ETC/UTC",
        "x": 220,
        "y": 260,
        "wires": [
            [
                "e0bb72dfae4ee3a3"
            ]
        ]
    },
    {
        "id": "64555399f8f3f8de",
        "type": "status",
        "z": "21dbb5f62f40368e",
        "name": "",
        "scope": [
            "abcd5255fa959167",
            "4676b6d77fb4c9fd"
        ],
        "x": 600,
        "y": 400,
        "wires": [
            []
        ]
    },
    {
        "id": "13ad0fba970dab22",
        "type": "function",
        "z": "21dbb5f62f40368e",
        "name": "Reformat data",
        "func": "let type;\nlet serial = (msg.exif.image.ImageDescription).split(\"_\")[1];\nlet random_name = uuid.v4();\nlet image_type = msg.type;\nlet file_extension;\nlet latitude = Number(env.get(\"latitude\"))\nlet longitude = Number(env.get(\"longitude\"))\nlet datasource = serial;\nlet unix_timestamp = parseInt(msg.unix_timestamp);\n\nif (image_type == \"jpg\") {\n    image_type = \"image/jpeg\"\n    file_extension = \".jpg\"\n}\nelse if (image_type == \"png\") {\n    image_type = \"image/png\"\n    file_extension = \".png\"\n}\n\nmsg.topic = serial;\nmsg.datasource = serial;\nmsg.random_name = random_name;\nmsg.file_extension = file_extension;\n\nlet parameters = {\n    \"owner\": msg.exif.image.Copyright,\n    \"version\": msg.exif.image.Software,\n    \"model\": msg.exif.image.Model,\n    \"serial\": serial,\n    \"manufacturer\": msg.exif.image.Make\n}\n\nmsg.data = {\n    \"type\": \"Feature\",\n    \"geometry\": {\n        \"type\": \"Point\",\n        \"coordinates\": [longitude, latitude]\n    },\n    \"properties\": {\n        \"datasource\": datasource,\n        \"local_timestamp\": msg.local_timestamp,\n        \"utc_timestamp\": msg.utc_timestamp,\n        \"unix_timestamp\": unix_timestamp,\n        \"cos\": {\n            \"bucket\": \"\",\n            \"region\": \"\",\n            \"service_id\": \"\"\n        },\n        \"object\": {\n            \"type\": image_type,\n            \"data\": {\n                \"width\": msg.width,\n                \"height\": msg.height,\n                \"size\": (msg.payload).length,\n                \"exif\": msg.exif,\n                \"parameters\": parameters,\n                \"original\": {\n                    \"image\": (msg.payload).toString('base64'),\n                    \"objectname\": datasource + \"-\" + random_name + file_extension,\n                    \"thumbnail\": (msg.thumbnail).toString('base64'),\n                    \"thumbnail_ms\": msg.thumbnail_ms,\n                    \"mjpeg_process_ms\":0\n                },\n                \"annotated\": {\n                    \"image\": \"\",\n                    \"objectname\": datasource + \"-\" + random_name+ \"_annotated\"+file_extension,\n                    \"thumbnail\": \"\",\n                    \"thumbnail_ms\":0,\n                    \"annotation_ms\":0,\n                    \"total_ms\":0\n                }\n            }\n        },\n        \"computer_vision\": {\n            \"type\": \"\",\n            \"model\": \"\",\n            \"inference_time\": \"\",\n            \"result\": \"\"\n        }\n    }\n}\n\ndelete msg.settings;\ndelete msg.width;\ndelete msg.height;\ndelete msg.type;\ndelete msg.exif;\ndelete msg.thumbnail;\ndelete msg.utc_timestamp;\ndelete msg.local_timestamp;\ndelete msg.unix_timestamp;\ndelete msg.start;\ndelete msg.thumbnail_ms;\ndelete msg.datasource;\ndelete msg.random_name;\ndelete msg.file_extension;\n\n\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [
            {
                "var": "uuid",
                "module": "uuid"
            }
        ],
        "x": 620,
        "y": 260,
        "wires": [
            [
                "cfd1798c21f67f4b"
            ]
        ]
    },
    {
        "id": "8f854870.b60a68",
        "type": "change",
        "z": "21dbb5f62f40368e",
        "name": "timer",
        "rules": [
            {
                "t": "set",
                "p": "start",
                "pt": "msg",
                "to": "",
                "tot": "date"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 330,
        "y": 380,
        "wires": [
            [
                "7ebfd14d.6f0a2"
            ]
        ]
    },
    {
        "id": "11af73fe.677eac",
        "type": "change",
        "z": "21dbb5f62f40368e",
        "name": "end timer",
        "rules": [
            {
                "t": "set",
                "p": "thumbnail_ms",
                "pt": "msg",
                "to": "$millis() - msg.start",
                "tot": "jsonata"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 600,
        "y": 200,
        "wires": [
            [
                "13ad0fba970dab22"
            ]
        ]
    },
    {
        "id": "24296ef596b98b25",
        "type": "change",
        "z": "21dbb5f62f40368e",
        "name": "timer",
        "rules": [
            {
                "t": "set",
                "p": "process_start",
                "pt": "msg",
                "to": "",
                "tot": "date"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 310,
        "y": 60,
        "wires": [
            [
                "f89675e462ce03bb"
            ]
        ]
    },
    {
        "id": "7ebfd14d.6f0a2",
        "type": "function",
        "z": "21dbb5f62f40368e",
        "name": "resize",
        "func": "let input = msg.payload;\n\nconst resized = await sharp(input)\n  .metadata()\n  .then(({ width }) => sharp(input)\n    .resize({ width: 200 })\n    .toBuffer()\n);\n\nmsg.thumbnail = resized\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [
            {
                "var": "sharp",
                "module": "sharp"
            }
        ],
        "x": 450,
        "y": 200,
        "wires": [
            [
                "11af73fe.677eac"
            ]
        ]
    },
    {
        "id": "cfd1798c21f67f4b",
        "type": "change",
        "z": "21dbb5f62f40368e",
        "name": "end timer",
        "rules": [
            {
                "t": "set",
                "p": "data.properties.object.data.original.mjpeg_process_ms",
                "pt": "msg",
                "to": "$millis() - msg.process_start",
                "tot": "jsonata"
            },
            {
                "t": "delete",
                "p": "process_start",
                "pt": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 600,
        "y": 320,
        "wires": [
            []
        ]
    },
    {
        "id": "4676b6d77fb4c9fd",
        "type": "msg-speed",
        "z": "d9dd49bd747346d4",
        "name": "",
        "frequency": "sec",
        "interval": 1,
        "estimation": false,
        "ignore": false,
        "pauseAtStartup": false,
        "topicDependent": false,
        "x": 210,
        "y": 60,
        "wires": [
            [
                "75a772553f72adc5"
            ],
            []
        ]
    },
    {
        "id": "75a772553f72adc5",
        "type": "switch",
        "z": "d9dd49bd747346d4",
        "name": "== 0",
        "property": "payload",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "0",
                "vt": "num"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 190,
        "y": 120,
        "wires": [
            [
                "48bd712e32556613"
            ]
        ]
    },
    {
        "id": "48bd712e32556613",
        "type": "delay",
        "z": "d9dd49bd747346d4",
        "name": "",
        "pauseType": "rate",
        "timeout": "5",
        "timeoutUnits": "seconds",
        "rate": "1",
        "nbRateUnits": "1",
        "rateUnits": "minute",
        "randomFirst": "1",
        "randomLast": "5",
        "randomUnits": "seconds",
        "drop": true,
        "allowrate": false,
        "outputs": 1,
        "x": 210,
        "y": 180,
        "wires": [
            [
                "10b1bb59b99fd89e"
            ]
        ]
    },
    {
        "id": "10b1bb59b99fd89e",
        "type": "trigger",
        "z": "d9dd49bd747346d4",
        "name": "",
        "op1": "{\"topic\":\"restart\",\"payload\":\"ok\"}",
        "op2": "",
        "op1type": "json",
        "op2type": "nul",
        "duration": "5",
        "extend": false,
        "overrideDelay": false,
        "units": "s",
        "reset": "",
        "bytopic": "all",
        "topic": "topic",
        "outputs": 1,
        "x": 380,
        "y": 180,
        "wires": [
            [
                "67250f6441a6d8f2"
            ]
        ]
    },
    {
        "id": "67250f6441a6d8f2",
        "type": "change",
        "z": "d9dd49bd747346d4",
        "name": "restart",
        "rules": [
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "restart",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 530,
        "y": 180,
        "wires": [
            [
                "abcd5255fa959167"
            ]
        ]
    },
    {
        "id": "abcd5255fa959167",
        "type": "function",
        "z": "d9dd49bd747346d4",
        "name": "Restart",
        "func": "node.status({fill:\"red\",shape:\"ring\",text:\"no data, restart\"});\nreturn msg;",
        "outputs": 0,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 700,
        "y": 140,
        "wires": []
    },
    {
        "id": "42e72deef2a5b5b1",
        "type": "status",
        "z": "d9dd49bd747346d4",
        "name": "",
        "scope": [
            "4676b6d77fb4c9fd",
            "abcd5255fa959167"
        ],
        "x": 520,
        "y": 240,
        "wires": [
            []
        ]
    },
    {
        "id": "b63e9a83f3001f21",
        "type": "function",
        "z": "fd51dfdc3367d25c",
        "name": "Triton request",
        "func": "if(\"tensor\" in msg){\n    const serverUrl = env.get(\"server_url\");\n    const modelName = env.get(\"model_name\");\n    const requestTimeout = env.get(\"request_timeout\");\n    const tensor_data = msg.tensor.data;\n    const inference_header_length = msg.tensor.inference_header_length;\n\n    //Construct Triton Inference Server HTTP API message\n    msg.request_start = Date.now();\n    msg.payload = tensor_data\n    msg.method = \"POST\";\n    msg.requestTimeout = requestTimeout;\n    msg.url = serverUrl + \"/v2/models/\" + modelName + \"/infer\";\n    msg.headers = {\n        \"Content-Type\": \"application/octet-stream\",\n        \"Inference-Header-Content-Length\": inference_header_length,\n    };\n\n    return msg;\n}\nelse{\n    node.status({ fill: \"red\", shape: \"dot\", text: \"No msg.tensor in input msg\" });\n    node.error(\"No msg.tensor in input msg\", msg);\n    return null;\n}",
        "outputs": 1,
        "noerr": 0,
        "initialize": "",
        "finalize": "// Code added here will be run when the\n// node is being stopped or re-deployed.\ncontext.set(\"piscina\",{})",
        "libs": [
            {
                "var": "pis",
                "module": "piscina"
            },
            {
                "var": "fs",
                "module": "fs"
            },
            {
                "var": "zlib",
                "module": "zlib"
            },
            {
                "var": "os",
                "module": "os"
            },
            {
                "var": "process",
                "module": "process"
            },
            {
                "var": "path",
                "module": "path"
            }
        ],
        "x": 240,
        "y": 100,
        "wires": [
            [
                "3937c53e6f34bf73"
            ]
        ]
    },
    {
        "id": "3937c53e6f34bf73",
        "type": "http request",
        "z": "fd51dfdc3367d25c",
        "name": "req",
        "method": "use",
        "ret": "obj",
        "paytoqs": "ignore",
        "url": "",
        "tls": "",
        "persist": true,
        "proxy": "",
        "insecureHTTPParser": false,
        "authType": "",
        "senderr": false,
        "headers": [],
        "x": 410,
        "y": 100,
        "wires": [
            [
                "7451b5b3b174fc54"
            ]
        ]
    },
    {
        "id": "5368623e2deefd7e",
        "type": "status",
        "z": "fd51dfdc3367d25c",
        "name": "",
        "scope": [
            "7451b5b3b174fc54",
            "b63e9a83f3001f21"
        ],
        "x": 400,
        "y": 180,
        "wires": [
            []
        ]
    },
    {
        "id": "7451b5b3b174fc54",
        "type": "function",
        "z": "fd51dfdc3367d25c",
        "name": "Process req",
        "func": "let statuscode = msg.statusCode;\nlet request_ms = Date.now() - msg.request_start;\nlet color = \"\"\nmsg.request_ms = request_ms\n\nif (statuscode == 400 || statuscode == 404 || statuscode == 500){\n    color = \"red\"\n}\nelse{\n    color = \"blue\"\n    delete msg.request_start;\n    delete msg.headers;\n    delete msg.statusCode;\n    delete msg.method;\n    delete msg.retry;\n    delete msg.url;\n    delete msg.responseUrl;\n    delete msg.requestTimeout;\n    delete msg.redirectList;\n}\n\nnode.status({ fill: color, shape: \"dot\", text: statuscode +\" | inference: \" + request_ms + \" ms\" });\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 570,
        "y": 100,
        "wires": [
            []
        ]
    },
    {
        "id": "557647c8b195bdea",
        "type": "function",
        "z": "fde3bd1da88bc03c",
        "name": "Image to tensor",
        "func": "//Get a worker from the pool and execute the task\nconst start = Date.now();\nconst image = msg.payload;\nlet piscina = context.get(\"piscina\")\nlet pre_process_time;\n\ntry{\n    let result = await piscina.run({jpeg:image,gzip:false});\n       \n    msg.tensor = {\n        \"image\":image,\n        \"data\":Buffer.from(result.tensorBuffer),\n        \"tensor_shape\":result.tensorShape,\n        \"inference_header_length\":result.inference_header_length,\n        \"image_tensor_buffer_length\":result.image_tensor_buffer_length\n    }\n    \n    pre_process_time =  Date.now() - start;\n    msg.data.properties.computer_vision.pre_process_ms = pre_process_time\n    node.status({fill:\"green\",shape:\"dot\",text:pre_process_time+\" ms\"});    \n    return msg;\n}\ncatch(e){\n    node.status({fill:\"red\",shape:\"dot\",text:\"Convert failed...\"});    \n    node.error(\"Image to tensor conversion failed. Probably input is not an image buffer.\", msg);\n}",
        "outputs": 1,
        "noerr": 0,
        "initialize": "let nr_folder;\nconst scriptName = 'numjs_piscina_worker.js';\nvar worker_path = \"\"\nconst platform = os.platform()\nvar worker_script;\nlet sep;\nlet numberOfWorkers;\n\nnode.warn(\"Platform: \" + platform)\n\nif(platform == \"win32\"){\n   sep = \"\\\\\"\n   nr_folder = path.resolve()\n   worker_path = path.resolve(scriptName)\n   worker_script = \"console.log(\\\"Worker starting...\\\");\\r\\nlet nj;\\r\\nlet zlib;\\r\\n\\r\\n\\r\\ntry{\\r\\n\\tnj = require(\\\"./node_modules\\//numjs\\\")\\r\\n}\\r\\ncatch(e){\\r\\n\\tconsole.log(\\\"Loading numjs failed\\\")\\r\\n\\tconsole.log(e)\\r\\n}\\r\\n\\r\\ntry{\\r\\n\\tzlib = require(\\'zlib\\');\\r\\n}\\r\\ncatch(e){\\r\\n\\tconsole.log(e)\\r\\n}\\r\\n\\r\\nmodule.exports = async ({jpeg,gzip}) => {\\r\\n\\ttry{\\r\\n\\t\\tconst start = Date.now();\\t\\r\\n\\t\\tconst img = nj.images.read(jpeg);\\r\\n\\t\\tconst dataBuffer = Buffer.from(img.selection.data);\\r\\n\\t\\tconst shape = [1].concat(img.shape);\\r\\n\\t\\tconst image_tensor_buffer_length = dataBuffer.length\\r\\n\\t\\t\\r\\n\\t\\t\\r\\n\\t\\tconst inference_header = {\\r\\n\\t\\t  \\\"model_name\\\" : \\\"test\\\",\\r\\n\\t\\t  \\\"inputs\\\" : [\\r\\n\\t\\t\\t{\\r\\n\\t\\t\\t  \\\"name\\\" : \\\"input_tensor\\\",\\r\\n\\t\\t\\t  \\\"shape\\\" : shape,\\r\\n\\t\\t\\t  \\\"datatype\\\" : \\\"UINT8\\\",\\r\\n\\t\\t\\t  \\\"parameters\\\" : {\\r\\n\\t\\t\\t\\t  \\\"binary_data_size\\\":image_tensor_buffer_length\\r\\n\\t\\t\\t  }\\r\\n\\t\\t\\t}\\r\\n\\t\\t  ],\\r\\n\\t\\t  \\\"outputs\\\" : [\\r\\n\\t\\t\\t{\\r\\n\\t\\t\\t  \\\"name\\\" : \\\"detection_scores\\\",\\r\\n\\t\\t\\t  \\\"parameters\\\" : {\\r\\n\\t\\t\\t\\t\\\"binary_data\\\" : false\\r\\n\\t\\t\\t  }\\r\\n\\t\\t\\t},\\r\\n\\t\\t\\t {\\r\\n\\t\\t\\t  \\\"name\\\" : \\\"detection_boxes\\\",\\r\\n\\t\\t\\t  \\\"parameters\\\" : {\\r\\n\\t\\t\\t\\t\\\"binary_data\\\" : false\\r\\n\\t\\t\\t  }\\r\\n\\t\\t\\t},\\r\\n\\t\\t\\t {\\r\\n\\t\\t\\t  \\\"name\\\" : \\\"detection_classes\\\",\\r\\n\\t\\t\\t  \\\"parameters\\\" : {\\r\\n\\t\\t\\t\\t\\\"binary_data\\\" : false\\r\\n\\t\\t\\t  }\\r\\n\\t\\t\\t}\\r\\n\\t\\t  ]\\r\\n\\t\\t}\\r\\n\\r\\n\\t\\tconst inference_header_buffer = Buffer.from(JSON.stringify(inference_header))\\r\\n\\t\\tconst inference_header_length = inference_header_buffer.length\\r\\n\\t\\tlet payload = Buffer.concat([inference_header_buffer,dataBuffer])\\r\\n\\t\\ttime_ms =  Date.now() - start;\\r\\n\\t\\t\\r\\n\\t\\tif(gzip){\\r\\n\\t\\t\\tpayload = zlib.gzipSync(payload)\\r\\n\\t\\t}\\r\\n\\t\\t\\r\\n\\t\\tresult = {\\r\\n\\t\\t\\t\\\"tensorBuffer\\\" : payload,\\r\\n\\t\\t\\t\\\"Content-Type\\\" : \\\"application\\/octet-stream\\\",\\r\\n\\t\\t\\t\\\"inference_header_length\\\": inference_header_length,\\r\\n\\t\\t\\t\\\"image_tensor_buffer_length\\\":image_tensor_buffer_length,\\r\\n\\t\\t\\t\\\"tensorShape\\\":shape,\\r\\n\\t\\t\\t\\\"processing_time\\\":time_ms,\\r\\n\\t\\t\\t\\\"payload\\\":jpeg\\r\\n\\t\\t}\\t\\t\\r\\n\\t\\treturn result\\t\\t\\r\\n\\t}\\r\\n\\tcatch(e){\\r\\n\\t\\tconsole.log(e)\\r\\n\\t}\\r\\n\\t\\t\\r\\n}\"\n}\nelse{\n   nr_folder = path.resolve(\".node-red\")\n   node.warn(nr_folder)\n   worker_path = path.resolve(\".node-red\",scriptName)\n   node.warn(worker_path)\n   sep = \"/\" \n   worker_script = \"console.log(\\\"Worker starting...\\\");\\r\\nlet nj;\\r\\nlet zlib;\\r\\n\\r\\n\\r\\ntry{\\r\\n\\tnj = require(\\\"./node_modules\\//numjs\\\")\\r\\n}\\r\\ncatch(e){\\r\\n\\tconsole.log(\\\"Loading numjs failed\\\")\\r\\n\\tconsole.log(e)\\r\\n}\\r\\n\\r\\ntry{\\r\\n\\tzlib = require(\\'zlib\\');\\r\\n}\\r\\ncatch(e){\\r\\n\\tconsole.log(e)\\r\\n}\\r\\n\\r\\nmodule.exports = async ({jpeg,gzip}) => {\\r\\n\\ttry{\\r\\n\\t\\tconst start = Date.now();\\t\\r\\n\\t\\tconst img = nj.images.read(jpeg);\\r\\n\\t\\tconst dataBuffer = Buffer.from(img.selection.data);\\r\\n\\t\\tconst shape = [1].concat(img.shape);\\r\\n\\t\\tconst image_tensor_buffer_length = dataBuffer.length\\r\\n\\t\\t\\r\\n\\t\\t\\r\\n\\t\\tconst inference_header = {\\r\\n\\t\\t  \\\"model_name\\\" : \\\"test\\\",\\r\\n\\t\\t  \\\"inputs\\\" : [\\r\\n\\t\\t\\t{\\r\\n\\t\\t\\t  \\\"name\\\" : \\\"input_tensor\\\",\\r\\n\\t\\t\\t  \\\"shape\\\" : shape,\\r\\n\\t\\t\\t  \\\"datatype\\\" : \\\"UINT8\\\",\\r\\n\\t\\t\\t  \\\"parameters\\\" : {\\r\\n\\t\\t\\t\\t  \\\"binary_data_size\\\":image_tensor_buffer_length\\r\\n\\t\\t\\t  }\\r\\n\\t\\t\\t}\\r\\n\\t\\t  ],\\r\\n\\t\\t  \\\"outputs\\\" : [\\r\\n\\t\\t\\t{\\r\\n\\t\\t\\t  \\\"name\\\" : \\\"detection_scores\\\",\\r\\n\\t\\t\\t  \\\"parameters\\\" : {\\r\\n\\t\\t\\t\\t\\\"binary_data\\\" : false\\r\\n\\t\\t\\t  }\\r\\n\\t\\t\\t},\\r\\n\\t\\t\\t {\\r\\n\\t\\t\\t  \\\"name\\\" : \\\"detection_boxes\\\",\\r\\n\\t\\t\\t  \\\"parameters\\\" : {\\r\\n\\t\\t\\t\\t\\\"binary_data\\\" : false\\r\\n\\t\\t\\t  }\\r\\n\\t\\t\\t},\\r\\n\\t\\t\\t {\\r\\n\\t\\t\\t  \\\"name\\\" : \\\"detection_classes\\\",\\r\\n\\t\\t\\t  \\\"parameters\\\" : {\\r\\n\\t\\t\\t\\t\\\"binary_data\\\" : false\\r\\n\\t\\t\\t  }\\r\\n\\t\\t\\t}\\r\\n\\t\\t  ]\\r\\n\\t\\t}\\r\\n\\r\\n\\t\\tconst inference_header_buffer = Buffer.from(JSON.stringify(inference_header))\\r\\n\\t\\tconst inference_header_length = inference_header_buffer.length\\r\\n\\t\\tlet payload = Buffer.concat([inference_header_buffer,dataBuffer])\\r\\n\\t\\ttime_ms =  Date.now() - start;\\r\\n\\t\\t\\r\\n\\t\\tif(gzip){\\r\\n\\t\\t\\tpayload = zlib.gzipSync(payload)\\r\\n\\t\\t}\\r\\n\\t\\t\\r\\n\\t\\tresult = {\\r\\n\\t\\t\\t\\\"tensorBuffer\\\" : payload,\\r\\n\\t\\t\\t\\\"Content-Type\\\" : \\\"application\\/octet-stream\\\",\\r\\n\\t\\t\\t\\\"inference_header_length\\\": inference_header_length,\\r\\n\\t\\t\\t\\\"image_tensor_buffer_length\\\":image_tensor_buffer_length,\\r\\n\\t\\t\\t\\\"tensorShape\\\":shape,\\r\\n\\t\\t\\t\\\"processing_time\\\":time_ms,\\r\\n\\t\\t\\t\\\"payload\\\":jpeg\\r\\n\\t\\t}\\t\\t\\r\\n\\t\\treturn result\\t\\t\\r\\n\\t}\\r\\n\\tcatch(e){\\r\\n\\t\\tconsole.log(e)\\r\\n\\t}\\r\\n\\t\\t\\r\\n}\"\n}\n\ntry{\n    numberOfWorkers = 2\n}\ncatch(e){\n    node.warn(\"Loading numberOfWorkers from env variable failed... using default value = 1\")\n    node.warn(e)\n    numberOfWorkers = 1\n}\n\nfs.writeFile(worker_path, worker_script, function (err) {\n    if (err) {\n        node.error(\"Cannot create web_worker_test_script.js file: \" + err);\n    }\n});\n\ntry{\n    const piscina = new pis.Piscina({\n      filename: worker_path,\n      maxThreads: numberOfWorkers\n    });\n    \n    context.set(\"piscina\",piscina)\n    node.status({fill:\"green\", shape:\"dot\", text:\"Piscina initialized\"});\n}\ncatch(error){\n    node.warn(error)\n    node.status({fill:\"red\", shape:\"dot\", text:\"Initializing piscina failed...\"});\n}",
        "finalize": "// Code added here will be run when the\n// node is being stopped or re-deployed.\ncontext.set(\"piscina\",{})",
        "libs": [
            {
                "var": "pis",
                "module": "piscina"
            },
            {
                "var": "fs",
                "module": "fs"
            },
            {
                "var": "os",
                "module": "os"
            },
            {
                "var": "process",
                "module": "process"
            },
            {
                "var": "path",
                "module": "path"
            }
        ],
        "x": 220,
        "y": 60,
        "wires": [
            []
        ]
    },
    {
        "id": "752a76d98d97af39",
        "type": "status",
        "z": "fde3bd1da88bc03c",
        "name": "",
        "scope": null,
        "x": 240,
        "y": 140,
        "wires": [
            []
        ]
    },
    {
        "id": "1e80d9526ad0c4ee",
        "type": "function",
        "z": "9215deec580c5391",
        "name": "Post-process",
        "func": "function chunk(arr, chunkSize) {\n    if (chunkSize <= 0) throw \"Invalid chunk size\";\n    var R = [];\n    for (var i = 0, len = arr.length; i < len; i += chunkSize)\n        R.push(arr.slice(i, i + chunkSize));\n    return R;\n}\n\ntry{\n    let ready = flow.get(\"ready\") || false;\n\n    if (ready){\n        \n        let start = Date.now()\n        let inference_result = msg.payload;\n        let results = [];\n        const modelName = env.get(\"model_name\")\n        const model = flow.get(modelName);\n        const labels = model.parameters.labels;\n        const metadata = model.parameters.metadata;\n        const ts = msg.ts;\n        let post_process_time = 0;\n        const threshold = env.get(\"threshold\")\n        msg.threshold = threshold;\n        const image_width = msg.data.properties.object.data.width;\n        const image_height = msg.data.properties.object.data.height;\n\n        let scores;\n        let boxes;\n        let names;\n        let newObject;\n\n        for(let i=0;i<(inference_result.outputs).length;i++){  \n            if(inference_result.outputs[i][\"name\"] == \"detection_scores\"){\n                scores = inference_result.outputs[i].data\n            }\n            else if(inference_result.outputs[i][\"name\"] == \"detection_boxes\"){\n                boxes = inference_result.outputs[i].data\n                boxes = chunk(boxes,4)       \n            }\n            else if(inference_result.outputs[i][\"name\"] == \"detection_classes\"){\n                names = inference_result.outputs[i].data \n            }\n        } \n\n        for (let i = 0; i < scores.length; i++) {\n            if (scores[i] > threshold) {\n                newObject = {\n                    \"bbox\":[\n                            boxes[i][1] * image_width,\n                            boxes[i][0] * image_height,\n                            (boxes[i][3] - boxes[i][1]) * image_width,\n                            (boxes[i][2] - boxes[i][0]) * image_height\n                    ],\n                    \"class\":labels[names[i]-1],\n                    \"label\":labels[names[i]-1],\n                    \"score\":scores[i],\n                    \"length_cm\":NaN\n                    }\n                results.push(newObject)\n            }\n        }\n\n        post_process_time = Date.now() - start;     \n\n        msg.data.properties.computer_vision.result = results;\n        msg.data.properties.computer_vision.model = modelName;\n        msg.data.properties.computer_vision.inference_time = msg.request_ms;\n        msg.data.properties.computer_vision.type = \"object detection\";\n        msg.data.properties.computer_vision.metadata = metadata;\n        msg.data.properties.computer_vision.labels = labels;\n        msg.data.properties.computer_vision.threshold = msg.threshold;\n        msg.data.properties.computer_vision.validated = false;\n        msg.data.properties.computer_vision.post_process_time = post_process_time;\n\n        msg.payload = msg.tensor.image;\n        delete msg.tensor;\n        delete msg.request_ms;\n\n        node.status({ fill: \"blue\", shape: \"dot\", text: post_process_time+\" ms | \"+threshold});           \n        return msg;\n    }\n    else{\n        node.status({ fill: \"red\", shape: \"dot\", text: \"Modeldata is not loaded\"});\n        return null;\n    }\n}\ncatch(e){\n    node.error(\"Post process failed.\", msg);\n    return null;\n}\n",
        "outputs": 1,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 330,
        "y": 120,
        "wires": [
            []
        ]
    },
    {
        "id": "bc716e2cbc7c0995",
        "type": "http request",
        "z": "9215deec580c5391",
        "name": "status request",
        "method": "use",
        "ret": "obj",
        "paytoqs": "ignore",
        "url": "",
        "tls": "",
        "persist": false,
        "proxy": "",
        "insecureHTTPParser": false,
        "authType": "",
        "senderr": false,
        "headers": [],
        "x": 500,
        "y": 220,
        "wires": [
            [
                "c42a4bd0916459d6"
            ]
        ]
    },
    {
        "id": "e070e6af761a0137",
        "type": "function",
        "z": "9215deec580c5391",
        "name": "request",
        "func": "let modelName = env.get(\"model_name\")\nlet serverUrl = env.get(\"server_url\")\n\nmsg.requestTimeout = 1000;\nmsg.method = \"GET\";\nmsg.url = serverUrl+\"/v2/models/\"+modelName+\"/config\";\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 320,
        "y": 220,
        "wires": [
            [
                "bc716e2cbc7c0995"
            ]
        ]
    },
    {
        "id": "c42a4bd0916459d6",
        "type": "function",
        "z": "9215deec580c5391",
        "name": "status",
        "func": "let statusCode = msg.statusCode;\nlet parsed_value;\nlet modelName = env.get(\"model_name\")\n\nif(statusCode == 200){\n    let model_data = msg.payload;\n    parsed_value = JSON.parse(model_data.parameters.metadata.string_value)\n    model_data.parameters.labels = parsed_value.labels\n    model_data.parameters.metadata = parsed_value.metadata\n    flow.set(\"ready\",true)  \n    node.status({fill:\"green\",shape:\"dot\",text:\" Model configuration loaded.\"});   \n    flow.set(modelName,model_data)  \n    return msg;\n}\nelse{\n    flow.set(\"ready\",false)\n    node.status({fill:\"red\",shape:\"dot\",text:msg.statusCode+\": \"+\"Server is not ready or model not found.\"});   \n    return null\n}\n",
        "outputs": 1,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 670,
        "y": 220,
        "wires": [
            []
        ]
    },
    {
        "id": "973d9f1bf645853c",
        "type": "inject",
        "z": "9215deec580c5391",
        "name": "",
        "props": [
            {
                "p": "payload"
            },
            {
                "p": "topic",
                "vt": "str"
            }
        ],
        "repeat": "",
        "crontab": "",
        "once": true,
        "onceDelay": "2",
        "topic": "",
        "payload": "ready",
        "payloadType": "flow",
        "x": 150,
        "y": 220,
        "wires": [
            [
                "e070e6af761a0137"
            ]
        ]
    },
    {
        "id": "b93022c83cb00303",
        "type": "status",
        "z": "9215deec580c5391",
        "name": "",
        "scope": [
            "1e80d9526ad0c4ee",
            "c42a4bd0916459d6"
        ],
        "x": 660,
        "y": 300,
        "wires": [
            []
        ]
    },
    {
        "id": "449388bafc5ced99",
        "type": "inject",
        "z": "04508fd4d91dd935",
        "name": "KILL",
        "props": [
            {
                "p": "kill",
                "v": "SIGTERM",
                "vt": "str"
            },
            {
                "p": "topic",
                "vt": "str"
            }
        ],
        "repeat": "",
        "crontab": "",
        "once": false,
        "onceDelay": 0.1,
        "topic": "",
        "x": 110,
        "y": 100,
        "wires": [
            [
                "fe9c51b3673ce6ee"
            ]
        ]
    },
    {
        "id": "bb209580ff5b4836",
        "type": "debug",
        "z": "04508fd4d91dd935",
        "name": "",
        "active": true,
        "tosidebar": true,
        "console": false,
        "tostatus": false,
        "complete": "payload",
        "targetType": "msg",
        "statusVal": "",
        "statusType": "auto",
        "x": 470,
        "y": 60,
        "wires": []
    },
    {
        "id": "5d12eb90f740fdd6",
        "type": "debug",
        "z": "04508fd4d91dd935",
        "name": "",
        "active": true,
        "tosidebar": true,
        "console": false,
        "tostatus": false,
        "complete": "false",
        "statusVal": "",
        "statusType": "auto",
        "x": 470,
        "y": 100,
        "wires": []
    },
    {
        "id": "fe9c51b3673ce6ee",
        "type": "daemon",
        "z": "04508fd4d91dd935",
        "name": "RTSP server",
        "command": "/home/tequ/rtsp-simple-server/rtsp-simple-server",
        "args": "/home/tequ/rtsp-simple-server/rtsp-simple-server.yml",
        "autorun": true,
        "cr": true,
        "redo": true,
        "op": "string",
        "closer": "SIGKILL",
        "x": 270,
        "y": 100,
        "wires": [
            [
                "bb209580ff5b4836"
            ],
            [
                "5d12eb90f740fdd6"
            ],
            [
                "03372fcec2f8d5be"
            ]
        ]
    },
    {
        "id": "f4e0dab8f6118c77",
        "type": "comment",
        "z": "04508fd4d91dd935",
        "name": "Start local RTSP server ",
        "info": "",
        "x": 160,
        "y": 40,
        "wires": []
    },
    {
        "id": "03372fcec2f8d5be",
        "type": "debug",
        "z": "04508fd4d91dd935",
        "name": "",
        "active": true,
        "tosidebar": true,
        "console": false,
        "tostatus": false,
        "complete": "false",
        "statusVal": "",
        "statusType": "auto",
        "x": 470,
        "y": 140,
        "wires": []
    },
    {
        "id": "b50216a2b31d801b",
        "type": "inject",
        "z": "332da02c8e396138",
        "name": "kill",
        "props": [
            {
                "p": "kill",
                "v": "SIGTERM",
                "vt": "str"
            },
            {
                "p": "topic",
                "vt": "str"
            }
        ],
        "repeat": "",
        "crontab": "",
        "once": false,
        "onceDelay": 0.1,
        "topic": "kill",
        "x": 130,
        "y": 240,
        "wires": [
            [
                "d22449da926eb9b1"
            ]
        ]
    },
    {
        "id": "dfefe6ec0d823cdb",
        "type": "inject",
        "z": "332da02c8e396138",
        "name": "start",
        "props": [
            {
                "p": "payload"
            },
            {
                "p": "topic",
                "vt": "str"
            }
        ],
        "repeat": "",
        "crontab": "",
        "once": true,
        "onceDelay": 0.1,
        "topic": "start",
        "payload": "",
        "payloadType": "date",
        "x": 130,
        "y": 280,
        "wires": [
            [
                "d22449da926eb9b1"
            ]
        ]
    },
    {
        "id": "d22449da926eb9b1",
        "type": "subflow:fa7f491898fed374",
        "z": "332da02c8e396138",
        "name": "gst 40257292",
        "env": [
            {
                "name": "mjpeg_width",
                "value": "960",
                "type": "num"
            },
            {
                "name": "mjpeg_height",
                "value": "540",
                "type": "num"
            },
            {
                "name": "mjpeg_framerate",
                "value": "10",
                "type": "num"
            },
            {
                "name": "framerate",
                "value": "5",
                "type": "num"
            }
        ],
        "x": 340,
        "y": 200,
        "wires": [
            [
                "21f2142dbe030a40"
            ]
        ]
    },
    {
        "id": "9e2794684ea9e823",
        "type": "link in",
        "z": "332da02c8e396138",
        "name": "40257292 in",
        "links": [
            "3b4445143b5d37b1"
        ],
        "x": 165,
        "y": 200,
        "wires": [
            [
                "d22449da926eb9b1"
            ]
        ]
    },
    {
        "id": "21f2142dbe030a40",
        "type": "debug",
        "z": "332da02c8e396138",
        "name": "Log",
        "active": true,
        "tosidebar": true,
        "console": false,
        "tostatus": false,
        "complete": "payload",
        "targetType": "msg",
        "statusVal": "",
        "statusType": "auto",
        "x": 510,
        "y": 200,
        "wires": []
    },
    {
        "id": "927817224e3149a0",
        "type": "comment",
        "z": "332da02c8e396138",
        "name": "Start GStreamer and connect to Basler camera using camera Serial number",
        "info": "",
        "x": 410,
        "y": 40,
        "wires": []
    },
    {
        "id": "9a51f17f7e58e6b4",
        "type": "comment",
        "z": "332da02c8e396138",
        "name": "Encode camera video stream and publish to local RTSP server",
        "info": "",
        "x": 380,
        "y": 120,
        "wires": []
    },
    {
        "id": "1eba6a95dbad0f45",
        "type": "comment",
        "z": "332da02c8e396138",
        "name": "Encode camera video as JPEGs and stream to local TCP port",
        "info": "",
        "x": 380,
        "y": 80,
        "wires": []
    },
    {
        "id": "3097982829e635f4",
        "type": "tcp in",
        "z": "0d2c119e1f2d1adf",
        "name": "",
        "server": "server",
        "host": "localhost",
        "port": "50001",
        "datamode": "stream",
        "datatype": "buffer",
        "newline": "",
        "topic": "",
        "trim": false,
        "base64": false,
        "tls": "",
        "x": 110,
        "y": 180,
        "wires": [
            [
                "09f9c5151ea072ae"
            ]
        ]
    },
    {
        "id": "1a1e99dab44074a2",
        "type": "link out",
        "z": "0d2c119e1f2d1adf",
        "name": "link out 1",
        "mode": "link",
        "links": [
            "70b287d622b1444c"
        ],
        "x": 655,
        "y": 180,
        "wires": []
    },
    {
        "id": "3b4445143b5d37b1",
        "type": "link out",
        "z": "0d2c119e1f2d1adf",
        "name": "link out 2",
        "mode": "link",
        "links": [
            "9e2794684ea9e823"
        ],
        "x": 655,
        "y": 120,
        "wires": []
    },
    {
        "id": "09f9c5151ea072ae",
        "type": "subflow:21dbb5f62f40368e",
        "z": "0d2c119e1f2d1adf",
        "name": "Parse JPEG",
        "x": 310,
        "y": 180,
        "wires": [
            [
                "ecf6867daa7efe37",
                "105f5bee0e3a36ca",
                "cdbec18a5195f296"
            ]
        ]
    },
    {
        "id": "ecf6867daa7efe37",
        "type": "subflow:d9dd49bd747346d4",
        "z": "0d2c119e1f2d1adf",
        "name": "",
        "x": 480,
        "y": 120,
        "wires": [
            [
                "3b4445143b5d37b1"
            ]
        ]
    },
    {
        "id": "be61237047ae3829",
        "type": "multipart-encoder",
        "z": "0d2c119e1f2d1adf",
        "name": "Encode MJPEG stream",
        "statusCode": "",
        "ignoreMessages": true,
        "outputOneNew": false,
        "outputIfSingle": false,
        "outputIfAll": false,
        "globalHeaders": {
            "Content-Type": "multipart/x-mixed-replace;boundary=--myboundary",
            "Connection": "keep-alive",
            "Expires": "Fri, 01 Jan 1990 00:00:00 GMT",
            "Cache-Control": "no-cache, no-store, max-age=0, must-revalidate",
            "Pragma": "no-cache"
        },
        "partHeaders": {
            "Content-Type": "image/jpeg"
        },
        "destination": "all",
        "highWaterMark": 16384,
        "x": 790,
        "y": 420,
        "wires": [
            []
        ]
    },
    {
        "id": "0ac85f383fec55a1",
        "type": "switch",
        "z": "0d2c119e1f2d1adf",
        "name": "Camera ID?",
        "property": "req.params.id",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "40257292",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 550,
        "y": 460,
        "wires": [
            [
                "be61237047ae3829"
            ]
        ]
    },
    {
        "id": "9a076687e75945b0",
        "type": "switch",
        "z": "0d2c119e1f2d1adf",
        "name": "Camera ID?",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "40257292",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 550,
        "y": 420,
        "wires": [
            [
                "be61237047ae3829"
            ]
        ]
    },
    {
        "id": "a8b547526768ea32",
        "type": "http in",
        "z": "0d2c119e1f2d1adf",
        "name": "",
        "url": "/camera/id/:id/stream/:stream",
        "method": "get",
        "upload": false,
        "swaggerDoc": "",
        "x": 240,
        "y": 460,
        "wires": [
            [
                "0ac85f383fec55a1"
            ]
        ]
    },
    {
        "id": "d4207e520d8f51aa",
        "type": "link in",
        "z": "0d2c119e1f2d1adf",
        "name": "JPEGs in",
        "links": [
            "105f5bee0e3a36ca"
        ],
        "x": 115,
        "y": 420,
        "wires": [
            [
                "9a076687e75945b0"
            ]
        ]
    },
    {
        "id": "105f5bee0e3a36ca",
        "type": "link out",
        "z": "0d2c119e1f2d1adf",
        "name": "JPEGs out",
        "mode": "link",
        "links": [
            "d4207e520d8f51aa"
        ],
        "x": 435,
        "y": 240,
        "wires": []
    },
    {
        "id": "61c36ce572b971fc",
        "type": "multipart-encoder",
        "z": "0d2c119e1f2d1adf",
        "name": "Encode MJPEG stream",
        "statusCode": "",
        "ignoreMessages": true,
        "outputOneNew": false,
        "outputIfSingle": false,
        "outputIfAll": false,
        "globalHeaders": {
            "Content-Type": "multipart/x-mixed-replace;boundary=--myboundary",
            "Connection": "keep-alive",
            "Expires": "Fri, 01 Jan 1990 00:00:00 GMT",
            "Cache-Control": "no-cache, no-store, max-age=0, must-revalidate",
            "Pragma": "no-cache"
        },
        "partHeaders": {
            "Content-Type": "image/jpeg"
        },
        "destination": "all",
        "highWaterMark": 16384,
        "x": 790,
        "y": 520,
        "wires": [
            []
        ]
    },
    {
        "id": "d70ae79ef0a8cec0",
        "type": "switch",
        "z": "0d2c119e1f2d1adf",
        "name": "Camera ID?",
        "property": "req.params.id",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "40257292",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 550,
        "y": 560,
        "wires": [
            [
                "61c36ce572b971fc"
            ]
        ]
    },
    {
        "id": "533bb4939f458851",
        "type": "switch",
        "z": "0d2c119e1f2d1adf",
        "name": "Camera ID?",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "40257292",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 550,
        "y": 520,
        "wires": [
            [
                "61c36ce572b971fc"
            ]
        ]
    },
    {
        "id": "bb3a8984bab22f98",
        "type": "http in",
        "z": "0d2c119e1f2d1adf",
        "name": "",
        "url": "/camera/id/:id/annotated/:stream",
        "method": "get",
        "upload": false,
        "swaggerDoc": "",
        "x": 250,
        "y": 560,
        "wires": [
            [
                "d70ae79ef0a8cec0"
            ]
        ]
    },
    {
        "id": "a7845144eea23474",
        "type": "link in",
        "z": "0d2c119e1f2d1adf",
        "name": "Annoated JPEGs in",
        "links": [
            "4239127242a1d3f6"
        ],
        "x": 115,
        "y": 520,
        "wires": [
            [
                "7388910e1b0f790b"
            ]
        ]
    },
    {
        "id": "7388910e1b0f790b",
        "type": "function",
        "z": "0d2c119e1f2d1adf",
        "name": "base64 -> buffer",
        "func": "if (msg.data.properties.object.data.annotated.image !== \"\"){\n    msg.payload = Buffer.from(msg.data.properties.object.data.annotated.image, 'base64');\n}\nelse{\n    // do not change payload\n}\n\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 320,
        "y": 520,
        "wires": [
            [
                "533bb4939f458851"
            ]
        ]
    },
    {
        "id": "cdbec18a5195f296",
        "type": "subflow:fde3bd1da88bc03c",
        "z": "0d2c119e1f2d1adf",
        "name": "",
        "x": 500,
        "y": 180,
        "wires": [
            [
                "1a1e99dab44074a2"
            ]
        ]
    },
    {
        "id": "a2db749f57aed2eb",
        "type": "catch",
        "z": "0d2c119e1f2d1adf",
        "name": "",
        "scope": null,
        "uncaught": false,
        "x": 440,
        "y": 680,
        "wires": [
            [
                "13cf22af4398336a"
            ]
        ]
    },
    {
        "id": "13cf22af4398336a",
        "type": "debug",
        "z": "0d2c119e1f2d1adf",
        "name": "debug 8",
        "active": true,
        "tosidebar": true,
        "console": false,
        "tostatus": false,
        "complete": "true",
        "targetType": "full",
        "statusVal": "",
        "statusType": "auto",
        "x": 720,
        "y": 680,
        "wires": []
    },
    {
        "id": "cd9fd91e206fca37",
        "type": "comment",
        "z": "0d2c119e1f2d1adf",
        "name": "http://localhost:1880/camera/id/40257292/annotated/1",
        "info": "",
        "x": 320,
        "y": 380,
        "wires": []
    },
    {
        "id": "97a1c861f21d777e",
        "type": "comment",
        "z": "0d2c119e1f2d1adf",
        "name": "http://localhost:1880/camera/id/40257292/stream/1",
        "info": "",
        "x": 310,
        "y": 340,
        "wires": []
    },
    {
        "id": "6517a19f1fdd0eb0",
        "type": "comment",
        "z": "0d2c119e1f2d1adf",
        "name": "Publish camera original video and annotated data as MJPEG",
        "info": "",
        "x": 320,
        "y": 300,
        "wires": []
    },
    {
        "id": "e881009b9922778f",
        "type": "comment",
        "z": "0d2c119e1f2d1adf",
        "name": "Receive JPEG stream from GStreamer, parse JPEGs and convert to Tensor for inference",
        "info": "",
        "x": 330,
        "y": 60,
        "wires": []
    },
    {
        "id": "70b287d622b1444c",
        "type": "link in",
        "z": "88f8f06219122b1d",
        "name": "link in 1",
        "links": [
            "1a1e99dab44074a2"
        ],
        "x": 135,
        "y": 140,
        "wires": [
            [
                "ce92855a7e766140"
            ]
        ]
    },
    {
        "id": "9721d71a45af7eb5",
        "type": "msg-speed",
        "z": "88f8f06219122b1d",
        "name": "",
        "frequency": "sec",
        "interval": 1,
        "estimation": false,
        "ignore": false,
        "pauseAtStartup": false,
        "topicDependent": false,
        "x": 840,
        "y": 80,
        "wires": [
            [],
            []
        ]
    },
    {
        "id": "370ef41edc3f7b8a",
        "type": "subflow:83a7a965.1808a8",
        "z": "88f8f06219122b1d",
        "name": "",
        "env": [
            {
                "name": "labels",
                "value": "[\"fish\",\"perch\", \"pike\", \"rainbow trout\", \"salmon\", \"trout\", \"cyprinidae\", \"zander\", \"smolt\", \"bream\", \"teddy_bear\"]",
                "type": "json"
            }
        ],
        "x": 630,
        "y": 140,
        "wires": [
            [
                "9721d71a45af7eb5",
                "5b8d32a6e75956d4",
                "4239127242a1d3f6"
            ]
        ]
    },
    {
        "id": "5b8d32a6e75956d4",
        "type": "image",
        "z": "88f8f06219122b1d",
        "name": "",
        "width": "640",
        "data": "data.properties.object.data.annotated.image",
        "dataType": "msg",
        "thumbnail": false,
        "active": true,
        "pass": false,
        "outputs": 0,
        "x": 270,
        "y": 220,
        "wires": []
    },
    {
        "id": "4239127242a1d3f6",
        "type": "link out",
        "z": "88f8f06219122b1d",
        "name": "Processed data out",
        "mode": "link",
        "links": [
            "a7845144eea23474"
        ],
        "x": 785,
        "y": 140,
        "wires": []
    },
    {
        "id": "ce92855a7e766140",
        "type": "subflow:fd51dfdc3367d25c",
        "z": "88f8f06219122b1d",
        "name": "",
        "env": [
            {
                "name": "model_name",
                "value": "ssd-example-model",
                "type": "str"
            }
        ],
        "x": 250,
        "y": 140,
        "wires": [
            [
                "cbbb94cb2044318f"
            ]
        ]
    },
    {
        "id": "e0d971f1bdb1c5d0",
        "type": "catch",
        "z": "88f8f06219122b1d",
        "name": "",
        "scope": null,
        "uncaught": false,
        "x": 160,
        "y": 660,
        "wires": [
            [
                "c8ca5ddecdb7b754"
            ]
        ]
    },
    {
        "id": "37752b6bc7054154",
        "type": "debug",
        "z": "88f8f06219122b1d",
        "name": "debug 9",
        "active": false,
        "tosidebar": true,
        "console": false,
        "tostatus": false,
        "complete": "true",
        "targetType": "full",
        "statusVal": "",
        "statusType": "auto",
        "x": 610,
        "y": 100,
        "wires": []
    },
    {
        "id": "cbbb94cb2044318f",
        "type": "subflow:9215deec580c5391",
        "z": "88f8f06219122b1d",
        "name": "",
        "env": [
            {
                "name": "threshold",
                "value": "0.60",
                "type": "num"
            }
        ],
        "x": 440,
        "y": 140,
        "wires": [
            [
                "370ef41edc3f7b8a",
                "37752b6bc7054154"
            ]
        ]
    },
    {
        "id": "c8ca5ddecdb7b754",
        "type": "debug",
        "z": "88f8f06219122b1d",
        "name": "debug 10",
        "active": true,
        "tosidebar": true,
        "console": false,
        "tostatus": false,
        "complete": "true",
        "targetType": "full",
        "statusVal": "",
        "statusType": "auto",
        "x": 320,
        "y": 660,
        "wires": []
    },
    {
        "id": "f250c0709c7a33e1",
        "type": "comment",
        "z": "88f8f06219122b1d",
        "name": "Send Tensor to Triton Inference Server, process response and annotate it",
        "info": "",
        "x": 320,
        "y": 40,
        "wires": []
    }
]
```

