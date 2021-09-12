---
Title: ffmpeg
---
# Encoding

```
ffmpeg -i input.mp4 \
    -c:v libx265 -preset veryslow -crf 17 \
    -c:a aac -b:a 320k \
    output.mp4
```

## [Encode/H.265 – FFmpeg](https://trac.ffmpeg.org/wiki/Encode/H.265)

- Choose a CRF. CRF affects the quality. The default is 28, and it should visually correspond to libx264 video at CRF 23, but result in about half the file size. CRF works just like in x264, so choose the highest value that provides an acceptable quality. The range of the CRF scale is 0–51, where 0 is lossless, 23 is the default, and 51 is worst quality possible. A lower value generally leads to higher quality, and a subjectively sane range is 17–28. Consider 17 or 18 to be visually lossless or nearly so; it should look the same or nearly the same as the input but it isn't technically lossless.
- Choose a preset. The default is `medium`. The preset determines compression efficiency and therefore affects encoding speed. Valid presets are `ultrafast`, `superfast`, `veryfast`, `faster`, `fast`, `medium`, `slow`, `slower`, `veryslow`, and `placebo`. Use the slowest preset you have patience for. Ignore `placebo` as it provides insignificant returns for a significant increase in encoding time.

## [Encode/AAC – FFmpeg](https://trac.ffmpeg.org/wiki/Encode/AAC)

- Set the bit rate. As a rule of thumb, for audible transparency, use 64 kBit/s for each channel (so 128 kBit/s for stereo, 384 kBit/s for 5.1 surround sound).

# Filtering

## Crop pixels from the top

```
ffmpeg -vf "crop=in_w:in_h-42:0:out_h" input.mp4
```

## Scale to 4K

```
ffmpeg -vf "scale=3840:2160" input.mp4
```

## Using multiple filters

```
ffmpeg -vf "crop=in_w:in_h-123:0:out_h, scale=3840:2160" input.mp4
```

## With encoding

```
ffmpeg -i input.mp4 \
    -vf "crop=in_w:in_h-123:0:out_h, scale=3840:2160" \
    -c:v libx265 -preset veryslow -crf 17 \
    -c:a aac -b:a 320k \
    output.mp4
```

# Miscellanea

## [video - Fetch frame count with ffmpeg - Stack Overflow](https://stackoverflow.com/questions/2017843/fetch-frame-count-with-ffmpeg)

```
ffprobe -v error -select_streams v:0 -count_packets -show_entries stream=nb_read_packets -of csv=p=0 input.mp4
```

This actually counts packets instead of frames but it is much faster. Result should be the same. If you want to verify by counting frames change `-count_packets` to `-count_frames` and `nb_read_packets` to `nb_read_frames`.

* `-v error` This hides "info" output (version info, etc) which makes parsing easier (but makes it harder if you ask for help since it hides important info).
* `-count_frames` Count the number of packets per stream and report it in the corresponding stream section.
* `-select_streams v:0` Select only the first video stream.
* `-show_entries stream=nb_read_packets` Show only the entry for `nb_read_frames`.
* `-of csv=p=0` sets the output formatting. In this case it hides the descriptions and only shows the value. See [FFprobe Writers](https://ffmpeg.org/ffprobe.html#Writers) for info on other formats including JSON.

## [linux - Cutting the videos based on start and end time using ffmpeg - Stack Overflow](https://stackoverflow.com/questions/18444194/cutting-the-videos-based-on-start-and-end-time-using-ffmpeg)

```
ffmpeg -ss 01:00.500 -i input.mp4 -to 02:00 -c copy output.mp4
```

The timing format is _[-][HH:]MM:SS[.m...]_
