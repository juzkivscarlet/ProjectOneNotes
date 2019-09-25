# Purpose of each piece/function/etc.:

## **Global variables**

* Create new global array, artists.
* Create new global variable, 'result', to be re-assigned every new search.

## **Class definitions**
### _Artist (class) definitions_

* class Artist             
    * Creates a template for each artist object
    * Defines all functions to get necessary info from Last.fm & Discogs API's
    * Defines all properties needed for artist objects

* constructor(name)
   * **Create a new Artist object**
   * _parameters:_
      * 'name': the name of the artist
   * Executed whenever a new object is created with class Artist
   * Runs function getAllData(), takes return from it and assigns it to a property 'searchData'

   * Defines property 'name', assigns the value to the 'name' parameter
   * Puts new artist object into array 'artists'

   * Defines an index value for each artist, starting with the current length of the artists array, subtracting one.
       * The first artist will be accessed via artists[0], the second with artists[1], etc.
                            
   * Runs function getLastFMArtist(), passing the parameters this.searchData[0], and this.index
       * this.searchData[0] is the value of the variable 'lastFmSearch', defined in getAllData()


* static getAllData(name) 
   * **Establishes search parameters for first two AJAX calls, *ignore the name of the function* **
   * _parameters:_
      * _'name': the name of the artist_
   * *'static' means this function can only be called on the whole class 'Artist', not individual objects*
   * Defines AJAX Search settings:
       1. Search Last.fm for artist
       2. Search Discogs for Artist
   * Returns these two search settings in an array

* getLastFMArtist(s,i)
   * _parameters:_ 
      * _'s': refers to the AJAX Search settings,_ 
      * _'i': being the index of the artist in array 'artists'_
   * Runs an AJAX call to Last.fm api
   * Gets genres of an artist
   * Gets similar artists
   * Gets summarized bio for artist

* getDiscogsArtist(t,s,callback)
   * **Get basic info from Discogs API about the artist**
   * *parameter 't' is the artist name, 's' is the AJAX search settings, and callback is to run function getReleases()*
   * Get the index of the artist from array 'artists'.
   * Run an AJAX call to the Discogs API, searching for the Artist.
   * Check if the artist title in the API response matches our intended artist
      * If so, create variables for the artist name and image. (We're double checking the artist name to overwrite user errors such as wrong capitalization of the artist name, etc.)
   * Assign the new artist name and image to our artist object
   * When the AJAX call function is complete, run function getReleases, passing in the new artist name, and their index
   
* getReleases(t,n)
   * **Gets an artist's discography, or album release history, from Discogs**
   * _parameters:_
      * _'t': the artist name,_
      * _'n'_ the artist's index in array 'artists'_
   * Create Search settings for AJAX call, which searches for each album by an artist through Discogs
   * Run AJAX call with aforementioned settings.
   * Create a local array, 'releases', and a local boolean (true or false) for whether the Album we received from Discogs API is from the correct artist
   * Loop through our API call results
      * *For each album returned from Discogs, the album title is formatted thusly: {Artist name} - {Album title}. There is no property for each result for the artist, exclusively.*
      * Check if the name of our intended artist is at the beginning of the 'title' property for each album result. 
         * If so, artistIsCorrect is set to true.
         * Otherwise, artistIsCorrect is set to false.
   * Correct the value of our album title from API result (remove artist name and hyphen)
      * Create (for temporary use) a new local variable, 'title', for the album title. Create another variable, 'num', for the index of " - " in the string 'title'.
     * Remove the string " - ", and everything preceding it (the artist name), effectively leaving us just the album title.
   * Create a new local boolean variable, isDuplicate, and set it to false. This variable will be used to determine whether any of the results from the API are duplicates
      * Loop through our array 'releases', checking of our local variable 'title' is equivalent to any of the values given to the property 'title' in array 'releases'. If a match is found, that means we have a duplicate result, so we set isDuplicate to true.
   * For each album result, if the artist is the correct one, and the result is not a duplicate of something that already exists in our data, we will create a new album object.
      * Create new local variable 'album', set to a new Album object.
      * Run function getAlbumImg on object 'album', in order to get the cover art of each album we want.
      * Push the new album to our array, 'releases'

### Album (class) definitions

#### *see **class Artist** and **constructor** under definitions for Artist class*
* getAlbumImg(t,a,ret)
   * **Get an image for the album cover**
   * *parameters:*
      * _'t': the album title,_
      * _'a': the artist name,_
      * _'ret': the album object_
   * Create new local variable img, for the source link of our album cover image.
   * Run an AJAX call to Last.fm.
   * Loop through the images given for each album.
      * *There are about 6 images given for each album. Each has a property called 'size'. We want only the image with a size of "mega"*
      * If the current image's size is "mega", assign our variable 'img' to the value of property '#text" of the image.
   * *Our return object is just the object for the album*
   * Assign property 'img' to the value of our variable 'img' to our return object
   * Run function getTracklist() on return object. We will pass in the track list data from this AJAX call, as well as the return object itself, in as parameters.
   
* getTracklist(data,ret)
   * **Gets the album's tracklist**
   * _parameters:_
      * _'data': the tracklist from our AJAX call in function getAlbumImg(),_
      * _'ret': the object we want this function to run on (I called this a 'return object' earlier)_
   * Create a new local array, 'tracks', and a counter variable, 'numOfTracks'
   * Loop through each track given in our data
      * Create two new variables for song length: one for minutes (rounded down), and one for seconds (0-60).
      * If 'lengthSec' turns out less than 10, it will only be one digit. However, seconds in timestamps are always two digits. So, if lengthSec is less than 10, add a zero in front. If lengthSec is zero, set lengthSec to '00'
   * Create a new local object called 'newTrack'
      * Set property 'number' to one less than our loop index (*i-1*)
      * Set property 'title' to our song name
      * Set property length to 'lengthMin:lengthSec' (for format m:ss)
   * Push 'newTrack' object to array 'tracks'.
   * Increment 'numOfTracks' by one
   * After the above loop is finished, if the length of array 'tracks' is *not* zero, 
      * set a 'tracks' property to our album object equal to our local 'tracks' array,
      * set a property 'numOfSongs' to our local variable 'numOfTracks'

## Functions to add content to the page

* function addCardElement(type,part,title)
   * **Adds a new Bootstrap card element, either a new body to a card, or a new card entirely**
   * *parameters:*
      * _'type': either 'card' or 'body', _
      * _'part': the HTML Element we want to add content to, _
      * _'title': will be the header for our new element_
   * If we create a new "card",
      * Create a new Bootstrap card element, give it a header with text of *title*, and append it to the HTML element *part*
   * If we create a new "body",
      * Create a new div with a Bootstrap class of "card-body"
      * If the "title" we passed in is *"Bio"*, we want to replace the card title to our Artist name, otherwise, it'll be our parameter 'title'
      * Add a new h2 Header with Bootstrap class "card-title", add text according to the above point.
      * Append the card title to the new Card body, and append the new card body to HTML Element *'part'*
      
* function addCardContent(to,type,content,link)
   * **Add content to an existing element**
   * *parameters:*
      * _'to': the HTML element we want to append (called 'part' in addCardElement()),_
      * _'type': either 'text', 'list', or 'image', depending on what type of content we add,_
      * _'content': the actual content we want to add,_
      * _'link': indicates whether we want the new content to be links or not_
   * If we add content with type 'text',
      * Create a new text element with inner HTML of *'content'*
      * Append new text element to *'to'*
   * If we add content with type 'list',
      * Create a new unordered list.
      * Loop through *'content'* parameter
         * _Links created this way will be used for 'similar artists'. When we click on these links, they search for that artist._
         * If we said it's a link, create a new link with a class of *.artist-link*
         * If we said it's not a link, just create a new variable for the content text.
      * Put content text/link in new list item element, and append the new list item to the unordered list.
      * Append the new list to *'to'* element
   * If we add an image,
      * Create a new image, with the *'content'* parameter being the source attribute for the image.
      * Add the image to *'to'* element
      
* function showInfo(d)
   * **Display first batch of info to the page**
   * _parameters:_
      * 'd': result data
   * Add a new card for the Artist bio. In this card, there will be an image, and a paragraph for their bio.
      * Run functions addCardElement() and addCardContent() twice.
   * Add a new card for the music section. In this card, there will be the list of the artist's genres, and similar artists.
      * Run function addCardElement() three times (once for the card, once for each of the two bodies)
      * run function addCardContent() twice
      
* function showDiscography(r)
...

# How it all works:

