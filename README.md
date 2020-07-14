# TensorFlow-Object-Detection-on-ESP32-Video-Stream

## Introduction

<p align="center"><img src="https://github.com/senglk/TensorFlow-Object-Detection-on-ESP32-Video-Stream/blob/master/Images/preview.png" alt="Screenshot of Web Stream Object Detection" width="401" height="316" /></p>

This Python script is meant to be run on a computer, and attempts to extend the tutorial on [Tensor Flow Object Detection API](https://tensorflow-object-detection-api-tutorial.readthedocs.io/en/latest/index.html) to enable TensorFlow object detection on 'HTTP Multipart MJPEG' video streams.

As noted by the [author](https://github.com/sglvladi) of the [Tensor Flow Object Detection Tutorial](https://github.com/sglvladi/TensorFlowObjectDetectionTutorial), at the time of writing, Object Detection model training and evaluation was not migrated to TensorFlow 2.X, but object detection using pre-trianed models work.

## Prerequisites

### ESP32-CAM

While any video stream of 'HTTP Multipart MJPEG' should work, this was tested on with video stream from the ESP32-CAM. 

The [ESP32-CAM](https://www.espressif.com/en/news/ESP32_CAM) consists of a ESP32 module and OV2460 camera sensor, integrated onto a development board. WiFi functionality of this device is enabled by the ESP32 module, and video is achieved by successive calls to the camera.

For the ESP32-CAM, you will need to upload the code for streaming video over WiFi via [JPEG HTTP Stream](https://github.com/espressif/esp32-camera#jpeg-http-stream) (or what I call 'HTTP Multipart MJPEG').

### Python 3

This script was written for Python 3 (preferably 3.7 and above). You will need to have Python 3 installed on your computer.

The script requires the following libraries (and dependencies therein).

### TensorFlow

TensorFlow is a Python library for fast numerical computing created and released by Google.

The instructions to install TensorFlow can be found [here](https://tensorflow-object-detection-api-tutorial.readthedocs.io/en/latest/install.html#tensorflow-cpu). The TensorFlow CPU variant should work fine for this script. You can 'upgrade' to the GPU variant in the future if you have compatible hardware.

As I had TensorFlow 2.1 installed, I confirm that TensorFlow 2.1 object detection works with pre-trained models. As noted [here](https://tensorflow-object-detection-api-tutorial.readthedocs.io/en/latest/index.html), TensorFlow 2.X would work on pre-trained models, but model and training is not yet implemented (as of writing this README), so you might want to go for Tensorflow 1.14 instead if you wish to do more than object detection.

While using TensorFlow GPU, I found that the code would run for a few frames (i.e. less than a second, since at 800x600 I get a 12fps stream), before freezing. It is unclear if this is because I am using TensorFlow 2.1, but I found that the workaround is to force the use of TensorFlow CPU when running this script by adding `os.environ["CUDA_VISIBLE_DEVICES"] = "-1"` at the top of the script, right after the imports, [as commented in the script](https://github.com/senglk/TensorFlow-Object-Detection-on-ESP32-Video-Stream/blob/master/WebStreamObjectDetection.py#L24).

### TensorFlow models

After installing TensorFlow, you will need to install the TensorFlow models. If you are using [Anaconda](https://www.anaconda.com/download/), you can start following the instructions from [install-prerequisites](https://tensorflow-object-detection-api-tutorial.readthedocs.io/en/latest/install.html#install-prerequisites) to [adding necessary environment variables](https://tensorflow-object-detection-api-tutorial.readthedocs.io/en/latest/install.html#adding-necessary-environment-variables).

If you are not using [Anaconda](https://www.anaconda.com/download/), then you can install the TensorFlow models with the following steps:

1. Install the prequisite packages via pip:

        pip install pillow lxml matplotlib opencv-python

2. Download and extract the models (especially the research folder) via [this link](https://github.com/tensorflow/models/tree/r1.13.0), and place them in a folder as described by [this link](https://tensorflow-object-detection-api-tutorial.readthedocs.io/en/latest/install.html#downloading-the-tensorflow-models) (ignore the link in the note):

        TensorFlow
        └─ models
            ├── official
            ├── research
            ├── samples
            └── tutorials

    Unfortunately, the TensorFlow Official Release Models does not include the `research` folder contents (where the pre-trained models are stored).

3. Download and extract [Protobuf](https://github.com/google/protobuf/releases) to a folder of your choice, add it to your `PATH`, and run the following command via command prompt in the research folder as described [here](https://tensorflow-object-detection-api-tutorial.readthedocs.io/en/latest/install.html#protobuf-installation-compilation):

        # From within TensorFlow/models/research/
        for /f %i in ('dir /b object_detection\protos\*.proto') do protoc object_detection\protos\%i --python_out=.

4. Finally, add the necessary [Environment Variables](https://tensorflow-object-detection-api-tutorial.readthedocs.io/en/latest/install.html#adding-necessary-environment-variables).

## Usage

1. Download and Save the `WebStreamObjectDetection.py` script from this repository to `/TensorFlow/models/research/object_detection`.

2. Connect your computer to the same WiFi network as the ESP32-CAM. This can be done by connecting the ESP32-CAM to a router/ mobile hotspot, or letting the ESP32-CAM run in softAP mode.

3. Update the saved `WebStreamObjectDetection.py` with details of the WiFi connection (i.e. IP Address and Port Number) to the ESP32-CAM 'HTTP Multipart MJPEG' video stream in [this line](https://github.com/senglk/TensorFlow-Object-Detection-on-ESP32-Video-Stream/blob/master/WebStreamObjectDetection.py#L27) if necessary.

3. Execute the script:
        
        # From /TensorFlow/models/research/object_detection
        python WebStreamObjectDetection.py
