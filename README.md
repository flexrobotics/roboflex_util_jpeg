# roboflex.util.jpeg

Roboflex support for jpeg compression to file and memory, and jpeg decompression from memory.

Useful for compressing images over slow transports (wifi, etc).

## System Dependencies

    None! We build jpeg-compression from source...

## pip install

    pip install roboflex.util.jpeg

## Import

    import roboflex.util.jpeg as ruj

## Nodes

There are two complementary nodes: `JPEGCompressor`, which can turn rgb tensors into jpegs in memory, and `JPEGDecompressor`, which does the opposite. Useful either for writing rgb tensors to files, or compressing them (NOTE jpeg is lossy).

    # all parameters are optional
    c = ruj.JPEGCompressor(

        # in the incoming message, where to find the rgb tensor
        image_key = "rgb",

        # in the outgoing message, where to place the jpeg data
        output_key = "jpeg", 

        # If this is provided, will ALSO write jpeg files with this 
        # prefix, with a variation of the date and time as the suffix.
        filename_prefix = "",

        # name of the node
        name = "JPEGCompressor",

        # prints internal info
        debug = False,
    )

... and ...

    c = ruj.JPEGDecompressor(

        # in the incoming message, where to find the jpeg data as a blob
        input_key = "jpeg",

        # in the outgoing message, where to place the rgb data as a tensor
        output_key = "rgb", 

        # name of the node
        name = "JPEGDecompressor",

        # prints internal info
        debug = False,
    )
