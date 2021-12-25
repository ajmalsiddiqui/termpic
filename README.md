# TermPic

An image renderer for the terminal. Written in Go.

## Usage

You need Golang to build the binary.

```bash
# Build it
$ go build -o termpic main.go

# Run it
$ ./termpic test.png <skip value>
```

## The `skip` Parameter

The second argument to `termpic` is the `skip` parameter. This defines how many pixels to skip (along both the x and y axes) after rendering a pixel. Default is set to 0.

Obviously the lower the value of skip, the better the detail and resolution of the rendered image. But a higher value can be useful for two reasons:
- Higher res images with a ton of pixels make this program prohibitively slow (I had to set skip to 1 or 2 to work with a 640x640 image).
- Your terminal may not be wide enough to display the whole image. Since each pixel is half the height of the terminal cursor, which is MASSIVE compared to a pixel on the screen, your max resolution will be lower.

## Caveats

- Currently only works with png and jpeg images.
- This really needs some optimization, since it is prohibitively slow for images in the 500x500 range and higher. Obviously I need to parallelize the main loop.

## Algorithm

We use [ANSI escape sequences](https://notes.burke.libbey.me/ansi-escape-codes/) to control the background and text colours of the terminal as we render blocks.

We construct 2 rows of pixels at a time. The lower row is constructed by setting the background colour of the terminal to the colour of the pixel we want to render, and the upper row is constructed by rendering the UPPER_HALF_BLOCK (▀) character, which is a square. Printing this character renders both the background and the block, thus rendering two pixels vertically.
