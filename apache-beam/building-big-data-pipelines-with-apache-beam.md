## Building Big Data Pipelines with Apache Beam

### Chapter 01

A time-constrained window might be defined as data arriving within a specific time interval – for example, between 1 P.M. and 2 P.M.

A watermark is a (heuristic) algorithm that gives us an estimation of how far we have got in the event time domain. A perfect watermark gives the highest possible time (T) that guarantees no further data arrives with an event time < T.

A window is a specific, bounded range of data within a data stream. Beam has several types of pre-defined window functions:
1. Tumbling windows
2. Sliding windows
3. Session windows

Allowed Lateness defines a timeout (in the event time) after which the state in a window can be cleared and all remaining data can be cleared.

A data model of each (meaningful) streaming processing engine must necessarily include a timestamp for each data element. This holds true for every data element that is emitted from a PTransform object. In Beam, we can easily access this timestamp via a PTransform object called Reify. The purpose of this PTransform object is to make all implicit parts of the data model accessible. These parts include windows and timestamps, among others.

