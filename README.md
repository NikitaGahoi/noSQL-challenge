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

**1. Database and Jupyter Notebook Set Up**

-  **Data Import:** The dataset, available in JSON format, was imported into a MongoDB database named `uk_food` and a collection named `establishments` using the `mongoimport --type json -d uk_food -c establishments --drop --jsonArray establishments.json` command.
-  **Python and MongoDB Setup:** The PyMongo library facilitated interaction with MongoDB through Python, establishing a connection and providing essential functionality.
-  **Pretty Print for Readability:** The `Pretty Print` library was incorporated to enhance the readability of data during both the setup and analysis phases.
-  **Database Verification:** Validation commands, such as `mongo.list_database_names()`, were executed to confirm the successful import of the database.

**2. Update the Database**

- **Addition of New Restaurant:** The project involved the addition of a new halal restaurant, `"Penang Flavours"` to the database, complete with detailed information.
- **BusinessTypeID Update:** To ensure data consistency, the `BusinessTypeID` for "Restaurant/Cafe/Canteen" was determined and applied to update the new restaurant's data.
- **Removal of Dover Establishments:** Pruning the dataset involved the removal of all establishments within the `Dover Local Authority` to align with the magazine's preferences.
- **Data Transformation:** Numeric values stored as strings, specifically `latitude`,`longitude`, and `RatingValue`, underwent conversion to appropriate numeric types.


