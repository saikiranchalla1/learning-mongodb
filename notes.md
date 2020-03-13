Start MongoDB server
```
    sudo mongod
```
Keep the process running.

```
    mongo
```

Once connected to the Client, we can start running our commands.

Check available databases
```
    show dbs
```

Switch to a database, even if it doesn't exist. If a DB is created, it will not show up in `show dbs` because there is no data

```
    use flights
```

Insert data to a `flightData` collection.

```
    db.flightData.insertOne({
        {
            "departureAirport": "MUC",
            "arrivalAirport": "SFO",
            "aircraft": "Airbus A380",
            "distance": 12000,
            "intercontinental": true
        }
    });

    db.flightData.insertOne({
        {
            "departureAirport": "LHR",
            "arrivalAirport": "TXL",
            "aircraft": "Airbus A320",
            "distance": 950,
            "intercontinental": false
        }
    });
```

Get data from a collection

```
    db.flightData.find().pretty()
```

Inserting document with a custom ID field
```
    db.flightData.insertOne({
        {
            "departureAirport": "LHR",
            "arrivalAirport": "TXL",
            "_id": "txl-lhr-1"
        }
    })
```

Clear the database
```
    db.flightData.deleteOne()
    or
    db.flightData.deleteMany()
```

Let's delete one document
```
    db.flightData.deleteOne({departureAirport: "TXL"})
```

Delete by ID:
```
    db.flightData.deleteOne({_id: "txl-lhr-1"});
```

Since there are no common properties across all documents let's checkout `updateOne` and `updateMany` before using `deleteMany`.

Update a document
```
    db.flightData.updateOne({distance:12000}, {marker: "delete"})
```
This will fail because MongoDB cannot parse or understand the change that needs to be made, hence we need to use `$set` operator, like below:

```
    db.flightData.updateOne({distance:12000}, {$set: {marker: "delete"}})
```

How to add that marker to all the documents?

```
    db.flightData.updateMany({}, {$set: {marker: "toDelete"}})
```

Let's delete all the documents
```
    db.flightData.deleteMany({marker: "toDelete"})
```

Time to add something again.

use insertMany to insert multiple documents. Input is to be provided as an Array of documents
```
    db.flightData.insertMany(
        [
            {
                "departureAirport": "MUC",
                "arrivalAirport": "SFO",
                "aircraft": "Airbus A380",
                "distance": 12000,
                "intercontinental": true
            },
            {
                "departureAirport": "LHR",
                "arrivalAirport": "TXL",
                "aircraft": "Airbus A320",
                "distance": 950,
                "intercontinental": false
            }
        ]
    )
```

How about finding data?

So far we used `find` with no options.

We can pass a document to filter the documents:

```
    db.flightData.find({intercontinental: true}).pretty()
```

How about distance > 12000

```
    db.flightData.find({distance: {$gt : 900}}).pretty()
```

What about findOne?
```
     db.flightData.findOne({distance: {$gt : 900}}).pretty()
```

What about updates?

Change the first flight
```
    db.flightData.updateOne({_id: <INSERT OBJECT ID HERE>}, {$set: {delayed: true}})
```

`update` works like `updateMany`

`update` vs `updateMany`?

```
    db.flightData.updateOne({_id: <INSERT OBJECT ID HERE>}, {$set: {delayed: true}})
    db.flightData.update({_id: <INSERT OBJECT ID HERE>}, {delayed: true})
```
`update` replaces the document with the updating document.


#### Find and Cursor object

Insert many passengers first
```
    db.passengers.insertMany(
        [
            {
                "name": "Max Schwarzmueller",
                "age": 29
            },
            {
                "name": "Manu Lorenz",
                "age": 30
            },
            {
                "name": "Chris Hayton",
                "age": 35
            },
            {
                "name": "Sandeep Kumar",
                "age": 28
            },
            {
                "name": "Maria Jones",
                "age": 30
            },
            {
                "name": "Alexandra Maier",
                "age": 27
            },
            {
                "name": "Dr. Phil Evans",
                "age": 47
            },
            {
                "name": "Sandra Brugge",
                "age": 33
            },
            {
                "name": "Elisabeth Mayr",
                "age": 29
            },
            {
                "name": "Frank Cube",
                "age": 41
            },
            {
                "name": "Karandeep Alun",
                "age": 48
            },
            {
                "name": "Michaela Drayer",
                "age": 39
            },
            {
                "name": "Bernd Hoftstadt",
                "age": 22
            },
            {
                "name": "Scott Tolib",
                "age": 44
            },
            {
                "name": "Freddy Melver",
                "age": 41
            },
            {
                "name": "Alexis Bohed",
                "age": 35
            },
            {
                "name": "Melanie Palace",
                "age": 27
            },
            {
                "name": "Armin Glutch",
                "age": 35
            },
            {
                "name": "Klaus Arber",
                "age": 53
            },
            {
                "name": "Albert Twostone",
                "age": 68
            },
            {
                "name": "Gordon Black",
                "age": 38
            }
            ]
    )
```

Check the last entry using `db.passengers.find()`. Last record you see is NOT the last record you inserted.

Get the next set of records 
```
    it
```

`find` returns a cursor to the records

Get all the records:

```
    db.passengers.find().toArray()
```

In your application:

```
    db.passengers.find().forEach(doc => {printjson(doc)})
```

#### Projection
Get all fields in data
```
    db.passengers.find()
```

Get specific fields in the data:
```
    db.passengers.find({}, {name: 1})
```

Don't need ID?

```
    db.passengers.find({}, {name: 1, _id: 0})
```

#### Embedded documents
```
    db.flightData.updateMany({}, {$set: {status: {desc: "on-time", lastUpdated: "1 hr ago"}}})
```

#### Arrays

```
    db.passengers.updateOne({name: "Albert Twostone"}, {$set: {hobbies: ["sports", "cooking"]}})
```

#### Accessing structured data

```
    db.passengers.findOne({name: "Albert Twostone"}).hobbies
```

```
    db.passengers.find({hobbies: "sports"}).pretty()
```


```
    db.flightData.find({"status.description" : "on-time"})
```
