# ExposureTimeCheck
Lightroom plugin that calculates a factor using the focal length and the shutter speed for selected photos

The following is taken from https://fbrnc.net/blog/2011/07/lightroom-plugin-detect-blurry-images

Lightroom plugin: Detect blurry images
PHOTOGRAPHY DEVELOPMENT LIGHTROOM


# Problem
When coming home with lots if gigabytes of files after a couple of days of taking lots of pictures (like recent 33. DEKT or some other scouting events) I always wished for a way to pre-filter my images and remove those that are blurry before inspect them in detail.

# Solution
My solution is no rocket science and won't scan the images in order to detect if they're actually blurry (there are some interestig approaches to this), but may still be a useful way to detect candidates of images that might be more blurrier than others.

There's this rule of thumb you might heard of and you may use when deciding what shutter speed to choose before taking a picture:

**shutterSpeed[s] <= 1 / focalLength[mm]**
It says: If your shutter speed (in seconds) is shorter than the inverse of your focal length (in mm) chances are high that your picture won't be blurred because of your shaking hands.

Example: When taking a picture with my telephoto lens at a focal length of 300mm my shutter speed shouldn't be longer than 1/300 sec, taking my camera's crop factor of 1.6 into account it should even be shorter than 1/480 sec.

Besides my 50mm fixed focus lens my other lenses aren't too light sensitive. Accidently (when choosing the wrong aperture) or when taking pictures inside or in the evening I often disobey this rule. (I'd love to see a feature build-in in cameras warning me if I fall below this value.)

Of course this rule of thumb is very imprecise. It doesn't take into account

if you're using a tripod,
if you fired the flash to freeze the object,
if your lens has an image stabilizer and if it is active or
if you have a calm hand
and

if the object is moving too fast,
if you missed the focus point or
if the depth of field doesn't cover your object.
By changing the rule of thumb to following we get a factor that tells us how much we disobeyed the rule of thumb:

**factor = shutterSpeed / (1/focalLength)**
If the factor is less or equal than 1 everything is fine. For example: We took a picture at 50mm with a shutterSpeed of 1/100s. The factor would be 0.5.

**The higher the factor is the more likely it is that the image is blurred. Example: A picture taken at 300mm with 1/100s would have a factor of 3.**

Taking your crop factor, features of your lenses, personal skills, tripods and flashes into account, you could define a personal threshold when to start worrying about the pictures. It seems that images with a factor up to 4 mostly seem ok within my recent images.

Lightroom Plugin "Exposure Time Check"
Out of curiosity I first started to access the Lightroom catalog directly using Sqlite and added all pictures with a factor higher than 4 to a collection using a little php script doing direct sql queries. While this was working quite fine it felt wrong to access Lightroom's database from outside and it wasn't really comfortable. So I digged into how to write Lightroom plugins.

And here it is: My first Lightroom plugin that adds a Metadata field for the picture where to store the calculated factors.



In the menu you'll find an option to calculate the rule of thumb factor for all selected images.



Sadly Lightroom doesn't allow numerical values for custom added metadata fields. I had to use strings as datatype. To make this value filterable (by smart collections) in an easy way I reduced the values by rounding them to integer values to 0 .. 8 and 9+ for everything that is greater or equal than 9.

Then I created two smart collections:

Exposure Time Factor 2 - 5
Exposure Time Factor 6 - 9+
(See download package below for .lrsmcol files read to import) 
I've extended my workflow by calculating the rule of thumb factors and then scanning over these to smart collections removing images that are blurry.

