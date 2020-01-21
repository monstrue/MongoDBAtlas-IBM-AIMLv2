# Writing the code
You can use the completed code located in the `FinishedSampleCode` if you want. However if you want to learn the process for writing it, follow along here.

## Pre-reqs
* Have access to the IBM Watson Vision API
* Have MongoDB Atlas deployed

## Preparing the Python environment
* Run the following commands to install python3 and python prereqs

```
  sudo apt-get update
  sudo apt-get install python3-pip
  pip3 install tornado dnspython pymongo ibm-watson
```

* The packages are used for (respectively): a python webserver, allow the use of MongoDB SRV connection strings, connect to MongoDB databases, and connect to the IBM Watson Vision API
* create a directory called `example` and then `cd example`

## Creating the sample python script
Using the text editor of your choice, create a `runner.py` with the simplified contents as below:

At the top we start with our imports:

```
import pymongo
from bson.objectid import ObjectId
import os
from bson.json_util import dumps
from watson_developer_cloud import VisualRecognitionV3
import json
```

To review these imports, `pymongo` is the official MongoDB driver for python, we need the `bson` line to convert the string version of a ObjectID to the `ObjectID` datatype found in the [BSON spec](http://bsonspec.org/) and to quickly dump the output to strings, `json` is used to parse JSON, and then the official library for communicating with the IBM Watson Vision API is used.

After that is there, let's put our global configuration and modify it with your MongoDB Python connection string:

```
# configure connection to mongodb
conn = pymongo.MongoClient("INSERT YOUR MONGODB CONNECTION STRING HERE")
handle = conn["ibmdemo"]["democol"]

# configure connection to watson VisualRecognitionV3 api
visual_recognition = VisualRecognitionV3(
    cfg['DEFAULT']['_WATSONAPIVER'],
    iam_apikey=cfg['DEFAULT']['_WATSONAPIKEY'])
```

Then is connecting to a MongoDB Change Stream. This opens a listener on the collection and will get a notification of every insert, update, delete, and replace. Here we care about an insert and make sure that insert has a `url` attribute. 

That last piece just prints the data out on the command line.

This is meant to be a simple illustration of how this works. Security is bypassed and this will not scale well. However with this knowledge, you can build applications using these features.

Note that Python is very particular about white space, so as you copy/paste, make sure that your tabs are all even.

## Running the aplication
Once that is saved, run the application:
* `python3 runner.py`
* Insert a document into the database using MongoDB Compass or the new Data Explorer view in MongoDB Atlas which you can get to via the "Collections" button. The document you insert should have a field called `url` which is a full URL to an image.
* Notice that in the CLI output of the python script that it saw an insert
* Notice that after the insert, the change stream called the IBM Watson Vision API to see what is in it. Refresh the Compass or Atlas Data Explorer view and see the rich data structure of the IBM Watson Vision API.
