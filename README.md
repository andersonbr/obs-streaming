# Initializing

## Docker installation and running nginx container

* Install and run docker

* Exec ./run.sh

After this you will be able to send stream from OBS to nginx inside docker container

## OBS custom stream:

url: rtmp://127.0.0.1:1935/dash
stream key: live

# send to services

## Youtube

ffmpeg -i rtmp://localhost:1935/dash/live -c:a copy -c:v copy -f flv rtmp://a.rtmp.youtube.com/live2/YOUR_YOUTUBE_KEY

## Facebook
ffmpeg -i rtmp://localhost:1935/dash/live -s 1280x720 -c:v libx264 -v:b 3000k -c:a copy -f flv rtmps://live-api-s.facebook.com:443/rtmp/YOUR_FACEBOOK_KEY

* change libx264 to h264_videotoolbox to use GPU acceleration on macOS

# optional

## transcoding from DASH to HLS using GPU acceleration (for internal streaming)
ffmpeg -i rtmp://localhost/dash/live \
  -c:a aac -b:a 32k  -c:v h264_videotoolbox -b:v 128K -vf scale=1280:720 -f flv rtmp://localhost/hls/live_low \
  -c:a aac -b:a 64k  -c:v h264_videotoolbox -b:v 256k -vf scale=1280:720 -f flv rtmp://localhost/hls/live_mid \
  -c:a aac -b:a 128k -c:v h264_videotoolbox -b:v 512K -vf scale=1280:720 -f flv rtmp://localhost/hls/live_hi

## From local file
ffmpeg -re -i ARQUIVO.flv ....
