---
title: "Live streaming video and audio with a USB webcam"
categories:
  - Edge Case
tags:
  - content
  - css
  - edge case
  - lists
  - markup
header:
  overlay_image: /assets/images/webcam.jpg
---

This project aims to live stream video and audi with a USB webcam.

First, we need to install `ffmpeg`.
```bash
# install ffmpeg
$ cd ~
$ git clone git://source.ffmpeg.org/ffmpeg.git
$ cd ffmpeg
$ sudo ./configure && sudo make && sudo make install
```
Then, we can find the name of devices.

```bash
# show available devices
$ v4l2-ctl --list-devices
# show available sound cards
$ arecord -L
```

```markdown
{% raw %}![alt]({{ site.url }}{{ site.baseurl }}/assets/images/v4l2-list_devices.jpeg){% endraw %}
```

![Unsplash image 9]({{ site.url }}{{ site.baseurl }}/assets/images/arecord-L.jpeg)

Finally, we are able to live stream both video and audio of a USB webcam.
```bash
# stream both audio (plughw:2,0, or sysdefault:CARD=WEBCAM) and video (/dev/video1) via UDP
$ ffmpeg -f alsa -thread_queue_size 1024 -ar 44100 -ac 1 -i plughw:2,0 \
-f v4l2 -thread_queue_size 16 -s 640x480 -i /dev/video1 \
-f mpegts udp://192.168.31.27:5000?pkt_size=1316
# play live video streaming via UDP (there is about a couple of seconds delay)
$ ffplay -probesize 32 -analyzeduration 0 -sync ext udp://192.168.31.27:5000?pkt_size=1316
# or use vlc to play the video streaming
$ vlc udp://@:5000?pkt_size=1316
```
