<script>
import { isTemplateNode } from '@vue/compiler-core';
import SearchResult from './components/SearchResult.vue';
//import DatePick from "vue-date-pick";
//import Datepicker from 'vuejs-datepicker';
//import Datepicker from "vuepic/vue-datepicker";
//import '@vuepic/vue-datepicker/dist/main.css'
//import 'vue-date-pick/dist/vueDatePick.scss';
import $ from 'jquery';
import axios from "axios";
            /*let case_number = document.getElementById("case_number").value;
            let date = document.getElementById("date").value;
            let time = document.getElementById("time").value;
            let code = document.getElementById("code").value;
            let incident = document.getElementById("incident").value;
            let police_grid = document.getElementById("police_grid").value;
            let neighborhood_number = document.getElementById("neighborhood_number").value;
            let block = document.getElementById("block").value;

            var queryString = "?case_number=" + case_number + "&date=" + date + "&time=" + time + "&code=" + code + 
            "&incident=" + incident + "&police_grid=" + police_grid + "&neighborhood_number=" + neighborhood_number + "&block=" + block;
            
            document.forms[0].action = "submit.php" + queryString;
            document.forms[0].submit();
            alert("New incident submitted!");*/
export default {
    data() {
        return {
            view: 'map',
            auth_data: {},
            address_search: '',
            codes: [],
            incidents: [],
            search_results: [],
            incident_results: [],
            marker_counter: [],
            case_number: "",
            date: "",
            time: "",
            code: "",
            incident: "",
            police_grid: "",
            neighborhood_number: "",
            neighborhood_names: [],
            neighborhoods: [1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17],
            block: "",
            leaflet: {
                map: null,
                center: {
                    lat: 44.955139,
                    lng: -93.102222,
                    address: ""
                },
                zoom: 12,
                bounds: {
                    nw: {lat: 45.008206, lng: -93.217977},
                    se: {lat: 44.883658, lng: -92.993787}
                },
                neighborhood_markers: [
                    {location: [44.942068, -93.020521], marker: null}, //.addTo(this.leaflet.map) //onMap: true
                    {location: [44.977413, -93.025156], marker: null},
                    {location: [44.931244, -93.079578], marker: null},
                    {location: [44.956192, -93.060189], marker: null},
                    {location: [44.978883, -93.068163], marker: null},
                    {location: [44.975766, -93.113887], marker: null},
                    {location: [44.959639, -93.121271], marker: null},
                    {location: [44.947700, -93.128505], marker: null},
                    {location: [44.930276, -93.119911], marker: null},
                    {location: [44.982752, -93.147910], marker: null},
                    {location: [44.963631, -93.167548], marker: null},
                    {location: [44.973971, -93.197965], marker: null},
                    {location: [44.949043, -93.178261], marker: null},
                    {location: [44.934848, -93.176736], marker: null},
                    {location: [44.913106, -93.170779], marker: null},
                    {location: [44.937705, -93.136997], marker: null},
                    {location: [44.949203, -93.093739], marker: null}
                ]
            },
            input_placeholder: "Enter a location here",

            filtersT: [ "Homicide", "Assault", "Theft", "PropertyDamage", "Narcotics", "Weapons" ],
            checked_filtersT: [ "Homicide", "Assault", "Theft", "PropertyDamage", "Narcotics", "Weapons" ],
            checked_allT: true,

            filtersN: [ 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17 ],
            checked_filtersN: [ 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17 ],
            checked_allN: true,

            start_date: "2014-01-01",
            end_date: "2022-01-01",

            limit: "1000"
        };
    },
    components: {
        "SearchResult": SearchResult, 
        //"datepicker": Datepicker
        //"date-pick" : DatePick
    },

    methods: {
        viewMap(event) {
            this.view = 'map';
        },

        viewNewIncident(event) {
            this.view = 'new_incident'; 
        },
        check_allT() {
            if(this.checked_allT == false) {
                this.checked_filtersT = [ "Homicide", "Assault", "Theft", "PropertyDamage", "Narcotics", "Weapons" ];
            }
            else {
                this.checked_filtersT = [];
            }
            
        },
        check_allN() {
            if(this.checked_allN == false) {
                this.checked_filtersN = [ 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17 ];
            }
            else {
                this.checked_filtersN = [];
            }
            
        },

        submitForm(){
            alert("Success!")
            const formData = {
                case_number: this.case_number,
                date: this.name,
                time: this.time,
                code: this.code,
                incident: this.incident,
                police_grid: this.police_grid,
                neighborhood_number: this.neighborhood_number,
                block: this.block,
            };
            axios.put("http://localhost:8000/new-incident", formData).then((response) => {
                if (response.status >= 200 && response.status < 300){
                    //Success!
                    console.log("Success");
                }else{
                    //Error
                    console.log("Error submitting request.");
                }
            })
            .catch((error) => {
                console.log(error);
            })
            this.$refs.form.reset(); //deletes all items inputted into the form on the webpage after submission
        },

        deleteForm(){
            var value = document.getElementById("delete").value;
            console.log(value);
            /*let url = 'http://localhost:8000/remove-incident?case_number='+value;

            this.getJSON(url).then((response) => {
                console.log(response);
            }).catch((err) => {
                console.log(err);
            });*/

            $.ajax({
                url: "http://localhost:8000/remove-incident?case_number="+value,
                contentType: 'application/json',
                type: 'DELETE',

                success: function(response) {
                    console.log("IT DELETED!");
                    location.reload();
                },

                error: function(error){
                    console.log(error);
                    console.log("Did not delete");
                }
            });


        },
   /*    const formData = {
                case_number: this.case_number,
                date: this.name,
                time: this.time,
                code: this.code,
                incident: this.incident,
                police_grid: this.police_grid,
                neighborhood_number: this.neighborhood_number,
                block: this.block,
            };
            console.log(this.case_number);
            axios.put("/new-incident", formData).then((response) => {
                if (response.status === 200){
                    //Success!
                    alert("Success!")
                }else{
                    //Error
                    alert("Error submitting new request.")
                }
            })*/

        viewAbout(event) {
            this.view = 'about';
        },

        getJSON(url) {
            return new Promise((resolve, reject) => {
                $.ajax({
                    dataType: 'json',
                    url: url,
                    success: (response) => {
                        resolve(response);
                    },
                    error: (status, message) => {
                        reject({status: status.status, message: status.statusText});
                    }
                });
            });
        },

        uploadJSON(method, url, data) {
            return new Promise((resolve, reject) => {
                $.ajax({
                    type: method,
                    url: url,
                    contentType: 'application/json; charset=utf-8',
                    data: JSON.stringify(data),
                    dataType: 'text',
                    success: (response) => {
                        resolve(response);
                    },
                    error: (status, message) => {
                        reject({status: status.status, message: status.statusText});
                    }
                });
            });
        },

        addressSearch(event) {
        
            if (this.address_search !== '') {
                console.log("Inside address search");
                console.log(this.address_search);
                let req = {
                    url: 'https://nominatim.openstreetmap.org/search?q=' + this.address_search +
                         '&format=json&limit=1&accept-language=en',
                        //https://nominatim.openstreetmap.org/search?q=6738+Emerson+avenue+south,+Minneapolis&format=json&polygon=1&addressdetails=1&limit=1
                    dataType: 'json',
                    headers: {
                        'Authorization': this.auth_data.token_type + ' ' + this.auth_data.access_token
                    },
                    success: this.addressData
                }
                console.log(req.url);
                $.ajax(req);
            }
            else {
                this.search_results = [];
            }
        },
        updateIncidents(){
            let ne = this.leaflet.map.getBounds()._northEast;
            let sw = this.leaflet.map.getBounds()._southWest;
            
            let neighborhood_list = [];
            let i;
            for(i=0; i<this.leaflet.neighborhood_markers.length; i++){
                if(this.leaflet.neighborhood_markers[i].location[0] <= ne.lat && this.leaflet.neighborhood_markers[i].location[0] >= sw.lat && 
                this.leaflet.neighborhood_markers[i].location[1] <= ne.lng && this.leaflet.neighborhood_markers[i].location[1] >= sw.lng){
                    neighborhood_list.push((i+1).toString());

                }
            }
            this.neighborhoods = neighborhood_list;
            console.log("Neighborhoods: " + this.neighborhoods);

            //Also update markers if needed
            this.updateNeighbors();

        },
        addressData(data) {
            this.search_results = data;

            let longitude = data[0].lon;
            let latitude = data[0].lat;

            
            if(longitude != null && latitude != null){
                console.log("Inside");
                
                var myMarker = L.marker([latitude, longitude], {title:'Hover Text',alt:"Marker",clickable:false,draggable:false}).addTo(this.leaflet.map)
                .bindPopup(data[0].display_name);

                this.leaflet.map.flyTo([latitude, longitude], 15);
                //this.updateIncidents();
                
            }

            //console.log(longitude);
        },
        updateNeighbors(){
            console.log(this.incident_results);
            console.log(this.incident_results[0].neighborhood_number)
            this.marker_counter = [0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0];
            var value = 0;
            let i;
            var curr_neighbors = [0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0];

            for(i=0; i<this.neighborhoods.length; i++){
                curr_neighbors[i] = this.neighborhoods[i];
            }

            //console.log(this.neighborhoods);
            //console.log(curr_neighbors);

            for(i=0; i<this.incident_results.length; i++){
                value = this.incident_results[i].neighborhood_number;
                this.marker_counter[value-1] = this.marker_counter[value-1]+1;
            }
            console.log("neighborhoods: " + neighborhoods);
            let url = 'http://localhost:8000/incidents?limit=' + this.limit + '&neighborhood_number='+neighborhoods + this.getFilterForSQL()+ "&start_date=" + this.start_date + "&end_date=" + this.end_date;
            this.getJSON(url).then((response) => {
                console.log(response);
                this.incident_results = response;
            }).catch((err) => {
                console.log(err);
            });

            console.log(this.marker_counter);

        },
        updateForFilters() {
            let url = 'http://localhost:8000/incidents?limit=' + this.limit + this.getNumberForSQL() +  this.getFilterForSQL() + "&start_date=" + this.start_date + "&end_date=" + this.end_date;
            console.log(url);
            this.getJSON(url).then((response) => {
                console.log(response);
                this.incident_results = response;
            }).catch((err) => {
                console.log(err);
            });
        },
        getFilterForSQL() {
            let string = "&filter=";
            let i = 0;
            for(i = 0;i < this.checked_filtersT.length; i++) {
                string = string + this.checked_filtersT[i] + ",";
            }
            string = string.substring(0, string.length - 1);
            console.log(string);
            return string;
        },
        getNumberForSQL() {
            let string = "&neighborhood_number=";
            if(this.checked_filtersN.length == 0) { return string; }
            let i = 0;
            for(i = 0;i < this.checked_filtersN.length; i++) {
                string = string + this.checked_filtersN[i] + ",";
            }
            string = string.substring(0, string.length - 1);
            return string;
        },
        updateNeighbors(){
            //var newMarker = L.marker([44.942068, -93.020521]).addTo(this.leaflet.map);
            //console.log("Inside");
            
            for(let i=0; i<this.leaflet.neighborhood_markers.length; i++){
                this.leaflet.neighborhood_markers[i].marker = L.marker(this.leaflet.neighborhood_markers[i].location, {title:'Hover Text',alt:"Marker",clickable:false,draggable:false,autoClose: false}).addTo(this.leaflet.map).bindPopup("Neighborhood: " + (i+1) + " Crime Count: " + this.marker_counter[i]);
            }
        },
        
    },
    mounted() {
        console.log("neighborhood calcs" + this.neighborhoods);
        this.leaflet.map = L.map('leafletmap').setView([this.leaflet.center.lat, this.leaflet.center.lng], this.leaflet.zoom);
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors',
            minZoom: 11,
            maxZoom: 18
        }).addTo(this.leaflet.map);
        this.leaflet.map.setMaxBounds([[44.883658, -93.217977], [45.008206, -92.993787]]);
        let district_boundary = new L.geoJson();
        district_boundary.addTo(this.leaflet.map);


        this.getJSON('/data/StPaulDistrictCouncil.geojson').then((result) => {
            // St. Paul GeoJSON
            $(result.features).each((key, value) => {
                district_boundary.addData(value);
            });

            /*for(i=0; i<this.neighborhood_markers.length; i++){
                var newMarker = L.marker(this.neighborhood_markers[i]).addTo(this.leaflet.map);
            }*/
        }).catch((error) => {
            console.log('Error:', error);
        });

        //var newMarker = L.marker([45.483658, -93.017977]).addTo(this.leaflet.map);
        console.log(this.neighborhoods);
        this.getJSON('http://localhost:8000/incidents?neighborhood_number=' + this.neighborhoods + '&limit=1000')
        .then((response) => {
            //incidents which are on map
            console.log(response);
            this.incident_results = response;
            //this.updateNeighbors(); //do you want this line

        }).catch((err)=> {
            console.log(err);
        });

        this.getJSON('http://localhost:8000/neighborhoods')
        .then((response) => {
            console.log(response);
            let i;
            for(i=0; i<response.length; i++){
                this.neighborhood_names.push(response[i].neighborhood_name);
            }
            console.log(this.neighborhood_names);

        }).catch((err) => {
            console.log(err);
        })

        this.leaflet.map.on('moveend', this.updateIncidents);

    }
}
</script>

<template>
    <div class="grid-container">
        <div class="grid-x grid-padding-x">
            <p :class="'cell small-4 ' + ((view === 'map') ? 'selected' : 'unselected')" @click="viewMap">Map</p>
            <p :class="'cell small-4 ' + ((view === 'new_incident') ? 'selected' : 'unselected')" @click="viewNewIncident">New Incident</p>
            <p :class="'cell small-4 ' + ((view === 'about') ? 'selected' : 'unselected')" @click="viewAbout">About</p>
        </div>
    </div>
    <div v-show="view === 'map'">
        <!--<input id="search" type="text" v-model="spotify_search" :placeholder="input_placeholder" />
        <select id="type" v-model="spotify_type">
            <option v-for="opt in spotify_type_options" :value="opt.value">{{ opt.text }}</option>
        </select>
        <button type="button" @click="spotifySearch">Search</button>-->


        <div class="grid-container">
            <input id="search" type="text" v-model="address_search" :placeholder="input_placeholder" />
            <button class="blue" type="button" @click="addressSearch">Go</button>

            <div class="grid-x grid-padding-x">
                <div id="leafletmap" class="cell auto"></div>
                
            </div>
            <div  id="filtersT" class="cell auto">
                <span>Crime Types: </span>
                <span v-for="filter in filtersT"> 
                    <input type="checkbox" v-bind:id="filter" v-bind:value="filter" v-model="checked_filtersT"/> 
                    <label>{{filter}}</label>
                </span>
                <span><input type ="checkbox" @click='check_allT()' v-bind:value="checked_allT" v-model="checked_allT"/><label>Check All</label></span>
            </div>
            <div  id="filtersN" class="cell auto">
                <span>Neighborhood Numbers: </span>
                <span v-for="filter in filtersN"> 
                    <input type="checkbox" v-bind:id="filter" v-bind:value="filter" v-model="checked_filtersN"/> 
                    <label>{{filter}}</label>
                </span>
                <span><input type ="checkbox" @click='check_allN()' v-bind:value="checked_allN" v-model="checked_allN"/><label>Check All</label></span>
            </div>
            <div>
                <span><label>Please enter a start date </label>
                <input type="text" v-model="start_date"></span>
                <span><label>Please enter an end date </label>
                <input type="text" v-model="end_date"></span>
            </div>
            <div>
                <label>Please enter an upper limit for the number of results: </label>
                <input type="text" v-model="limit">
                <button class="blue" type="button" @click="updateForFilters()">Search with filters</button>
            </div>

            <SearchResult :result_array="incident_results" :neighborhoods="neighborhoods" :neighborhood_names="neighborhood_names" />
        </div>
    </div>
    <div v-if="view === 'new_incident'">
        <!-- Replace this with your actual form: can be done here or by making a new component -->
        <div class="grid-container">
            <div class="grid-x grid-padding-x">
                <h1 class="cell auto center" style="font-family:fantasy">New Incident Form</h1>
            </div>
            <br/>
                <form>
                    <label for="case_number">Case Number:</label><br>
                    <input type="text" placeholder="Ex: 123456" id="case_number" name="case_number" required>
                    <label for="date">Date:</label><br>
                    <input type="text" placeholder="Ex: 2019-04-26" id="date" name="date" required>
                    <label for="time">Time:</label><br>
                    <input type="text" placeholder="Ex: 19:15:00" id="time" name="time" required>
                    <label for="code">Code:</label><br>
                    <input type="text" placeholder="Ex: 600" id="code" name="code" required>
                    <label for="incident">Incident:</label><br>
                    <input type="text" placeholder="Ex: theft" id="incident" name="incident" required>
                    <label for="police_grid">Police Grid:</label><br>
                    <input type="text" placeholder="Ex: 49" id="police_grid" name="police_grid" required>
                    <label for="neighborhood_number">Neighborhood Number:</label><br>
                    <input type="text" placeholder="Ex: 1" id="neighborhood_number" name="neighborhood_number" required>
                    <label for="block">Block:</label><br>
                    <input type="text" placeholder="Ex: 212OLDHUDSONRD" id="block" name="block" required>
                    <button type="button" v-on:click="submitForm()">Submit</button>
                </form>

            <div class="grid-x grid-padding-x">
                <h1 class="cell auto center" style="font-family:fantasy">Delete Incident Form</h1>
            </div>
            <br/>

            <label for="date">Incident Number:</label><br>

            <form>
                <input type="text" placeholder="Type here" id="delete" name="delete" required>
                <button type="button" v-on:click="deleteForm()">Submit</button>
            </form>
            <br/>
            <br/>

        </div>
    </div>
    <div v-if="view === 'about'">
        <!-- Replace this with your actual about the project content: can be done here or by making a new component -->
        <div class="grid-container">
            <div class="grid-x grid-padding-x">
                <h1 class="cell auto center" style="font-family:fantasy">About the Project</h1>
            </div>
            <br/>
        </div>

        <div class="grid-container names rcor">

            <h1 class="cell auto center"> Who are we?</h1>
            <div class="grid-x grid-padding-x">

                <div class="cell small-12 large-6 center"><img src ="../images/picofjoe.jpg" class="floatleft rcor" alt="Picture of Joseph Schoen"/></div> 
                <div class="cell center small-12 large-6 ">
                    <h1>Name:</h1>
                    <p>My name is Joseph Schoen, and I am attending the University of St. Thomas as a senior.</p>
                    <br/>
                    <h1>Studies:</h1>
                    <p>I am studying Computer Science for my major, and Applied Statistics for my minor.</p>
                    <br/>
                    <h1>Goals After College:</h1>
                    <p>I am currently enrolled into Optums TDP program, and hope to work in their Information Securities department.</p>
                </div>
            </div>
            <br/>


            <div class="grid-x grid-padding-x">
                <div class="cell small-12 large-6 center"><img src ="../images/picofhayden.jpg" class="floatleft rcor" alt="Picture of Joseph Schoen"/></div> 
                <div class="cell small-12 large-6 center ">
                <h1>Name:</h1>
                    <p>My name is Hayden Richards, and I am a junior at the University of St. Thomas!</p>
                    <br/>
                    <h1>Studies:</h1>
                    <p>I am majoring in Computer Science, while double minoring in Teacher Education and Applied Statistics.</p>
                    <h1>Goals After College:</h1>
                    <p>My main goal as of now is to graduate with a Bachelor's in Computer Science! I am currently undecided in what I want
                        to pursue as far as a career in the computer science field. However, game development would be a dream job of mine.
                    </p>
                </div>
            </div>
            <br/>

            <div class="grid-x grid-padding-x">
                <div class="cell small-12 large-6 center"><img src ="../images/picofaudrey.jpg" class="floatleft rcor" alt="Picture of Joseph Schoen"/></div> 
                <div class="cell center small-12 large-6 ">
                    <h1>Name:</h1>
                    <p>My name is Audrey Jenkins</p>
                    <br/>
                    <h1>Studies:</h1>
                    <p>I am majoring in computer science and minoring in sustainability and statistics</p>
                    <h1>Goals After College:</h1>
                    <p>Once I graduate in the spring, I hope to look for work as a software engineer. I do not have a specific field of work in mind, but game design is interesting to me</p>

                </div>
            </div>
            <br/>

            <div class="grid-x grid-padding-x">
                <div class="cell small-12 large-6 center"><img src ="../images/picoftara.jpeg" class="floatleft rcor" alt="Picture of Tara Sothy"/></div> 
                <div class="cell center small-12 large-6 ">
                    <h1>Name:</h1>
                    <p>My name is Tara Sothy</p>
                    <br/>
                    <h1>Studies:</h1>
                    <p>I am majoring in Computer Science, while double minoring in both Data Analytics and Applied Statistics.</p>
                    <h1>Goals After College:</h1>
                    <p>Once I graduate (in just a few days here) I'll soon start a job as an full stack associate software developer in the Twin Cities!
                        I've wanted to work as a full stack software developer for a while now, so I'm excitied to be able to have this opportunity.
                        I'm thinking I'd like to live abroad for a few years but I have yet to decide when and where but I expect that to be on the horizon for me.
                    </p>
                </div>
            </div>
            <br/>

        </div>

        <div class="grid-container">
            <br/>
            <br/>
            <div class="grid-x grid-padding-x">
                <h1 class="cell large-12 center">Tools Used</h1>

                <div class="cell small-12 large-6 center">
                    <p><strong>Nominatim API</strong>
                        <br/>
                        By using the Nominatim API, this gives us the capability to make a search for a location, and taking
                        that search information, placing it into a URL, and then getting an actual longitude and latitude to use.
                    </p>
                </div>
                <div class="cell small-12 large-6 center">
                    <p><strong>Leaflet API</strong>
                        <br/>
                        Using the Leaflet API, this gave us the capability to create an interactive map. This map was heavily used
                        for displaying a visual representation of the different neighborhoods, showing the location that was searched
                        by marking it, and also visually chosing an area on the map to see the crime cases in that location/neighborhoods.
                    </p>
                </div>

                <div class="cell small-12 large-6 center">
                    <p><strong>St. Paul Crime API</strong>
                        <br/>
                        This API was created by us through a St. Paul Crime dataset. This dataset includes all the crime's documented through
                        the city of St. Paul from August 2014, to May 2022. With 420,162 crime entries documented, this contains lots of information!
                        We used this API to display the crimes commited in an area, by using the search features from Nominatim and the live map frame!
                    </p>
                </div>

                <div class="cell small-12 large-6 center">
                    <p><strong>Foundation Framework</strong>
                        <br/>
                        This framework was used for stying the visual aspect of the website. This is focused in on the HTML side of the website.
                        It allows for a custom grid to work with when organizing objects on a website. That way, whether on a phone, tablet, desktop,
                        or some other device, it is custom to the size of the screen to make it well sorted for the user to read.
                    </p>
                
                </div>
            </div>

            
            <br/>
            <br/>
        </div>

        <div class="grid-container video rcor">

            <div class="grid-x grid-padding-x">
                <h1 class="cell auto center">Video Demo</h1>
            </div>
        </div>


        <div class="grid-container">
            <br/>
            <br/>
            <div class="grid-x grid-padding-x">
                <h1 class="cell small-12 large-12 center">Six Interesting Things</h1>

                <div class="cell small-12 large-6 center">
                    <p>The first thing</p>
                </div>
                <div class="cell small-12 large-6 center">
                    <p>The second thing</p>
                </div>
                <div class="cell small-12 large-6 center">
                    <p>The third thing</p>
                </div>
                <div class="cell small-12 large-6 center">
                    <p>The fourth thing</p>
                </div>
                <div class="cell small-12 large-6 center">
                    <p>The fifth thing</p>
                </div>
                <div class="cell small-12 large-6 center">
                    <p>The sixth thing</p>
                </div>
            </div>
            <br/>
        </div>

        <div class="grid-container names rcor">
            <div class="grid-x grid-padding-x">
                <h1 class="cell auto center">Thanks!</h1>
                <div class="cell small-12 large-12 center"><img src ="images/family.png" class="rcor" alt="Picture of Joseph Schoen"/></div>
            </div>
            <br/>
        </div>
        <br/>

    </div>

    
</template>

<style>
#leafletmap {
    height: 600px;
}
.selected {
    background-color: rgb(10, 100, 126);
    border-radius: 20px;
    color: white;
    border: solid 1px white;
    text-align: center;
    cursor: pointer;
}
.unselected {
    background-color: rgb(200, 200, 200);
    border-radius: 20px;
    color: black;
    border: solid 1px white;
    text-align: center;
    cursor: pointer;
}
</style>

 