# WebWorldWind_SurfaceImage_Long_URL
When URL is too long (e.g: POST request), it needs more tricks for displaying response image to WebWorldWind
Cannot use normal JQuery Ajax POST but need to use var xhr = new XMLHttpRequest(); and parse the result as BLOB, then create a Image object 
and set it as imageSource of WebWorldWind surface image.
