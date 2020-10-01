# Transcode_tf_sr.py


## [Jupyter Frame Viewer](https://github.com/kkroening/ffmpeg-python/blob/master/examples/ffmpeg-numpy.ipynb)

<img src="https://raw.githubusercontent.com/kkroening/ffmpeg-python/master/doc/jupyter-screenshot.png" alt="jupyter screenshot" width="75%" />

## [Jupyter Stream Editor](https://github.com/kkroening/ffmpeg-python/blob/master/examples/ffmpeg-numpy.ipynb)

<img src="https://raw.githubusercontent.com/kkroening/ffmpeg-python/master/doc/jupyter-demo.gif" alt="jupyter demo" width="75%" />

## [Tensorflow Streaming](https://github.com/kkroening/ffmpeg-python/blob/master/examples/tensorflow_stream.py)

<img src="https://raw.githubusercontent.com/kkroening/ffmpeg-python/master/examples/graphs/tensorflow-stream.png" alt="tensorflow streaming; challenge mode: combine this with the webcam example below" width="55%" />

- Decode input video with ffmpeg
- Process video with tensorflow using "deep dream" example
- Encode output video with ffmpeg

```python
process1 = (
    ffmpeg
    .input(in_filename)
    .output('pipe:', format='rawvideo', pix_fmt='rgb24', vframes=8)
    .run_async(pipe_stdout=True)
)

process2 = (
    ffmpeg
    .input('pipe:', format='rawvideo', pix_fmt='rgb24', s='{}x{}'.format(width, height))
    .output(out_filename, pix_fmt='yuv420p')
    .overwrite_output()
    .run_async(pipe_stdin=True)
)

while True:
    in_bytes = process1.stdout.read(width * height * 3)
    if not in_bytes:
        break
    in_frame = (
        np
        .frombuffer(in_bytes, np.uint8)
        .reshape([height, width, 3])
    )

    # See examples/tensorflow_stream.py:
    out_frame = deep_dream.process_frame(in_frame)

    process2.stdin.write(
        out_frame
        .astype(np.uint8)
        .tobytes()
    )

process2.stdin.close()
process1.wait()
process2.wait()
```

<img src="https://raw.githubusercontent.com/kkroening/ffmpeg-python/master/examples/graphs/dream.png" alt="deep dream streaming" width="40%" />
