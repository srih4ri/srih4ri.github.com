---
layout: post
title: "Finding what’s the highest altitude you have clicked a photo at"
date: "2016-09-17"
comments: true
categories: python
---

I live at an altitude of 18m above sea level. Coming back after seeing himalayas for the first time, I was curious to see how far above did I go.

One way to find this out would be to check the altitudes of places I’ve been to. However, if we do this, we might miss the passes and peaks we crossed in between.

I clicked photos using my phone during the trip, and I knew my phone saved GPS information along with the photo when I clicked it. It’s highly likely your phone does that too.

Along with every photo, information like time the photo is taken, Model of the camera etc is saved. This is done using a standard called “EXIF”. If you’ve curiously checked the properties of an image, you might’ve run into the exif data.
![EXIF - Wikipedia](https://upload.wikimedia.org/wikipedia/commons/6/6a/Konqueror_Exif_data.jpg) 

It’s not really practical to go through check all the images like this. EXIF data of an image is a well supported machine readable format.

Looking for python libraries to read exif data, I came across [ExifRead](https://pypi.python.org/pypi/ExifRead), which is described as “Easy to use Python module to extract Exif metadata from tiff and jpeg files.” . Just what we’re looking for!

What I wanted now, is a folder full of photos to scrape exif. Luckily for me, MAC OSX saves it in `/Users/srihari/Pictures/Photos Library.photoslibrary/Masters/` since I’ve imported the photos from my phone using the `Photos` app in mac.

With some copy pasting from stackoverflow, I came up with the following snippet that walked through all the neatly arranged folders inside the photos app’s folder : 

```python
#!/usr/bin/env python

from os import walk

mypath = "/Users/srihari/Pictures/Photos Library.photoslibrary/Masters"

for root, subdirs, files in walk(mypath):
    for file in files:
        filepath =join(root, file)
        print "Path to file is", filepath
```
 
Looking at the documentation for ExifRead, there’s an example snippet to read exif tags from photos. Let’s first list all the tags which are available, since we’re not sure how exactly GPS is stored in EXIF.

```python
… 
        imagefile = open(filepath, 'rb')
        tags = exifread.process_file(imagefile)
        for key, value in tags.items()
          print key
…
```

We grep the output of our script for GPS and find out following tags : 

```
GPS GPSLatitudeRef
GPS GPSLatitude
GPS GPSTrack
GPS GPSDOP
GPS GPSSpeed
GPS GPSDate
GPS GPSSpeedRef
Image GPSInfo
GPS GPSTrackRef
GPS GPSTimeStamp
GPS GPSLongitude
GPS GPSLongitudeRef
GPS GPSAltitude
```

That’s a lot of tags, but now that we’ve the exact names, let’s google for the obvious choices.

We land up at this page for [GPSAltitude](http://www.awaresystems.be/imaging/tiff/tifftags/privateifd/gps/gpsaltitude.html) . Looks like we are closer to the information we want. Let’s extract that out : 

```python
        imagefile = open(filepath, 'rb')
        tags = exifread.process_file(imagefile)
        altitude = tags.get('GPS GPSAltitude', '0/1')
        print file, altitude
```

Some photos were missing the altitude information, for such photos, I gave the altitude as ‘0/1’ .

We see that altitude is expressed as a fraction, and the output doesn’t make much sense: 

```
IMG_2350.JPG  113290/137
```

Although I googled for what this fraction might mean, most of results said what the [above link](http://www.awaresystems.be/imaging/tiff/tifftags/privateifd/gps/gpsaltitude.html) said. It’s a fraction, and it’s in meters. 

I opened this photo in my laptop, and checked what my image viewer displays as the altitude.

It showed 826.934m.

Hmm, what’s 113290 divided by 137? that’s 826.934 too.

D’oh!

It’s obvious now,isn’t it?
 
Let’s go ahead and print out altitudes of all the photos in meters: 

```python
        tags = exifread.process_file(imagefile)
        altitude = tags.get('GPS GPSAltitude', '0/1')

        altFraction =  str(altitude).split('/')

        numerator = float(altFraction[0])
        try:
            denominator = float(altFraction[1])
        except IndexError:
            denominator = 1
```

That’s how a ruby programmer writes python :D

We need the try block there, because in some photos, there wasn’t any denominator in the altitude EXIF information.


The next step was to find out the photo at highest altitude.

I was so curious at this point, I didn’t have the patience to complete it in python. I printed out the file name and altitude in a very basic CSV format. And sorted it using excel 

B-)

```python
print filepath,',', num/den
```
```shell
$ python make_exif_csv.py > altexif.csv
$ open altexif.csv
```

And that’s how I found this photo, clicked on my way to Pangong Lake, at 5098.05 m above sea level: 

![Worth it!]({{site.url}}/assets/IMG_2579.JPG)


It’s not just about the firsts, is it?

For science, I updated the program to print out an html page with all the photos ordered by altitude. This also meant I needed to do the sorting without excel sheet. Back to stackoverflow again, and I had this : 

```
#!/usr/bin/env python

from os import walk
from os.path import isfile, join
import exifread
import operator

mypath = "/Users/srihari/Pictures/Photos Library.photoslibrary/Masters"
images = {}

for root, subdirs, files in walk(mypath):
    for file in files:

        filepath =join(root, file)
        imagefile = open(filepath, 'rb')

        tags = exifread.process_file(imagefile)
        alt = tags.get('GPS GPSAltitude', '0/1')
        altS =  str(alt).split('/')

        num = float(altS[0])
        try:
            den = float(altS[1])
        except IndexError:
            den = 1

        images[filepath] = num/den

sorted_images = sorted(images.items(), key=operator.itemgetter(1))

for key, val in sorted_images:
    print "<div class='image'>"
    print "<span>", val , "m</span>"
    print "<img src='", key , "'/>"
    print "</div>"

```

🐍
