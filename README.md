# leaflet-challenge
## Map Portion of the Assignment
### TileLayer loads without error (20 points)
    - Create a function to hold the TileLayer and populate the map when it was called

            / Function to create the baseMaps and overlayMaps
            function createMap(earthquakes) {
            // Create the base layer
            let street = L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
                attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
                });
            
            // Create a baseMap object.
                let baseMaps = {
                "Street Map": street
                };

            // Create an overlay to hold the earthquakes overlay
                let overlayMaps = {
                "Earthquakes": earthquakes
                };

            // Creating the map
            let myMap = L.map("map", {
                center: [32.97180377635759, -110.89555953349418],
                zoom: 5,
                layers: [street, earthquakes]
                });

            // Create a layer control and pass it baseMaps and overlayMaps
            L.control.layers(baseMaps, overlayMaps, {
                collapsed: false
            }).addTo(myMap);

    - **Code for this portion of the assignment was taken from activities performed in class**

### Connects to geojson API using D3 without error (20 points)
    - Created a function to loop through the json file pulling out all the earthquake data that I found
    - Here's what part of the code looked like for this

            // Function that will create our markers based on the data
            function createMarkers(response) {
            // Pull the earthquake data
            let earthquakeData = response.features

            // Create an array to hold earthquake data
            let quakeMarkers = [];

            // Loop through the earthquakeData array
            for (let index = 0; index < earthquakeData.length; index++) {
                let earthquake = earthquakeData[index];

    - Then I performed the API call at the end of my code and referenced the functions I had created earlier

            / Perform an API call to the Citi Bike API to get the station information. Call createMarkers when it completes.
            d3.json("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/2.5_month.geojson").then(createMarkers);

    - **This was directly taken from the examples we performed in class**

### Markers with size corresponding to earthquake magnitude (10 points)
    - To start I created a function for the marker size that multiplied the results so we could see a significant difference

            // Function to create the marker size
            function markerSize(magnitude) {
            return Math.pow(2, magnitude) * 3000;
            };

    - Then within my function to loop through the json file I referenced the function making sure to use the magnitude of the earthquake with my markerSize function

                // For each earthquake create a circle marker with the color based on the depth and
                // The radius of the cirle based on the magnitude
                // Also bind a popup with the earthquake data
                let quakeMarker = L.circle([earthquake.geometry.coordinates[1], earthquake.geometry.coordinates[0]], {
                fillOpacity: 1, 
                color: "black", 
                weight: .5,
                fillColor: markerColor(earthquake.geometry.coordinates[2]), 
                radius: markerSize(earthquake.properties.mag)
                })
                .bindPopup("<h3>Magnitude: " + earthquake.properties.mag + "<h3><h3>Location: " + earthquake.properties.place + "<h3><h3>Depth: " + earthquake.geometry.coordinates[2] + "</h3>");

                // Add the marker to the bikeMarkers array
                quakeMarkers.push(quakeMarker);
            }

            //Create a layer group that's made from the quakeMarkers array and pass it through the createMap function
            createMap(L.layerGroup(quakeMarkers));

    - **This code was also taken from activities we performed in class. This was very similar to class examples.**

### A legend showing the depth and their corresponding color (10 points)
### Data points scale with magnitude level (10 points)
    - I created a legend that showed what color corresponded to what depth range
    - The way specific way I added the legend required adding code to the CSS file to make the legend populate

            / Code to create my legend that I would insert on the lower right hand side of the page
            let legend = L.control({position: "bottomright"});
            legend.onAdd = function() {
                let div = L.DomUtil.create("div", "info legend");
                // Code to create the info of my legend and assign color squares to match what was showing in the example
                let legendInfo = `
                <div class="legend">
                    <h4>Depth Legend</h4>
                    <div class="legend-item"><span class="legend-square" style="background-color: forestgreen;"></span>-10 to 10</div>
                    <div class="legend-item"><span class="legend-square" style="background-color: greenyellow;"></span>10 to 30</div>
                    <div class="legend-item"><span class="legend-square" style="background-color: yellow;"></span>30 to 50</div>
                    <div class="legend-item"><span class="legend-square" style="background-color: orange;"></span>50 to 70</div>
                    <div class="legend-item"><span class="legend-square" style="background-color: darkorange;"></span>70 to 90</div>
                    <div class="legend-item"><span class="legend-square" style="background-color: red;"></span>90+</div>
                </div>`;

                div.innerHTML = legendInfo;
                return div;
            }
            
            // Add Legend to my map
            legend.addTo(myMap);
            };

    - **I got a lot of the code from the example we went over in class. However, the specific code assigning the legend-square a color and adding the CSS was found with help from chatGPT. I was having trouble figuring out how to create the legend to match the example. ChatGPT helped clean up my code with the colors and fix the issues by editing the CSS code.**

### Data points colors change with depth level (10 points)
    - Created a function to set a different color depending on the depth

            // Function to create the color
            function markerColor(depth) {
            if (depth >= -10 && depth <= 10) return "forestgreen";
            else if (depth > 10 && depth <= 30) return "greenyellow";
            else if (depth > 30 && depth <= 50) return "yellow";
            else if (depth > 50 && depth <= 70) return "orange";
            else if (depth > 70 && depth <= 90) return "darkorange";
            else if (depth > 90) return "red";
            else return "black";

    - Later referenced the color when I was creating the markers and plugged the depth into the markerColor function to get the right color
    - **Code was taken from activities performed in class. I used the following site to get a list of colors to call https://www.w3.org/TR/css-color-3/#svg-color**

### Each point has a tooltip with the Magnitude, the location and depth (10 points)
    - This was done using the bindPopup method when I was looping through the data
    - Pulled the desired information from the json file for each earthquake in my desired format

            .bindPopup("<h3>Magnitude: " + earthquake.properties.mag + "<h3><h3>Location: " + earthquake.properties.place + "<h3><h3>Depth: " + earthquake.geometry.coordinates[2] + "</h3>");

    - **Code for this was taken from activites performed in class. 

### All data points load in the correct locations (10 points)
    - All data points should load into the code as you change out the url