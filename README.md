# omdb
A simple Node.JS module to access and normalize data from the
[OMDb API](http://www.omdbapi.com/) by Bryan Fritz.

## Installation
    $ npm install omdb

## Examples

```javascript
var omdb = require('omdb');

omdb.search('saw', function(err, movies) {
    if(err) {
        return console.error(err);
    }

    if(movies.length < 1) {
        return console.log('No movies were found!');
    }

    movies.forEach(function(movie) {
        console.log('%s (%d)', movie.title, movie.year);
    });

    // Saw (2004)
    // Saw II (2005)
    // Saw III (2006)
    // Saw IV (2007)
    // ...
});

omdb.search({ terms: 'saw', year: 2005, type: 'movie' }, function(err, movies) {
    if(err) {
        return console.error(err);
    }

    if(movies.length < 1) {
        return console.log('No movies were found!');
    }

    movies.forEach(function(movie) {
        console.log('%s (%d)', movie.title, movie.year);
    });

    // Saw II (2005)
    // Saw Rebirth (2005)
    // I Saw Ben Barka Get Killed (2005)
    // ...
});


omdb.get({ title: 'Saw', year: 2004 }, true, function(err, movie) {
    if(err) {
        return console.error(err);
    }

    if(!movie) {
        return console.log('Movie not found!');
    }

    console.log('%s (%d) %d/10', movie.title, movie.year, movie.imdb.rating);
    console.log(movie.plot);

    // Saw (2004) 7.6/10
    // Two men wake up at opposite sides of a dirty, disused bathroom, chained
    // by their ankles to pipes. Between them lies...
});
```

## API
### omdb.search(terms, callback)
Run a search request on the API. `terms` should be a string containing search
terms or an object such as:

```javascript
{
    terms: String,
    type: 'series' || 'movie' || 'episode' // optional
    year: Number // optional
}
```

`callback` returns an array of movies. If no movies are
found, the array is empty.

```javascript
{
    title: String, // the title of the movie
    type: 'series' || 'movie' || 'episode',

    // If `type` is "series":
    year: {
        from: Number,
        to: Number || undefined // (if the series is still airing)
    },

    // Otherwise,
    year: Number,

    imdb: String
}
```

### omdb.get(show, [fullPlot], callback)
Run a single movie request on the API.

`show` is assumed to be one of the following, respectively:

1. An object with an `imdb` property.

    `{ imdb: 'tt0387564' }`
2. An object with a `title` property.

    `{ title: 'Saw' }`
3. An object with *both* a `title` and a `year` property.

    `{ title: 'Saw', year: 2004 }`
4. An IMDb ID string.

    `'tt0387564'`
5. A title string.

    `'Saw'`

`fullPlot` is an optional argument that if set to `true`, will attempt to
request the extended version of the movie's plot.

`callback` returns an object of the movie's information. If no movies are
found, it will return `null`.

See the following for a list of possible properties:
https://github.com/misterhat/omdb/blob/master/index.js#L154

### omdb.poster(show)
Return a readable stream of the poster JPEG.

`show` is the same as the `show` argument used in `.get()`.

## License
MIT
