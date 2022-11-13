# Send Get Requests

```javascript
import React, {useState} from 'react';
 
import MoviesList from './components/MoviesList';
import './App.css';
 
function App() {
 
  const [movies, setMovies] = useState([]);
 
  function fetchMoviesHandler () {
    fetch('https://swapi.dev/api/films')
    .then(response => {
      return response.json();
    })
    .then((data)=>{
      const trasnformedMovies = data.results.map(movieData=>{
        return {
          id:movieData.episode_id,
          title: movieData.title,
          openingText: movieData.opening_crawl,
          releaseDate: movieData.release_date
        }
      })
      setMovies(trasnformedMovies)
    });
  }
 
  return (
    <React.Fragment>
      <section>
        <button onClick={fetchMoviesHandler}>Fetch Movies</button>
      </section>
      <section>
        <MoviesList movies={movies} />
      </section>
    </React.Fragment>
  );
}
 
export default App;
 
```

Using async/await

```javascript
import React, {useState} from 'react';
 
import MoviesList from './components/MoviesList';
import './App.css';
 
function App() {
 
  const [movies, setMovies] = useState([]);
 
  async function fetchMoviesHandler () {
    const response = await fetch('https://swapi.dev/api/films')
    const data = await response.json();
   
      const trasnformedMovies = data.results.map(movieData=>{
        return {
          id:movieData.episode_id,
          title: movieData.title,
          openingText: movieData.opening_crawl,
          releaseDate: movieData.release_date
        }
      })
      setMovies(trasnformedMovies)
 
  }
 
  return (
    <React.Fragment>
      <section>
        <button onClick={fetchMoviesHandler}>Fetch Movies</button>
      </section>
      <section>
        <MoviesList movies={movies} />
      </section>
    </React.Fragment>
  );
}
 
export default App;
```

# Firebase 

Firebase: create a back end + database : a full backend app and complete rest API

- Create project
- Real time database
- Create database
- Take the link provided and add something : https://react-http-26861-default-rtdb.firebaseio.com/ + movies.json 

Then you can send GET and POST request to the firebase backend

```javascript
import React, { useState, useEffect, useCallback } from 'react';
 
import MoviesList from './components/MoviesList';
import AddMovie from './components/AddMovie';
import './App.css';
 
function App() {
  const [movies, setMovies] = useState([]);
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState(null);
 
  const fetchMoviesHandler = useCallback(async () => {
    setIsLoading(true);
    setError(null);
    try {
      const response = await fetch('https://react-http-26861-default-rtdb.firebaseio.com/movies.json');
      if (!response.ok) {
        throw new Error('Something went wrong!');
      }
 
      const data = await response.json();
 
      const loadedMovies = [];
 
      for (const key in data) {
        loadedMovies.push({
          id: key,
          title: data[key].title,
          openingText: data[key].openingText,
          releaseDate: data[key].releaseDate,
        });
      }
 
     
      setMovies(loadedMovies);
    } catch (error) {
      setError(error.message);
    }
    setIsLoading(false);
  }, []);
 
  useEffect(() => {
    fetchMoviesHandler();
  }, [fetchMoviesHandler]);
 
  async function addMovieHandler(movie) {
    const response = await fetch('https://react-http-26861-default-rtdb.firebaseio.com/movies.json', {
      method: 'POST',
      body: JSON.stringify(movie),
      headers: {
        'Content-Type': 'application/json'
      }
    });
    const data = await response.json()
    console.log(data)
  }
 
  let content = <p>Found no movies.</p>;
 
  if (movies.length > 0) {
    content = <MoviesList movies={movies} />;
  }
 
  if (error) {
    content = <p>{error}</p>;
  }
 
  if (isLoading) {
    content = <p>Loading...</p>;
  }
 
  return (
    <React.Fragment>
      <section>
        <AddMovie onAddMovie={addMovieHandler} />
      </section>
      <section>
        <button onClick={fetchMoviesHandler}>Fetch Movies</button>
      </section>
      <section>{content}</section>
    </React.Fragment>
  );
}
 
export default App;
 
```
