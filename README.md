# png-js
This is a modified version of [png.js](https://github.com/react-pdf/png.js) that is intended to be used for palette swapping within the browser.

**NOTE**: Only supports indexed PNGs for obvious reasons. All others will cause IndexedPNG to throw during construction.

## Usage
The following shows the process of loading a PNG file and rendering it to a canvas with its palette data reversed.

```
import { IndexedPNG }   from '@kettek/png-js/src'
import { readFile   }   from 'fs'

readFile("my_png.png", (err, data) => {
    if (err) throw err
    
    // Create an IndexedPNG object from our data.
    let png = new IndexedPNG(data)
    
    // Preemptively decode PNG data (optional).
    png.decode()
    
    // Convert our PNG data into an ImageData, usable by a Canvas. Passed options provide a reversed palette and a clipping.
    png.toImageData({
      palette: png.decodePalette().reverse(),
      clip: {
        x: 16,
        y: 16,
        w: 32,
        h: 32
      }
    })
    .then(imageData => {
      // This presumes at least one canvas element exists on the page.
      document.getElementsByTagName('canvas')[0]
      .getContext('2d')
      .putImageData(imageData, 0, 0)
    })
    .catch(err => {
        throw err
    })
})
```

## Types
### IndexedPNG

IndexedPNG([Buffer](https://nodejs.org/api/buffer.html#buffer_class_buffer))
:  processes PNG data but does not decode it.

async decodePixels()
: Decodes the PNG pixel data and returns it.

decodePalette()
: Decodes the PNG palette and returns it as a [Palette](#palette).

async decode()
: Decodes the palette and the pixel data of the PNG data passed into the constructor. Calls `decodePixels()` and `decodePalette()`.

[ImageData](https://developer.mozilla.org/en-US/docs/Web/API/ImageData) async toImageData([ImageDataOptions](#imagedataoptions)) 
: Returns ImageData. Calls `decode()` automatically if PNG has not been decoded.
  
### ImageDataOptions
Object containing options to apply during `toImageData()`.


| Property | Type                | Description
|----------|---------------------|---------------
| palette  | [Palette](#palette) | Palette data to use for decoding.
| clip     | [Clip](#clip)       | Clipping options to use for decoding.
 
#### Palette
A [Buffer](https://nodejs.org/api/buffer.html#buffer_class_buffer) or Array containing the palette RGBA palette data.

```
let palette = [
    255, 0  ,  0  , 255, // Index 0
    0  , 255,  0  , 255, // Index 1
    0  , 0  ,  255, 255, // Index 2
    ...
]
```
#### Clip
Options to clip pixel data.

| Property | Type   | Defaults     | Description
|----------|--------|--------------|-------------
| x        | Number | 0            | Starting X coordinate.
| y        | Number | 0            | Starting Y coordinate.
| w        | Number | width - x    | Width of clip.
| h        | Number | height - y   | Height of clip.

```
let clip = {
    x: 16,
    y: 16,
    w: 32,
    h: 32,
}
```
