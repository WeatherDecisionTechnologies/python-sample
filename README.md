# Background

WDT creates, ingests, processes, and distributes a large amount of weather data.

A lot of people rely on this data to make critical life and business decisions. As a result, we need to process this data quickly and reliably.

Since we are a small company with limited resources, it's imperative that our programs function correctly, self-correct when they can, and leave useful clues about what went wrong and how to fix it when they cannot recover.

# Task

Since we process many types of data, and process that data in many different ways, we have implemented a distributed architecture based on message queues.

For the purpose of this task however, we are going to abstract that away and just use the command line as an input source. Your task is to write a program that reads lightning data from stdin (one lightning strike per line as a [JSON](https://en.wikipedia.org/wiki/JSON) object, and matches that data against a source of assets (also in JSON format) to produce an alert.

An example 'strike' coming off of the exchange looks like this:
```
{
    "flashType": 1,
    "strikeTime": 1386285909025,
    "latitude": 33.5524951,
    "longitude": -94.5822016,
    "peakAmps": 15815,
    "reserved": "000",
    "icHeight": 8940,
    "receivedTime": 1386285919187,
    "numberOfSensors": 17,
    "multiplicity": 1
}
```

#### Where:
 - flashType=(0='cloud to ground', 1='cloud to cloud', 9='heartbeat')
 - strikeTime=the number of milliseconds since January 1, 1970, 00:00:00 GMT

#### note:
   - A 'heartbeat' flashType is not a lightning strike. It is used internally by the software to maintain connection.

An example of an 'asset' is as follows:
```
  {
    "assetName":"Dante Street",
    "quadKey":"023112133033",
    "assetOwner":"6720"
  }

```
---

You might notice that the lightning strikes are in lat/long format, whereas the assets are listed in quadkey format.

There is a simple conversion process, outlined [here](http://msdn.microsoft.com/en-us/library/bb259689.aspx) that you can take advantage of. Feel free to use an open source library as well, such as [this](https://github.com/buckhx/QuadKey)

For this purpose, you can assume that all asset locations are at a zoom level of '12'.

For each strike received, you should simply print to the console the following message:

```
lightning alert for <assetOwner>:<assetName>
```

But substituting the proper assetOwner and assetName.

i.e.:

```
lightning alert for 6720:Dante Street
```

There's a catch though... Once we know lightning is in the area, we don't want to be alerted for it over & over again. Therefore, if someone (an asset owner) has already received a lightning strike alert for a particular location, we should ignore any additional strikes that occur in that quadkey for that asset owner.

---

# Implementation

The code should be written in [pep-8](https://www.python.org/dev/peps/pep-0008/) compliant python. Many developers at WDT use a free editor called [PyCharm](https://www.jetbrains.com/pycharm/download/) that will automatically lint your code and highlight sections of code that might not be pep-8 compliant or incorrect in some other way.

Since code is read more often than it is written, we want to our projects well structured. We have adopted the following structure for laying out projects: https://github.com/mapbox/pyskel You should base your project layout on that.

Your program should be runnable with the following steps (assuming you named your program 'sample'):

$ pip install -r requirements.txt
$ pip install -e .[test]
$ sample < lightning.json assets.json

The files containing lightning strikes (as single JSON objects) and assets (as an array of JSON objects) can be found in this repo.

Feel free to use open source libraries where available...

#### Please send us a link to your source code checked in on github. Note that we are a Linux shop and expect the program to run on Linux.

### What we're looking for
 - Correctness. If the program doesn't run correctly, it doesn't matter how beautiful or efficient it is.
 - Conciseness. Small is beautiful. Easy to read is paramount. The easier it is for someone else to come in and modify your program, the better.
 - Reliability. You should expect to handle bad data, and expect to handle failures.

#### In addition, please answer the following questions:
 - What is the [time complexity](https://en.wikipedia.org/wiki/Time_complexity) for determining if a strike has occurred for a particular asset?
 - If we put this code into production, but found it too slow, or it needed to scale to many more users or more frequent strikes, what are the first things you would think of to speed it up?
