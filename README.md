# MongoDB-all-info-in-excel
import pymongo
import pandas as pd

# Connect to MongoDB server
client = pymongo.MongoClient("mongodb://172.16.0.42:27017/")

# Get database names
db_names = client.list_database_names()

# Create an empty DataFrame to store the results
data = pd.DataFrame(columns=['Database Name', 'Collection Name', 'Field Name', 'Field Type'])

# Loop through each database and collection and get its fields
for db_name in db_names:
    # Get collection names for current database
    collection_names = client[db_name].list_collection_names()
    
    # Loop through each collection and get its fields
    for collection_name in collection_names:
        # Get fields for current collection
        fields = client[db_name][collection_name].find_one().keys()
        
        # Loop through each field and add it to the DataFrame
        for field in fields:
            data = data.append({'Database Name': db_name,
                                'Collection Name': collection_name,
                                'Field Name': field,
                                'Field Type': type(client[db_name][collection_name].find_one()[field]).__name__},
                               ignore_index=True)

# Write the DataFrame to an Excel file
data.to_excel('mongo_info.xlsx', index=False)
