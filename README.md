
# FFmpeg

My personal notes on how to use FFmpeg through the command-line interface.

## How to's

**Listing the streams**

You can list the streams in a file by giving it as input to FFmpeg without specifying any output file.
```bash
ffmpeg -i video.mkv
```

**Selecting streams**

Use the `-map` option:
```bash
ffmpeg -i video.mkv -map 0:0 -map 0:1 -map 0:3 output.mkv
```

Learn more about stream selection in FFmpeg: https://trac.ffmpeg.org/wiki/Map

**Cropping**

Detecting the crop:
```bash
ffmpeg -i video.mkv -vf cropdetect -f null -
```

Applying the crop:
```bash
ffmpeg -i video.mkv -vf crop=w:h:x:y output.mkv
```

**Trimming**

Use the `-ss` option before specifying the input files for fast skipping to a particular time.
Use the `-t` option to specify the duration that you want to encode.

Example:
```bash
ffmpeg -ss 00:10:00 -i video.mkv -t 15 output.mkv
```

**Convert to stereo**

Use `-ac 2` for converting 5-channel Dolby Surround to stereo.

**Encoding formats**

- video: h265 (see [H.265/HEVC Video Encoding Guide](https://trac.ffmpeg.org/wiki/Encode/H.265))
- audio: libopus (see [Guidelines for high quality lossy audio encoding](https://trac.ffmpeg.org/wiki/Encode/HighQualityAudio))

```bash
ffmpeg -i video.mkv -c:v libx265 -crf 26  -c:a libopus -ac 2 output.mkv
```

**Fast preset**

Control the speed of the encoding by changing from the default preset. 
This can be useful when testing various encoding formats for filesize / quality.

```bash
ffmpeg -i video -preset fast output.mkv
```

**Deinterlacing**

Use the yadif video filter: `-vf yadif`.

More: https://video.stackexchange.com/questions/17396/how-to-deinterlacing-with-ffmpeg

**Sound normalisation**

Use the `loudnorm` filter.

This is a two-pass process.

First pass:

```bash
ffmpeg -i audio.mka -filter:a loudnorm=print_format=json -f null NULL
```

Output:
```
[Parsed_loudnorm_0 @ 000002a2bcfa8ec0]
{
    "input_i" : "-16.92",
    "input_tp" : "0.55",
    "input_lra" : "24.20",
    "input_thresh" : "-29.83",
    "output_i" : "-25.37",
    "output_tp" : "-2.00",
    "output_lra" : "16.20",
    "output_thresh" : "-36.52",
    "normalization_type" : "dynamic",
    "target_offset" : "1.37"
}
```

Second pass:

```bash
ffmpeg -i audio.mka -filter:a loudnorm=linear=true:measured_i=-16.92:measured_tp=0.55:measured_lra=24.20:measured_thresh=-29.83 -ar 48000 -c:a libopus -ac 2 output.mka
```

More: https://wiki.tnonline.net/w/Blog/Audio_normalization_with_FFmpeg

**Extracting frames**

Specify an output pattern with the `.png` extension.

```bash
ffmpeg -i myvideo.avi -vf fps=1/60 img%04d.png
```

The `fps` filter controls the number of frame extracted per second.

More: https://stackoverflow.com/questions/10957412/fastest-way-to-extract-frames-using-ffmpeg

## Full tutorials

- [Creating a digital copy of blu-ray discs](BDRIP.md)
