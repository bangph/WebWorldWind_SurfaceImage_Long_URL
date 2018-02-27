# WebWorldWind_SurfaceImage_Long_URL
When URL is too long (e.g: POST request), it needs more tricks for displaying response image to WebWorldWind
Cannot use normal JQuery Ajax POST but need to use var xhr = new XMLHttpRequest(); and parse the result as BLOB, then create a Image object 
and set it as imageSource of WebWorldWind surface image.

        var query = 'for c in (world_map_new) return encode(clip(c, $WKT), "png")';
                var objWKT = getWKT(wktId);

                var coverage = "world_map_new";
                var wcsRequest = wcsRequestTemplate.replace("$COVERAGE", coverage);

                // e.g: texas
                var wktId = $("#wktList").val();
                var objWKT = getWKT(wktId);
                wcsRequest = wcsRequest.replace("$WKT", objWKT["wkt"]);
		        var params = "query=" + query.replace("$WKT", objWKT["wkt"]);
                
                // Send POST request to server and look at the center of the raster image on globe
                var xhr = new XMLHttpRequest();
                xhr.responseType = 'arraybuffer';
                xhr.onload = function() {
                    var blb = new Blob([xhr.response], {type: 'image/png'});
                    var url = (window.URL || window.webkitURL).createObjectURL(blb);
                    // NOTE: must set the image properly from source which is blob by xhr, JQuery ajax cannot do it (!!)
                    var image = new Image();
                    image.src = url;
                    var minLat = objWKT["lower_left"][0];
                    var maxLat = objWKT["upper_right"][0];
                    var minLong = objWKT["lower_left"][1];
                    var maxLong = objWKT["upper_right"][1];
                    var centerLat = (minLat + maxLat) / 2;
                    var centerLong = (minLong + maxLong) / 2;          

                    var surfaceImage = new WorldWind.SurfaceImage(new WorldWind.Sector(minLat, maxLat, minLong, maxLong), new WorldWind.ImageSource(image));
                    var surfaceImageLayer = new WorldWind.RenderableLayer();
                    surfaceImageLayer.displayName = "Surface Images";
                    surfaceImageLayer.addRenderable(surfaceImage);
                    wwd.addLayer(surfaceImageLayer);

                    wwd.navigator.lookAtLocation = new WorldWind.Location(centerLat, centerLong);
                    wwd.redraw();
                }                

                xhr.open('POST', wcsRequest);
                xhr.send(params);      
