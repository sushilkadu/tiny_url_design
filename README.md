# tiny_url_design

## Problem statement
Given a URL to shorten, it gives back a unique shortened URL
Given a URL to shorten and a custom string, it gives back a shortened URL with the custom string as suffix (If its available).
When I type shortened URL into the browser, it redirects me to the original URL.


## High level approach
For a given URL, the idea is to generate a unique fixed length key and save it into DB as a key value.

For example, following would be rough schema to save the data.

id | url | unique_key | custom_string

- id = auto generated primary key
- url = actual url provided by the user
- unique_key = Unique key generated against a specific URL.
- custom_string = Optional field, provided by the user. If provided, it should be appended to the unique key.

When someone requests http://<<domain_url_shortner>>/unique_key (or http://<<domain_url_shortner>>/unique_keyCustomString)
Pick the last part of the URL and extract first 7 characters (considering fixed legth unique key). Make a query to DB such that these characters match unique_key 
in database and get the corresponding actual URL and redirect to that url.


## Ensure that no 2 URL's gets mapped to same shortened URL
In this section, we will discuss how can we make sure that no 2 url's get the same unique key.
We have multiple approaches here, lets discuss pros and cons of this approach.

### Hashing a URL
One of the easiest approach would be to create a hash of a given URL. Meaning, f(url) = 'a_supposedly_unique_key'.
And this unique string then can be saved and returned as a part of URL.

Let's discuss problems in this approach.
#### Hashcode really unique?
If we talk about java's hashcode function of String class, it will generate duplicate hashcode.
For example look at the following code.

<img src="hashcode_collision.png">

#### Fixed length
If we rely on in-built function of String class then it would not be able provide us with fixed length hashcode.


### Other approaches
We can use UUID which can generate global unique id, across different machines. But this id is 128 bit long (quite a paradox for url shortner solution :D ) and does not index well in DB.

Another approach would be to generate 12-byte (96-bit) hexadecimal numbers that are made up of (MongoDB ObjectID) -
a 4-byte epoch timestamp in seconds,
a 3-byte machine identifier,
a 2-byte process id, and
a 3-byte counter, starting with a random value.
This is smaller than the earlier 128-bit UUID but again size is relatively longer.


