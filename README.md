# house-prices

**[Example Report](https://docs.google.com/spreadsheets/d/1s1gbR3nAoLc4Oa2xlKJjPuR4ZIbawRuVL8F4juD3NgE/edit?usp=sharing):**
<br>

[<img width="1500" src="https://github.com/user-attachments/assets/560f8f6f-331b-4991-98b4-37ab38e6e7db">
](https://docs.google.com/spreadsheets/d/1s1gbR3nAoLc4Oa2xlKJjPuR4ZIbawRuVL8F4juD3NgE/edit?usp=sharing)

<br>

**Purpose:**
To help find a house to buy, good value and within set parameters. Way to incorporate all available data in a systematic way. Currently you search through thousands of properties manually googling commute times and assessing their value. Hoping to be able to predict house value/potential using machine learning models.

**Features:**
Provide single system to search for properties using Zoopla API (filter by commute times to our work, filter by good value compared to available data). Provide report for comparison containing highlighted properties with all available data e.g. travel times, past sales, area index
Price estimate using ML model.

**Introduction:**
This project is an improved way to search for a property to buy. I have created it for my own use in London, however the code can be applied to anywhere in the UK (and quite easily adjusted for overseas https://www.zoopla.co.uk/overseas/).

Although online property sites assist in the process of buying a house beyond estate agents, I believe there is still a gap in the services they provide. For example, in my case I was buying with a partner and we both wanted a reasonable commute to work. This seems like a situation which won't be uncommon, yet Zoopla and it's competitors only allow you to search for commute times to one address. To map the overlap between two areas, there is a useful tool online (http://traveltime.propertywide.co.uk/) which enables you to draw a map and search properties within a commute to two addresses. This was the only such tool available at time of my search (25/04/2018) and had a limited amount of properties which were available. I ended up resorting to manually drawing the map provide onto Zoopla using their smart map tool (https://www.zoopla.co.uk/smartmaps/).

A better way to complete this search is using the Zoopla API [Zoopla-property-listing-API.ipynb](Zoopla-property-listing-API.ipynb) to request property listings within certain search parameters, then use the Google Maps API [Google-maps-API.ipynb](Google-maps-API.ipynb)to add the commute time to any number of addresses of interest, then web scrape [Zoopla-Web-Scraper.ipynb](Zoopla-Web-Scraper.ipynb) any additional information from the link in the property listing. This pipeline [Pipeline-sanitised.ipynb](Pipeline-sanitised.ipynb) outputs a csv which is in turn stored in an Excel online, where a view can be created [House-prices.xlsx](House-prices.xlsx) in order to filter and search the available properties much more easily than the limited options currently available through property listing sites. A separate sheet is used to store the listing id of properties already viewed and put into either yes, no or maybe. These are used to conditionally format the main sheet so you don't look at the same property twice.

Options for searches include:
- within any combination of search times for multiple addresses
- price put on market higher than current i.e. been reduced
- page views in last 30 days/all time
- area rating out of 5 (broken into community and safety, entertainment and nightlife, parks and recreation, restaurants and shopping, schools and public services, transport and travel)
- rental price and its comparison with sale price
- area value (current, 3 months, 6 months, 1-5 years) [Zed-index-API.ipynb](Zed-index-API.ipynb)
- increasing area value (using regression) [Zoopla-Regression-area-value.ipynb](Zoopla-Regression-area-value.ipynb)


**Data:**
[DataFlowchart.png](DataFlowchart.png)
Zoopla property listings using API - picks up ones from most recently added to keep up to date list of current market.
 
This list contains:
- a link to the listing
- the address (to match to my data)
- property type
- price and price changes
- number of rooms
- potential NLP data from desc

At time of writing there are 10,000 properties within 20 miles of waterloo between 250,000 and 400,000 max 2 bed.

The user can join data to this database:
- google maps commute times and time to nearest station
- local area price index from zoopla API
- area value change 3, 6, 12 months, 5, 10, 20 years
- similar rental price and house price
- travel cost

Web scraper:
- asking prices and rents of similar properties
- price put on market (for price increase)
- date put on market (time took to sell)
- page views
- council tax
- bills
- broadband speed
- miles to nearest station
- area stats (avg current value for property type, £sqft for property type)
- local area ratings (overall, community, parks etc.) can get the number of stars

**Model**
What may be useful in a house search would be an accurate evaluation of a house based on all available information online. For example, a linear regression could be applied to all similar properties or if enough of the same data is available on a new property potentially a neural net could be used. 

I began by undertaking a Kaggle competition to predict house prices on the Ames housing dataset (https://www.kaggle.com/c/house-prices-advanced-regression-techniques). This taught the basics of exploring, cleaning and modelling a similar data landscape. The data provided is quite extensive however, detailing 80 different variables for every single property. The dataset is also relatively small and taken in the same year, enabling you to train models quite accurately.

My search online for housing data in London provided two main sources - the price paid dataset from gov.uk (https://data.gov.uk/dataset/land-registry-monthly-price-paid-data) and the house price index dataset from (http://landregistry.data.gov.uk/app/ukhpi). 

The price paid data contains the sale price of all properties from the land registry across England and Wales since 1995, along with some basic details about the property such as area, tenure, old/new and property type. I was able to join the Energy Performance Certificate dataset (https://epc.opendatacommunities.org/domestic/search) to this (see [Price-paid-Data-Sourcing.ipynb](/Price-paid-Data-Sourcing.ipynb)) which provided numerous categorical and continuous variables such as total floor space, number of habitable rooms, cost of heating etc. I also adjusted the price for inflation.

In [Data_exploration.xlsx](Data_exploration.xlsx) I followed some of the same methodologies from (https://www.kaggle.com/pmarcelino/comprehensive-data-exploration-with-python/notebook) which helps to identify the most important variables for predicting house price. I continued to explore the data further, visualising it and seeking correlations to price paid. Linear regression models had a degree of success when limited to those properties in a similar area, due to the contrast in areas of London. 

An attempt ([Price-paid-Neural-Net.ipynb](Price-paid-Neural-Net.ipynb))to apply neural nets to the dataset using FastAI (https://github.com/fastai) proved less than satisfactory, despite attempts ([Price-paid-Data-Clean.ipynb](Price-paid-Data-Clean.ipynb)) to clean the data. The same quality/coverage of data available in the Ames housing dataset is not freely available in London.

Another dataset available is the house price index applies a hedonic regression model to the various sources of data on property price and attributes to produce estimates of the change in house prices each period. A similar method was used to train a neural net [House-price-index-Neural-Net.ipynb](House-price-index-Neural-Net.ipynb) on this data to predict the value of a house given its location and the past data about its price and the area index at that time.
