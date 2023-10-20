# Mt Everest, National Geographic, 1988, 3D
3D mapping of very high elevation terrain using MapLibre GL JS

I encountered a lot of difficulties generating RGB tif files from DEMs of terrain with extreme ranges of elevations, so I wanted to document how I was able to overcome this using a scan one of my favorite wall maps - a 1988 National Geographic Magazine shaded topographic map insert of Mt. Everest.

## Problem: RGB Outputs Display Spikes or Cave-Ins in MapLibre GL JS Visualizations

When I used the same rgbify commands for high-relief DEM data as for lower elevation low-relief data, I had issues with spikes appearing at 3000 meters or would see cave-ins when trying to correct for this in my Terminal commands.

## Solution: Use a TXT File with RGB Designations for the Full Elevation Range with GDALDEM to Generate a Smooth and Continuous RGB TIF

First, I created a txt file I called "color_relief.txt" and placed the following text within:

```
3590 68 1 84
8590 253 231 37
```

In meters, 3590 to 8590 contained the full range of elevation values in my map. The numbers that follow these in each row are RGB values. I used this file to create a continuous range of colors between these two values.

Opening Terminal in MacOS, I used the following command to create an output RGB tif file using this color range:

```
gdaldem color-relief input_dem.tif color_relief.txt output_rgb.tif
```

Replace "input_dem.tif" and "output_rgb.tif" with your file names.

Next, I converted the output to mbtiles with the following command:

```
rio mbtiles output_rgb.tif output_rgb.mbtiles --format PNG --zoom-levels 0..14 --tile-size 256 --resampling bilinear
```

Again, replace the file names with your own.

Finally, I converted the mbtiles to pmtiles for use with MapLibre GL JS, using the following command:

```
pmtiles convert output_rgb.mbtiles output_rgb.pmtiles
```

Using this output with pmtiles of the scanned topographic map yielded the [following results](https://jebowe3.github.io/Mt-Everest-NGM-1988-3D/).