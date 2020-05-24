# Initializing

## Docker installation and running nginx container

* Install and run docker

* Exec ./run.sh

After this you will be able to send stream from OBS to nginx inside docker container

## OBS custom stream:

url: rtmp://127.0.0.1:1935/dash
stream key: live

## Advanced OBS output config

* recording tab

  * custom output (ffmpeg) -> output to url -> udp://224.0.0.1:9999

  * container format (mpegts)

  * video bitrate between 3000 - 4000 kbps

  * keyframes (120) = 4s when 30fps

  * check all codecs, and select h264_videotoolbox for macOS

  * config line: -preset veryfast -profile:v baseline

  * audio bitrate: 128 - 256kbps

  * audio codec: aac


# send to services

## Youtube
ffmpeg -i rtmp://localhost:1935/dash/live -c:a aac -b:a 256k -c:v h264_videotoolbox -b:v 4500k -bf 4 -f flv rtmp://x.rtmp.youtube.com/live2/YOUTUBE_KEY

## Facebook
ffmpeg -i rtmp://localhost:1935/dash/live -vf scale=1280:720 -c:v h264_videotoolbox -b:v 3000k -c:a aac -b:a 128k \
  -f flv rtmps://live-api-s.facebook.com:443/rtmp/YOUR_FACEBOOK_KEY

## Instagram
ffmpeg -i rtmp://localhost:1935/dash/live -vf "scale=1280:720,crop=608:1080:656:0" -c:v h264_videotoolbox -b:v 3000k -c:a aac -b:a 128k \
  -f flv "rtmps://live-upload.instagram.com:443/rtmp/INSTAGRAM_KEY"

* to get Instagram Key, use YellowDuck.tv

* change libx264 to use CPU encoding or h264_videotoolbox to use GPU acceleration on macOS

## Multiple services with same encoding
ffmpeg -i rtmp://localhost:1935/dash/live -vf "scale=1280:720" -c:v h264_videotoolbox \
  -b:v 3000k -c:a aac -b:a 128k -f mpegts - | ffmpeg -f mpegts -i - \
  -c copy -f flv rtmp://x.rtmp.youtube.com/live2/FACEBOOK_KEY \
  -c copy -f flv "rtmps://live-upload.instagram.com:443/rtmp/INSTAGRAM_KEY"

## From UDP to services
ffmpeg -i udp://@:9999?overrun_nonfatal=1&fifo_size=50000000 \
  -c copy -f flv rtmp://localhost:1935/hls/live \
  -c copy -f flv rtmp://x.rtmp.youtube.com/live2/FACEBOOK_KEY \
  -c copy -f flv "rtmps://live-upload.instagram.com:443/rtmp/INSTAGRAM_KEY"


# optional

## transcoding from DASH to HLS using GPU acceleration (for internal streaming)
ffmpeg -i rtmp://localhost/dash/live \
  -c:a aac -b:a 32k  -c:v h264_videotoolbox -b:v 128k -vf scale=1280:720 -f flv rtmp://localhost/hls/live_low \
  -c:a aac -b:a 64k  -c:v h264_videotoolbox -b:v 256k -vf scale=1280:720 -f flv rtmp://localhost/hls/live_mid \
  -c:a aac -b:a 128k -c:v h264_videotoolbox -b:v 3000k -vf scale=1280:720 -f flv rtmp://localhost/hls/live_hi

## From local file
ffmpeg -re -i ARQUIVO.flv ....


## Other options with ffmpeg

https://trac.ffmpeg.org/wiki/Creating%20multiple%20outputs

## rtmp nginx module documentation

https://github.com/arut/nginx-rtmp-module