# Tube-Mapper

## Overview
Tube-mapper is my first project using React, and was produced as part of the General Assembly Software Engineering Immersive Course.

In a pair our task was to create, in 2 days, a front-end React app that utilised a public API and contained multiple pages.

After investigating a range of freely available API's, we decided to use the TFL (Transport for London) API to create an app that showed station information and arrival times for every station and train within the London Underground Network.

You can view the project [here](https://peteg-cmd.github.io/project-2/)

## The Brief

**Build a React application** that consumes a **public API**

* **Consume a public API** – this could be anything but it must make sense for your project.
* **Have several components** - At least one classical and one functional.
* **The app should include a router** - with several "pages".
* **Include wireframes** - that you designed before building the app.
* Have **semantically clean HTML** - you make sure you write HTML that makes structural sense.
* **Be deployed online** and accessible to the public.



## The Technologies Used
* React
* JavaScript (ES6)
* HTML5
* CSS3
* Bulma
* TFL API
* Git and GitHub

## The Approach

The app consists of 3 main pages, each built from several components. The fist page shows all the available tube lines, along with their current status.

It also includes a search bar, which a feature on each page to allow users to filter the information shown if needed. The code to fetch the data from the TFL API and then to implement the filter is shown below.

	//GET THE DATA REQUIRED FROM TFL
	
	componentDidMount() {
	    axios.get('https://api.tfl.gov.uk/Line/Mode/tube/Status?detail=true')
	      .then(res => this.setState({
	        lineData: res.data,
	        filteredLines: res.data
	      }))
	      .catch(err => console.error(err))
	  }
	
	//ALLOW THE USER TO FILTER THE LINES IF NEEDED
	
	  filterTheLines(event) {
    const searchQuery = event.target.value
    const filteredLines = this.state.lineData.filter((line) => {
      const regex = new RegExp(searchQuery, 'i')
      return line.name.match(regex)
    })

    this.setState({
      filteredLines,
      query: searchQuery
    })
  	}

The home page is shown below in mobile view:

<img src='assets/tubemap - mobile home.png'>

The colours are taken from the TFL documentation and implemented within the class using a variable:

	const hexForTubeLines = {
	  'Bakerloo': '#996633',
	  'Central': '#CC3333',
	  'Circle': '#FFCC00',
	  'District': '#006633',
	  'Hammersmith & City': '#CC9999',
	  'Jubilee': '#868F98',
	  'Metropolitan': '#660066',
	  'Northern': '#000000',
	  'Piccadilly': '#0019a8',
	  'Victoria': '#0099CC',
	  'Waterloo & City': '#66CCCC'
	}


Clicking on the required line will then take the user to the 'stations' page showing every station on the line, and giving the user the option to see the station facilities available, or the live arrival times of trains in to the station.

Clicking to see the station information will trigger a modal view that displays the information available. The modal is controlled via state on the 'lineStations' page. 
	
	//FIRST THE LINE STATION IS SET, THEN THE MODAL TOGGLED AS REQUIRED
	
	 handleClick(station) {
	    this.setState({ singleLineStation: station })
	    this.toggleModal()
	  }
	  
	  toggleModal() {
	    const tempModal = !this.state.modal
	    this.setState({ modal: tempModal })
	  }
	  
The station cards seen in the below example are controlled in a separate functional component and rendered as required. You can also see an example of the modal being activated.

<img src='assets/tubemap - station list.png'>

<img src='assets/tubemap - facilities.png'>


## Arrivals

From this line stations page the user is also able to click to see the arrival times and train destinations in to each platform at the station.

When this page is loaded it is passed the line and station ID as props, and uses these to make another call to the TFL API for the specific station that has been selected.

This information is then sorted, and periodically updated every 10 seconds.

	updataData() {
	
	//SET THE INTERVAL TO UPDATE EVERY 10 SECONDS

    setInterval(() => {
      const id = this.props.match.params.id
      const line = this.props.match.params.line
      axios.get(`https://api.tfl.gov.uk/Line/${line}/Arrivals/${id}`)
        .then(res => this.setState({ arrivalsInformation: res.data }))
        .catch(err => console.error(err))
      console.log('Hello')
    }, 10000)
	  }
	
	  sortArrivalsInformation() {
	  
	  //THE RECEIVED DATA IS NOT ORDERED, SO IS SORTED BY MINUTES TO ARRIVAL
	  
	    const newArrivalInfo = { ...this.state.arrivalsInformation }
	    newArrivalInfo.sort(function (a, b) {
	      return parseInt(a.timeToStation) - parseInt(b.timeToStation)
	    })
	    console.log(newArrivalInfo)

    this.setState({ arrivalsInformation: newArrivalInfo })
 	}
 	

The ordered arrival information is then shown to the user for each platform the station has, along with the trains final destination.

Below this can be seen in desktop and mobile view. In mobile view the layout is adjusted using a media query to assess the screen width.

	//ASSESS SCREEN SIZE AND ADJUST MOBILE LAYOUT
	
	@media screen and (max-width: 815px) {
	  .lineBox {
	    flex-direction: column;
	  }
	  .arrivalBox {
	    flex-direction: column;
	  }
	}

Desktop: 

<img src='assets/tubemap - arrivals.png'>


Mobile:

<img src='assets/tubemap - mobile arrivals.png'>



## Challenges

One of the main challenges with this project was the filtering of the information from TFL around the available station facilities, and although we have filtered out some information, the data is still not good enough.

For example in the station information there are results such as 'PhoneNo', 'address', and some others which do not provide much useful information for our user, and these are delivered along with the useful information. They are also not always identical which makes consistently filtering them out more of a challenge.


## Potential Future Features

There are three bits of further work i would like to do on this project, but that we did not have time for initially. 

Firstly I would like to use a little more of the information available from TFL. Away from the issues around the station facilities data, the data on trains and lines is actually very good, and although we only allow the user to access one line at a time, the data contains the necessary information to allow a user to navigate from one station to another line, and even to bus routes, and i would like to include this.

Secondly I would like to include some graphics to not only show how long until the train arrives, but to overlay this on a map to make the overall user experience better.

Lastly, the look and feel of the app needs a bit of work, and although at the time we made the conscious decision to focus on the line data, i would like to make it look a little better too.

## Successes

The biggest success of this project was learning about how the API functioned, and ensuring that we were making the correct calls to receive only the data we needed. Although this API allows you to make a single call and receive almost all the information available, this would mean a lot of data was unnecessarily processed, slowing the application down. We managed to avoid this by being very specific wit out API calls.

## Lessons Learnt

For this project the main lesson is that we should have read the documentation around the TFL API in more detail before diving in to the coding. In retrospect there is a lot more information available to us, and better ways of accessing it than we initially thought. In future I will definitely spend longer looking at all the features of external API's before I implement them.