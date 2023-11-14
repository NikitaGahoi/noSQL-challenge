# noSQL-challenge

## Introduction

MongoDB, a robust NoSQL database system, was leveraged for a comprehensive analysis of food establishment ratings data from the UK Food Standards Agency. Commissioned by the food magazine company, "Eat, Safe, Love," this project aimed to facilitate informed decision-making regarding the selection of establishments for future articles. The report is structured into key sections, covering project background, methodology, and detailed insights obtained through exploratory analysis.

MongoDB, renowned for its document-oriented model and BSON storage format, provides agility in handling diverse datasets. In this project, MongoDB was chosen due to its flexibility and efficiency, particularly in managing JSON-like data structures. The dataset under consideration includes information about food establishments, such as hygiene scores, geographical details, and ratings. The project's focal point was the implementation of MongoDB for data storage and retrieval, aided by Python and Jupyter Notebooks for seamless interaction.

## Project Structure

The project was organized into three main phases, with each phase addressing specific aspects of the dataset:

**1. Database and Jupyter Notebook Set Up**

-  **Data Import:** The dataset, available in JSON format, was imported into a MongoDB database named `uk_food` and a collection named `establishments` using the `mongoimport --type json -d uk_food -c establishments --drop --jsonArray establishments.json` command.
-  **Python and MongoDB Setup:** The PyMongo library facilitated interaction with MongoDB through Python, establishing a connection and providing essential functionality.
-  **Pretty Print for Readability:** The `Pretty Print` library was incorporated to enhance the readability of data during both the setup and analysis phases.
-  **Database Verification:** Validation commands, such as `mongo.list_database_names()`, were executed to confirm the successful import of the database.


