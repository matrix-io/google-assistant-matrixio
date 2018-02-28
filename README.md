# Setup Google Assistant with MATRIX Creator/Voice boards.

Checkout also this guide in [Hackster](https://www.hackster.io/matrix-labs/matrix-voice-with-google-assistant-e9751e)!

## Required Hardware

Before get started, let's review what you'll need.

Raspberry Pi 3 (Recommended) or Pi 2 Model B (Supported).
* MATRIX Voice - Raspberry Pi does not have a built-in microphone, the MATRIX Voice has an 8 mic array - Buy [MATRIX Voice](https://www.matrix.one/products/voice).
* Micro-USB power adapter for Raspberry Pi.
* Micro SD Card (Minimum 8 GB) - An operating system is required to get started. You can download [Raspbian Stretch](https://www.raspberrypi.org/downloads/raspbian/) and use the guides for [Mac OS](https://www.raspberrypi.org/documentation/installation/installing-images/mac.md), [Linux](https://www.raspberrypi.org/documentation/installation/installing-images/linux.md) and [Windows](https://www.raspberrypi.org/documentation/installation/installing-images/windows.md) in the Raspberry Pi website.
* External Speaker with 3.5 mm audio cable.
* A USB Keyboard & Mouse, and an external HDMI Monitor - we also recommend having a USB keyboard and mouse as well as an HDMI monitor handy. You can also use the Raspberry Pi remotely, see [this](https://developers.google.com/assistant/sdk/guides/library/python/embed/setup-headless) guide from Google.
* Internet connection (Ethernet or WiFi)
* (Optional) WiFi Wireless Adapter for Pi 2. Note: Pi 3 has built-in WiFi.

## Let's get started
Once you have the Raspberry Pi with the SD Card inserted, the MATRIX Voice connected to it, power up the Raspberry Pi and open a terminal either in the Raspbian Desktop or just by ssh into it ( if you are you are using the Raspbian Lite version, in this case see guide on how to ssh [here](https://developers.google.com/assistant/sdk/guides/library/python/embed/setup-headless)).

## 1. Installing MATRIX software
In order to allow the Google Assistant software to have access to MATRIX Voice microphones you need to first install:

```
# Add repo and key
curl https://apt.matrix.one/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.matrix.one/raspbian $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/matrixlabs.list
# Update packages and install
sudo apt-get update
sudo apt-get upgrade
# Installation MATRIX Pacakages
sudo apt install matrixio-creator-init
# Installation Kernel Packages
sudo apt-get -y install raspberrypi-kernel-headers raspberrypi-kernel git 
# Reboot
sudo reboot
```

After rebooting go and clone, build, and install the kernel repository following this steps:

```
# Clone, build and install kernel modules
git clone https://github.com/matrix-io/matrixio-kernel-modules
cd matrixio-kernel-modules
make && make install
```

and finally:

```
# Adding kernel module
echo "matrixio-everloop" | sudo tee -a /etc/modules
# Adding overlay
echo "dtoverlay=matrixio" | sudo tee -a /boot/config.txt
# reboot
sudo reboot
```

## 2. Configure and Test

```
# Setting up the asoundrc file
cd ~/matrixio-kernel-modules
cp misc/asoundrc ~/.asoundrc
```

Run this command to record a 5 sec audio and play it back

```
cd ~/
arecord --duration 5 --rate 16000 --format S16_LE test.wav && aplay ./test.wav
```
You should hear the recorded audio, then setup is good and you can move forward.

## 3. Setup Google Assistant.

In this step you will setup all the required software to finally run the Assistant. Make sure you are in the /home/pi directory before starting.

Follow the guides in the links below :

* [Configure Google Developer Project](https://developers.google.com/assistant/sdk/guides/library/python/embed/config-dev-project-and-account)
* [Install the Google Assistant SDK and Sample Code](https://developers.google.com/assistant/sdk/guides/library/python/embed/install-sample)
* [Register the Device Model](https://developers.google.com/assistant/sdk/guides/library/python/embed/install-sample)

## 4. Get code to use the Everloop

```
git clone https://github.com/matrix-io/google-assistant-matrixio.git
cd google-assistant-matrixio/
```

## 5. Run the Google Assistant!

Now you are ready. Just run the following line making sure you put your project id and device model id from previous steps :

```
~/google-assistant-matrixio/google-matrixio-assistant-hotword --project_id your-dev-project-id --device_model_id your-model-id
```

The output should be like this:

```
(env) pi@raspberrypi:~ $ ~/google-assistant-matrixio/google-matrixio-assistant-hotword --project_id your-dev-project-id --device_model_id your-model-id
device_model_id: your-model-id
device_id: XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
https://embeddedassistant.googleapis.com/v1alpha2/projects/your-dev-project-id/devices/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX 403
[3140:3159:ERROR:assistant_nupnp_client.cc(103)] Cloud discovery nupnp request failed: [60] Peer certificate cannot be authenticated with given CA certificates
[3140:3159:ERROR:assistant_nupnp_client.cc(103)] Cloud discovery nupnp request failed: [60] Peer certificate cannot be authenticated with given CA certificates
EventType.ON_MUTED_CHANGED
EventType.ON_START_FINISHED
[3140:3159:ERROR:assistant_nupnp_client.cc(103)] Cloud discovery nupnp request failed: [60] Peer certificate cannot be authenticated with given CA certificates 
```
Now you can talk to the Assistant starting with "OK Google" or just "Hey Google"!
