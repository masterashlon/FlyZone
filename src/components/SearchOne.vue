<template>
    <div class="search-container">
        <form autocomplete="off" @submit.prevent="searchPressed($event)" spellcheck="false" id="form">
            <div class="flight-type-choice">
                <div class="first-choice">
                    <input type="radio" name="trip" id="roundtrip" checked @click="yesback">
                    <label for="roundtrip">Round-trip</label>
                </div>
                <div class="second-choice">
                    <input type="radio" name="trip" id="oneway" @click="noback">
                    <label for="oneway">One-way</label>
                </div>
            </div>
            <div class="destination-choice">
                <label for="start-location" class="label-start">Where from   <span id="span" @click="findAirports(undefined, $event)">Near me</span></label>
                <input type="text" v-model.trim="start" autofocus @input="findAirports(start, $event)" placeholder="City, location, airport to start" id="start-location" required />
                <div id="airports-nearby"></div>
                <a class="button-exchange" @click="exchangeLocation"><img src="../assets/exchange.png" alt="exchange"/></a>
                <label for="arrive-location" class="label-arrive">For where </label>
                <input type="text" v-model.trim="arrive" @input="findAirports(arrive, $event)" placeholder="City, location, airport to arrive" id="arrive-location" required />
            </div>
            <div class="date-choice">
                <label for="start-date" class="label-date-start">Start date</label>
                <input type="date" id="start-date" v-model="startDate" required />
                <label for="return-date" class="label-date-return">Return date</label>
                <input type="date" id="return-date"  v-model="returnDate" required/>
            </div>
            <div class="other-choice">
                <label for="travelers" class="label-travelers">Travelers number</label>
                <select name="travelers" id="travelers" v-model="travelers">
                    <option value="1adult">1 adult</option>
                    <option value="2adults">2 adults</option>
                    <option value="2adults 1child">2 adults 1 child</option>
                    <option value="1adult 1child">1 adult 1 child</option>
                    <option value="2adults 2children">2 adults 2 children</option>
                </select>
                <label for="cabinclass" class="label-cabinclass">Cabin class</label>
                <select name="cabinclass" id="cabinclass" v-model="cabinClass">
                    <option value="economy">Economy-class</option>
                    <option value="premium">Premium-class</option>
                    <option value="business">Business-class</option>
                    <option value="first">First-class</option>
                </select>
            </div>
            <input type="submit"  value="Search" class="search-button" id="submit">
        </form>
    </div>
</template>
   
   <script>
        export default {
            components: { },  
            data()  {
                return {
                    startDate: this.todayDate(),
                    returnDate: this.tomorrowDate(),
                    AIRPORT_LIST_URL: "https://raw.githubusercontent.com/jpatokal/openflights/master/data/airports.dat",
                    AIR_API_URL: `http://api.aviationstack.com/v1/flights?access_key=null&limit=20&flight_status=active`,
                    start: "",
                    arrive: "",
                    travelers: "1adult",
                    cabinClass: "economy",
                    iataStart: "",
                    iataReturn: ""
                }   
            },
            methods: {
                searchPressed(event) {

                    let startFlights = undefined;
                    let returnFlights = undefined;
                    this.findAirports(undefined, event);
                    startFlights = this.searchFlight(this.startDate, this.iataStart, this.iataReturn)
                    if(!document.getElementById("return-date").disabled) {
                        returnFlights = this.searchFlight(this.returnDate, this.iataReturn, this.iataStart)
                        this.createFlightList(startFlights, returnFlights)
                    }
                    this.createFlightList(startFlights)
                    //this.findAirports(undefined, event);
                    //console.log(this.arrayAirports);
                },
                todayDate() {
                    //return today date in this form year-month-day
                    let today = new Date();
                    return `${today.getFullYear()}-${(today.getMonth() + 1).toString().length == 1 ? "0" : ""}${today.getMonth() + 1}-${today.getDate().toString().length === 1 ? "0" : ""}${today.getDate()}`;
                },
                tomorrowDate() {
                    //return tomorrow date in this form year-month-day
                    let today = new Date();
                    let tomorrow = new Date();
                    tomorrow.setDate(today.getDate() + 1);
                    return `${tomorrow.getFullYear()}-${(tomorrow.getMonth() + 1).toString().length == 1 ? "0" : ""}${tomorrow.getMonth() + 1}-${tomorrow.getDate().toString().length === 1 ? "0" : ""}${tomorrow.getDate()}`; 
                },
                yesback() {
                    document.querySelector("#return-date").disabled = false;
                    document.querySelector(".search-container").classList.remove("other-image");
                    document.querySelector(".label-date-return").style.opacity = "1";
                    document.querySelector("#return-date").style.opacity = "1";
                },
                noback() {
                    document.querySelector("#return-date").disabled = true;
                    document.querySelector(".search-container").classList.add("other-image");
                    document.querySelector(".label-date-return").style.opacity = ".4";
                    document.querySelector("#return-date").style.opacity = ".4";
                },
                
                exchangeLocation() {
                    let exchange = "";
                    const startLocation = document.getElementById("start-location");
                    const arriveLocation = document.getElementById("arrive-location");
                    exchange = startLocation.value;
                    this.arrive = startLocation.value;
                    //console.log(exchange); 
                    startLocation.value = arriveLocation.value;
                    this.start = arriveLocation.value;
                    arriveLocation.value = exchange;
                },
                createFlightList(startFlight, returnFlight = undefined){
                    startFlight, returnFlight;
                    //console.log(startFlight, returnFlight);
                },
                createAirportList(listAirport, event) {
                    const firstQuery = window.matchMedia("(max-width: 1200px)");
                    const secondQuery = window.matchMedia("(max-width: 600px)");
                    const idTarget = event.target.id == "span"? "start-location" : event.target.id ;
                    const div = document.querySelector("#airports-nearby");
                    div.innerHTML = listAirport;
                    //so its below every input target
                    if(idTarget == "arrive-location") {
                        if(secondQuery.matches){ 
                            div.style.top = "155px"; 
                        }
                        else if(firstQuery.matches) {   
                            div.style.left = "10px"; 
                            div.style.top = "180px"; 
                        } else  {  
                            div.style.left = "455px"
                            div.style.top = "55px"; 
                        }

                    } else {
                        if(secondQuery.matches){ 
                            div.style.top = "80px"; 
                        }
                        else if(firstQuery.matches) {   
                            div.style.left = "10px"; 
                            div.style.top = "90px"; 
                        } else  {  
                            div.style.left = "10px"
                            div.style.top = "50px"; 
                        }
                    }
                    // const divAppear = () => {
                    //     window.setTimeout(() => {
                    //         div.style.opacity= "1";
                    //         document.querySelector(".label-start span").innerText = "Near me";
                    //     }, 300);
                    // }
                    // const browser = this.browserCheck().toLowerCase(); //save the browser's name
                    div.style.display= "block";
                    window.setTimeout(() => {
                        div.style.opacity= "1";
                        document.querySelector(".label-start span").innerText = "Near me";
                    }, 50);
                    // avoid a bug in not-firefox browser where timingevent are skipped the second time
                    // if(browser.includes("firefox"))     divAppear(); 
                    // else {
                    //     div.style.opacity= "1";
                    //     document.querySelector(".label-start span").innerText = "Near me";
                    // }
                    document.addEventListener("click", (e) => {
                        if(e.target !== div) {
                            //div.style.display = "none";
                            div.style.opacity= "0";
                            //if(browser.includes("firefox"))     window.setTimeout(() => div.style.display= "none", 300);
                            //else div.style.display= "none";
                        }
                    }, false)
                    div.addEventListener("transitionend", (e) => { 
                        if(e.target !== div) 
                            div.style.display = "none"
                    }, false)
                    
                    const pArray = document.querySelectorAll("#airports-nearby p");
                    const h2Array = document.querySelectorAll("#airports-nearby h2");
                    const smallArray = document.querySelectorAll("#airports-nearby small");
                    smallArray.forEach(small => small.style.display = "none");
                    pArray.forEach(p => {
                        p.style.color = "lightgray";
                        p.style.paddingBottom = "10px";
                    });
                    h2Array.forEach(h2 => {
                        h2.style.color = "lightgray";
                        h2.addEventListener("mouseover", () => {
                            h2.style.color= "white";
                            h2.style.cursor = "pointer";
                        }
                        ,false);
                        h2.addEventListener("mouseout", () => h2.style.color= "lightgray",false);
                        h2.addEventListener("click", () => {
                            if(idTarget == "arrive-location") {
                                document.getElementById(idTarget).value = h2.innerText;
                                this.arrive = h2.innerText;
                            } else {
                                document.getElementById(idTarget).value = h2.innerText;
                                this.start = h2.innerText;
                            }
                            if(document.getElementById("start-location").value == document.getElementById("arrive-location").value) {
                                document.getElementById(idTarget).value = "";
                                event.target.id == "start-location"? this.start = "": this.arrive = ""; 
                            }
                            div.style.opacity = "0";
                            div.style.display = "none";
                        },false);

                    });

                },
                async findAirports(city = undefined, event) {
                    //link in a webserver with all airports in the world updated constantly 
                    const response = await fetch(this.AIRPORT_LIST_URL);
                    //console.log(response);
                    //console.log(event);
                    const data = await response.text();
                    const text = data.toString();
                    let array = text.split("\n");
                    array = array.map(x => x.replace(/"/g, ""));
                    const arrayAirports = array.map(x => x.split(",")); 
                    //console.log(typeof city);
                    //console.log(event.target.id);
                    if(event.target.id == "start-location" || event.target.id == "arrive-location")    this.inputPos(arrayAirports, city, event);
                    else if(event.target.id == "form") this.submitPos(arrayAirports);
                    
                    else this.gpsPos(arrayAirports, event);
                    //send to functions a 2d array in this form for every element inside: [id, name airport, city, country, iata, icao, lat, long, alt, timezone, dst, tzdb timezone, type, source]     
                },
                gpsPos(arrayAirports, event) {
                    const success = pos => { 
                        const latitude = pos.coords.latitude; 
                        const longitude = pos.coords.longitude;
                        let  c = 0;
                        let innerHtmlSpan = "";
                        for (let i = 0; i < arrayAirports.length; i++) {
                            let airport = arrayAirports[i][1];
                            if(!airport) continue; //skip airport with no name
                            airport = airport.toLowerCase();
                            if(airport.includes("private") || airport.includes("air base") || airport.includes("navy") || airport.includes("army") || airport.includes("air force") || airport.includes("heliport") || airport.includes("idroport") || airport.includes("airfield") || airport.includes("naval") ) { //skip military, private, heliport etc..
                                continue;
                            }
                            //if this location is within 100KM of the gps or ip position, add it to the span above input text
                            if ( this.distance(latitude, longitude, arrayAirports[i][6], arrayAirports[i][7], "K") <= 40) {
                                innerHtmlSpan += `<h2>${arrayAirports[i][1]}</h2>  <p> Distance: ${Math.round(this.distance(latitude, longitude, arrayAirports[i][6], arrayAirports[i][7], "K"))}km , ${arrayAirports[i][2]} , ${arrayAirports[i][3]} </p><small>${arrayAirports[i][4]}, ${arrayAirports[i][5]}</small>`;
                                c++;
                            }
                        }
                        if(c == 0)  document.querySelector(".label-start span").innerText = "No airports near you";
                        else {
                            this.createAirportList(innerHtmlSpan, event);                               
                        }
                    
                
                    }

                    document.querySelector(".label-start span").innerText = "One moment";
                    navigator.geolocation.getCurrentPosition(success, () => {
                        document.querySelector(".label-start span").innerText = "Search position error";
                    });
                },
                distance(lat1, lon1, lat2, lon2, unit)  {
                    //this function calculate the distance between two position with latitude and longitude
                    const radlat1 = Math.PI * lat1/180
                    const radlat2 = Math.PI * lat2/180
                    const theta = lon1-lon2
                    const radtheta = Math.PI * theta/180
                    let dist = Math.sin(radlat1) * Math.sin(radlat2) + Math.cos(radlat1) * Math.cos(radlat2) * Math.cos(radtheta);
                    if (dist > 1) {
                        dist = 1;
                    }
                    dist = Math.acos(dist)
                    dist = dist * 180/Math.PI
                    dist = dist * 60 * 1.1515
                    if (unit=="K") { dist = dist * 1.609344 }
                    if (unit=="N") { dist = dist * 0.8684 }
                    return dist
                },
                inputPos(arrayAirports, city, event)  {
                    if(city.length > 3) {
                            
                            //city = city.trim();
                            //console.log(city);
                            let  c = 0;
                            let innerHtmlSpan = "";
                            for (let i = 0; i < arrayAirports.length; i++) {
                                let airport = arrayAirports[i][1];
                                if(!airport) continue; //skip airport with no name
                                airport = airport.toLowerCase();
                                if(airport.includes("private") || airport.includes("air base") || airport.includes("navy") || airport.includes("army") || airport.includes("air force") || airport.includes("heliport") || airport.includes("idroport") || airport.includes("airfield") || airport.includes("naval") ) 
                                    continue; //skip military, private, heliport etc..
                                //user input control in cities and airports' name
                                if(arrayAirports[i][2].toLowerCase().includes(city.toLowerCase()) || airport.toLowerCase().includes(city.toLowerCase())){
                                    innerHtmlSpan += `<h2>${arrayAirports[i][1]}</h2>  <p> ${arrayAirports[i][2]} , ${arrayAirports[i][3]} </p><small>${arrayAirports[i][4]}, ${arrayAirports[i][5]}</small>`;
                                    c++;
                                }
                            }
                            if(c == 0) { 
                                innerHtmlSpan = "<p>No corresponding airport</p>";
                                this.createAirportList(innerHtmlSpan, event);
                            } else {
                                this.createAirportList(innerHtmlSpan, event);                               
                            }
                        }
                },
                submitPos(arrayAirports) {
                            
                    //city = city.trim();
                    //console.log(city);
                    for(let i = 0; i < arrayAirports.length ; i++){
                        if(arrayAirports[i][1] === this.start) {
                            this.iataStart = arrayAirports[i][4];
                        }
                        if(arrayAirports[i][1] === this.return) {
                            this.iataReturn = arrayAirports[i][4];
                        }
                    }
                    
                    
                },
                async searchFlight(date, startFlight, returnFlight){
                        const response = await fetch(`${this.AIR_API_UR}&dep_ita=${startFlight}&arr_iata=${returnFlight}` );
                        console.log(response);
                        const data = await response.json();
                        this.returnJsonFlights(data);

                },
                returnJsonFlights(data) {
                    this.jsonFlights = data;
                },  
                browserCheck() { //check browser's name
                    navigator.sayswho= (function(){
                        const ua= navigator.userAgent;
                        let tem; 
                        let M= ua.match(/(opera|chrome|safari|firefox|msie|trident(?=\/))\/?\s*(\d+)/i) || [];
                        if(/trident/i.test(M[1])){
                            tem=  /\brv[ :]+(\d+)/g.exec(ua) || [];
                            return 'IE '+(tem[1] || '');
                        }
                        if(M[1]=== 'Chrome'){
                            tem= ua.match(/\b(OPR|Edge)\/(\d+)/);
                            if(tem!= null) return tem.slice(1).join(' ').replace('OPR', 'Opera');
                        }
                        M= M[2]? [M[1], M[2]]: [navigator.appName, navigator.appVersion, '-?'];
                        if((tem= ua.match(/version\/(\d+)/i))!= null) M.splice(1, 1, tem[1]);
                        return M.join(' ');
                    })();

                    return navigator.sayswho;
                }
            },
            mounted() { }

        }
   </script>
   
<style scoped>
     .search-container {
        background: url("../assets/search-background1.webp");
        background-size:cover;
        background-position: center;
        background-repeat: no-repeat;
        transition: background 750ms ease-out;
        width: 100%;
        height: max-content;
        padding: 5rem;
        display: grid;
        place-items: center;
        position: relative;
        transition: 500ms all;
     }
     .other-image {
        background: url("../assets/search-background2.webp");
        background-size:cover;
        background-position: center;
        background-repeat: no-repeat;
     }

     form {
        max-width: 80rem;
        background: var(--black-trasparent);
        border-radius: 20px;
        display: flex;
        flex-direction: column;
        align-items: center;
        padding: 5rem;
        transition: 500ms all;
     }

     .flight-type-choice {
        display: flex;
        justify-content: flex-end;
        gap: 4rem;
     }

    
     .first-choice,
     .second-choice {
        font-size: 1.6rem;
        display: flex;
        gap: .4rem;
        margin-bottom: 1rem;
     } 

     
     input[type=radio] {
        background: transparent;
        width: 1rem;
     }

     .destination-choice,
     .date-choice,
     .other-choice {
        display: flex;
        justify-content: space-evenly;
        padding: .5rem 0.5rem;
        gap: 4rem;
        margin-bottom:  2rem;
        position: relative;
        transition: 500ms all;
     }

     .destination-choice label,
     .date-choice label,
     .other-choice label{
        font-size: 18px;
        text-shadow: 0 0 1px white;
        position: absolute;
        top: -15px;
     }

     .label-start,
     .label-date-start,
     .label-travelers {
        left: 25px;
     }

     .label-arrive,
     .label-date-return,
     .label-cabinclass {
        right: 25px;
     }

     #span {
        margin-left: 20px;
        color: aquamarine;
        transition: all 400ms;
     }

     #span:hover {
        text-decoration: underline;
        cursor: pointer;
     }

     #airports-nearby {
        color: lightgray;
        position: absolute;
        opacity: 0;
        background: black;
        padding: 10px 20px;
        left: 10px;
        top: 50px; 
        z-index: 2;
        border-bottom-left-radius: 8px;
        border-bottom-right-radius: 8px;
        box-shadow: 0 0 1px lightgray;
        transition: opacity 600ms;
        max-height: 400px;
        max-width: 380px;
        scroll-behavior: smooth;
        text-overflow: ellipsis;
        overflow-y: auto;
        overflow-x: hidden;
        scrollbar-color: blue;
     }

     #airports-nearby:hover {
        cursor: default;
     }

     #airports-nearby::-webkit-scrollbar {
        overflow: scroll;
        width: 20px;
        border-radius: 5%;
     }


     #airports-nearby p:hover,
     #airports-nearby h3:hover{
        color: white;
        opacity: .4;
        cursor: pointer;
     }

     input:not(input[type=radio],input[type=submit]),
     select { 
        text-overflow: ellipsis;
        font-size: 1.2rem;
        padding: 0 2rem;
        width: 24rem;
        height: 2.8rem;
        background: transparent;
        color: lightgray;
        border: transparent;
        border-bottom: 1px  lightgray solid;
        transition: 500ms all;
     } 

     input:not(input[type=radio]):hover,
     select:hover,
     input:not(input[type=radio]):focus{
        outline: 1px solid white;
        border-bottom: 0 solid transparent;
     }

     input[type=text]::placeholder {
        opacity: .6;
        color: lightgray;
     }


     .button-exchange{
        display: grid;
        place-items: center;
        border-radius: 5px;
        background: transparent;
        position: absolute;
        height: 2.8rem;
        width: 2rem;
     }

     .button-exchange:hover,
     .button-exchange:active,
     .button-exchange:focus {
        opacity: 0.8;
        cursor: pointer;
     }

     .button-exchange img {
        width: 100%;
     }

     #start-date,
     #return-date {
        background: url(../assets/calendar.png);
        background-size: 30px;
        background-repeat: no-repeat;
        background-position-x: 20.3rem;
        background-position-y: center;
     }

     select option{
        background: black;
     }

     input[type=submit] {
        text-align: center;
        border-radius: 20px;
        font-size: 1.2rem;
        padding: 0 2rem;
        width: 8rem;
        height: 2.8rem;
        background:limegreen;
        border: 1px solid darkseagreen;
        box-shadow: 1px 1px darkgreen;  
        color: whitesmoke;
     }

     @media screen and (max-width: 1200px){
        .search-container {
            padding: 5vw;
        }

        form {
            padding: 10vw;
        }
        .destination-choice,
        .date-choice,
        .other-choice {
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            gap: 1rem;
            margin-bottom:  1rem;
        }

        .destination-choice label,
        .date-choice label,
        .other-choice label{
            position:static;

        }

        #airports-nearby {
            top: 85px;
        }

        .button-exchange {
            top: 92px;
            left: 180px;
            transform: scale(90%);
        }
        .button-exchange img {
            transform: rotate(90deg);
        }
     }

     @media screen and (max-width: 600px){
        .search-container {
            background: black;
            padding: 0;
        }

        form {
            padding: 1rem .3rem;
        }

        .flight-type-choice {
            display: flex;
            justify-content: center;
            gap: 2rem;
        }
        
        .first-choice,
        .second-choice {
            font-size: 1.2rem;
            display: flex;
            gap: .4rem;
            margin-bottom: .7rem;
        }
        
        .destination-choice,
        .date-choice,
        .other-choice {
            justify-content: center;
            gap: .7rem;
            margin-bottom:  .4rem;
            position: relative;
        }

        
        #airports-nearby {
            top: 70px;
            max-width: 300px; 
        }


        input:not(input[type=radio],input[type=submit]),
        select { 
            padding: 0 1rem;
            width: 19rem;
            height: 2.2rem;
            text-shadow: 0 0 1px black;
        }

        .button-exchange {
            top: 72px;
            left: 140px;
            transform: scale(75%);
        }

        #start-date,
        #return-date {
        background-position-x: 16.3rem;
        }

        

        input[type=submit] {
            font-size: 1.1rem;
            padding: 0 .2rem;
            width: 6rem;
            height: 2.2rem;
        }
     }
</style>