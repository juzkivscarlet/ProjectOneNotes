# Purpose of each piece/function/etc.:

## definitions for class Artist
* class Artist             
    * Creates a template for each artist object
    * Defines all functions to get necessary info from Last.fm & Discogs API's
    * Defines all properties needed for artist objects

* constructor(name)             
    * Executed whenever a new object is created with class Artist
    * Runs function getAllData(), takes return from it and assigns it to a property 'searchData'

    * Defines property 'name', assigns the value to the 'name' parameter
    * Puts new artist object into array 'artists'

    * Defines an index value for each artist, starting with the current length of the artists array, subtracting one.
        * The first artist will be accessed via artists[0], the second with artists[1], etc.
                            
    * Runs function getLastFMArtist(), passing the parameters this.searchData[0], and this.index
        * this.searchData[0] is the value of the variable 'lastFmSearch', defined in getAllData()


* static getAllData(name)    
    * Defines AJAX Search settings:
        1. Search Last.fm for artist
        2. Search Discogs for Artist
    * Returns these two search settings in an array

* getLastFMArtist(s,i)
    * Runs an AJAX call to Last.fm api
    * Gets genres of an artist
    * Gets similar artists
    * Gets summarized bio for artist

* getDiscogsArtist(t,s,callback)


# How it all works:

