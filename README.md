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
- unique_key = Unique key generated against a specific URL. This would be fixed length ( say 7 characters)
- custom_string = Optional field, provided by the user. If provided, it should be appended to the unique key.

When someone requests http://<<domain_url_shortner>>/unique_key (or http://<<domain_url_shortner>>/unique_keyCustomString)
Pick the last part of the URL and extract first 7 characters. Make a query to DB such that these characters match unique_key 
in database and get the corresponding actual URL and redirect to that url.


## Ensure that no 2 URL's gets mapped to same shortened URL
In this section, we will discuss how can we make sure that no 2 url's get the same unique key.
We have multiple approaches here, lets discuss pros and cons of this approach.

### Hashing a URL
One of the easiest approach would be to create a hash of a given URL. Meaning, f(url) = 'a_supposedly_unique_key'.
And this unique string then can be saved and returned as a part of URL.

Let's discuss problems in this approach.
#### Hashcode really unique?
If we talk about java's hashcode function of String class, it will generate duplicate hascode.
For example look at te following code.
<img src="hashcode_collision.png">
