# noSQL-challenge

## Introduction

MongoDB, a robust NoSQL database system, was leveraged for a comprehensive analysis of food establishment ratings data from the UK Food Standards Agency. Commissioned by the food magazine company, "Eat, Safe, Love," this project aimed to facilitate informed decision-making regarding the selection of establishments for future articles. The report is structured into key sections, covering project background, methodology, and detailed insights obtained through exploratory analysis.

MongoDB, renowned for its document-oriented model and BSON storage format, provides agility in handling diverse datasets. In this project, MongoDB was chosen due to its flexibility and efficiency, particularly in managing JSON-like data structures. The dataset under consideration includes information about food establishments, such as hygiene scores, geographical details, and ratings. The project's focal point was the implementation of MongoDB for data storage and retrieval, aided by Python and Jupyter Notebooks for seamless interaction.

This project is broken up into two Jupyter Notebook:

 **1. NoSQL_setup_starter:** This notebook covers the first two sections of this repository, the database and jupyter notebook set up, as well as the steps that were taken to update the database before performing an exploratory analysis on the establishments.
 
 **2. NoSQL_analysis_starter:** This notebook contains the code to answer the following questions:
   - Which establishments have a hygiene score equal to 20?
   - Which establishments in London have a RatingValue greater than or equal to 4?
   - What are the top 5 establishments with a RatingValue of 5, sorted by lowest hygiene score, nearest to the new restaurant added, "Penang Flavours"?
   - How many establishments in each Local Authority area have a hygiene score of 0? Sort the results from highest to lowest, and print out the top ten local authority areas.

## Project Structure

The project was organized into three main phases, with each phase addressing specific aspects of the dataset:

### 1. Database and Jupyter Notebook Set Up

-  **Data Import:** The dataset, available in JSON format, was imported into a MongoDB database named `uk_food` and a collection named `establishments` using the `mongoimport --type json -d uk_food -c establishments --drop --jsonArray establishments.json` command.
-  **Python and MongoDB Setup:** The PyMongo library facilitated interaction with MongoDB through Python, establishing a connection and providing essential functionality.
-  **Pretty Print for Readability:** The `Pretty Print` library was incorporated to enhance the readability of data during both the setup and analysis phases.
-  **Database Verification:** Validation commands, such as `mongo.list_database_names()`, were executed to confirm the successful import of the database.

### 2. Update the Database

- **Addition of New Restaurant:** The project involved the addition of a new halal restaurant, `"Penang Flavours"` to the database, complete with detailed information.

```python
new_restaurant = {
    "BusinessName":"Penang Flavours",
    "BusinessType":"Restaurant/Cafe/Canteen",
    "BusinessTypeID":"",
    "AddressLine1":"Penang Flavours",
    "AddressLine2":"146A Plumstead Rd",
    "AddressLine3":"London",
    "AddressLine4":"",
    "PostCode":"SE18 7DY",
    "Phone":"",
    "LocalAuthorityCode":"511",
    "LocalAuthorityName":"Greenwich",
    "LocalAuthorityWebSite":"http://www.royalgreenwich.gov.uk",
    "LocalAuthorityEmailAddress":"health@royalgreenwich.gov.uk",
    "scores":{
        "Hygiene":"",
        "Structural":"",
        "ConfidenceInManagement":""
    },
    "SchemeType":"FHRS",
    "geocode":{
        "longitude":"0.08384000",
        "latitude":"51.49014200"
    },
    "RightToReply":"",
    "Distance":4623.9723280747176,
    "NewRatingPending":True
}
```
 - **BusinessTypeID Update:** To ensure data consistency, the `BusinessTypeID` for "Restaurant/Cafe/Canteen" was determined and applied to update the new restaurant's data. To update the new restaurant data, the following command is used: `establishments.update_one(new_restaurant, [{'$set' : {'BusinessTypeID' : 1}}])`.

- **Removal of Dover Establishments:** Pruning the dataset involved the removal of all establishments within the `Dover Local Authority` to align with the magazine's preferences using the `delete_many()` method

- **Data Transformation:** Numeric values stored as strings, specifically `latitude`,`longitude`, and `RatingValue`, underwent conversion to appropriate numeric types.

Converting the latitude and longitude to decimal numbers:
```python
lat_long_update_query = {
    '$set': {
        'geocode.latitude': {
            '$toDouble': '$geocode.latitude'
        },
        'geocode.longitude': {
            '$toDouble': '$geocode.longitude'
        }
    }
}
```

Converting the RatingValue to integer numbers:

```python
RatingValue_update_query = {
    '$set': {
        'RatingValue': {
            '$toInt': '$RatingValue'
        }
    }
}
```

### 3. Exploratory Analysis

In this section, we will delve into the food establishment data to provide insights and answers to specific questions posed by the magazine editors and help them make the decision. 

Following key notes were considered while exploring the dataset:

* RatingValue: This field represents the overall rating given by the Food Authority, ranging from 1 to 5, with higher values indicating better ratings. It may also include non-numeric values like 'Pass,' which implies that the establishment passed inspection but doesn't have a numeric rating. Note that non-numeric values will be treated as nulls during data setup.
* The scores for Hygiene, Structural, and ConfidenceInManagement are inversely proportional; higher values indicate poorer performance in these areas.

1. Which establishments have a hygiene score equal to 20?

```python
# Find the establishments with a hygiene score of 20
query = {'scores.Hygiene' : {'$eq': 20}}

# Use count_documents to display the number of documents in the result
hygiene_score = establishments.count_documents(query)
print("The number of establishments with hygiene score of 20 are", hygiene_score, "\n")
```

2. Which establishments in London have a RatingValue greater than or equal to 4?
```python
# Find the establishments with London as the Local Authority and has a RatingValue greater than or equal to 4.
RatingValue_query = {'LocalAuthorityName': {'$regex': 'London'},
                            'RatingValue': {'$gte':4}}

# Use count_documents to display the number of documents in the result
RatingValue_London_results = establishments.count_documents(RatingValue_query)
print("The number of establishments in London with rating greater than or equal to 4 are", RatingValue_London_results, "\n")
```

3. What are the top 5 establishments with a RatingValue of 5, sorted by lowest hygiene score, nearest to the new restaurant added, "Penang Flavours"?
```python
# Search within 0.01 degree on either side of the latitude and longitude.
# Rating value must equal 5
# Sort by hygiene score

degree_search = 0.01
latitude =  establishments.find_one({'BusinessName': 'Penang Flavours'})['geocode']['latitude']
longitude = establishments.find_one({'BusinessName': 'Penang Flavours'})['geocode']['longitude'] 

query = { 'RatingValue': {'$eq': 5},
          'geocode.latitude': {'$gte': latitude - degree_search, '$lte': latitude + degree_search },
          'geocode.longitude': {'$gte': longitude - degree_search, '$lte': longitude + degree_search}
        }


sort =  [('scores.Hygiene', -1)] 

limit = 5

# Print the results
result = list(establishments.find(query).sort(sort).limit(limit))
pprint(result)
```


4. How many establishments in each Local Authority area have a hygiene score of 0?

```python
# 1. Matches establishments with a hygiene score of 0
match = {'$match': {'scores.Hygiene': {'$eq': 0}}}
         
# 2. Groups the matches by Local Authority
group = {'$group': {
                    '_id': '$LocalAuthorityName',
                    'count': { '$sum': 1 }
                    }
         }

# 3. Sorts the matches from highest to lowest
sort = {'$sort': {'count': -1}}

pipeline = [match, group, sort]

```

## Conclusion

The MongoDB project for "Eat, Safe, Love" showcased the database's adaptability to handle real-world scenarios. The utilization of Python and Jupyter Notebooks facilitated a seamless workflow, integrating data setup, updates, and exploratory analysis. MongoDB's document-oriented structure proved advantageous in managing diverse data, allowing for flexibility in schema design. This project underscores the significance of NoSQL databases in data-driven decision-making processes and highlights MongoDB's pivotal role in handling complex datasets for actionable insights. The detailed exploration provided valuable information, empowering the food magazine company to make informed choices for their upcoming articles.


## References

UK Food Standards AgencyLinks to an external site. (2022). UK food hygiene rating data API. https://ratings.food.gov.uk/open-data/en-GBLinks to an external site.. Contains public sector information licensed under the Open Government Licence v3.0Links to an external site.
Accessed Sept 9, 2022 and Sept 12, 2022 with the establishment settings as follows: longitude=51.5072, latitude=-0.1276, maxdistancelimit=4567, pagesize=10000, sortoptionkey=distance, pagenumber=(1,2,3,4,5,6,7,8).






