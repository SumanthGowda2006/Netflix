<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Netflix UI with Hover + Search</title>
  <style>
    body {
      margin: 0;
      font-family: 'Helvetica Neue', sans-serif;
      background-color: #141414;
      color: white;
    }

    header {
      background-color: #000;
      padding: 1rem 2rem;
      display: flex;
      align-items: center;
      justify-content: space-between;
      flex-wrap: wrap;
    }

    header h1 {
      color: #e50914;
      margin: 0;
      font-size: 1.8rem;
    }

    .search-bar input {
      padding: 8px 12px;
      font-size: 16px;
      border-radius: 5px;
      border: none;
      outline: none;
    }

    .container {
      display: flex;
      padding: 20px;
    }

    .carousel-container {
      flex: 3;
    }

    .carousel {
      margin-bottom: 2rem;
    }

    .carousel h2 {
      margin-bottom: 10px;
    }

    .scrolling-wrapper {
      display: flex;
      overflow-x: auto;
      gap: 10px;
      padding-bottom: 10px;
      flex-wrap: nowrap;
    }

    .movie-card {
      flex: 0 0 auto;
      width: 200px;
      height: 300px;
      background: #222;
      border-radius: 8px;
      position: relative;
      overflow: hidden;
      cursor: grab;
    }

    .movie-card img,
    .movie-card video {
      width: 100%;
      height: 100%;
      object-fit: cover;
      display: block;
    }

    .movie-card video {
      position: absolute;
      top: 0;
      left: 0;
      display: none;
      z-index: 2;
    }

    .movie-card:hover video {
      display: block;
    }

    .watch-later {
      flex: 1;
      margin-left: 20px;
      background: #222;
      padding: 20px;
      border-radius: 10px;
      min-height: 300px;
    }

    .watch-later h3 {
      margin-top: 0;
    }

    .watch-later .movie-title {
      background: #444;
      margin-bottom: 10px;
      padding: 8px;
      border-radius: 5px;
    }

    @media (max-width: 768px) {
      .container {
        flex-direction: column;
      }
      .watch-later {
        margin-left: 0;
        margin-top: 20px;
      }
    }
  </style>
</head>
<body>

  <header>
    <h1>Netflix</h1>
    <div class="search-bar">
      <input type="text" id="searchInput" placeholder="Search movies..." />
    </div>
  </header>

  <div class="container">
    <div class="carousel-container">
      <div class="carousel">
        <h2>Trending Now</h2>
        <div class="scrolling-wrapper" id="movieList"></div>
      </div>
    </div>

    <div class="watch-later" id="watchLater">
      <h3>📌 Watch Later</h3>
    </div>
  </div>

<script>
  const movies = [
    {
      title: "Stranger Things",
      image: "https://image.tmdb.org/t/p/w500/49WJfeN0moxb9IPfGn8AIqMGskD.jpg",
      genre: "Sci-Fi",
      trailer: "https://www.w3schools.com/html/mov_bbb.mp4"
    },
    {
      title: "Breaking Bad",
      image: "https://image.tmdb.org/t/p/w500/eSzpy96DwBujGFj0xMbXBcGcfxX.jpg",
      genre: "Crime",
      trailer: "https://www.w3schools.com/html/movie.mp4"
    },
    {
      title: "The Witcher",
      image: "https://image.tmdb.org/t/p/w500/zrPpUlehQaBf8YX2NrVrKK8IEpf.jpg",
      genre: "Fantasy",
      trailer: "https://www.w3schools.com/html/mov_bbb.mp4"
    },
    {
      title: "Money Heist",
      image: "https://image.tmdb.org/t/p/w500/reEMJA1uzscCbkpeRJeTT2bjqUp.jpg",
      genre: "Action",
      trailer: "https://www.w3schools.com/html/movie.mp4"
    },
    {
      title: "You",
      image: "https://image.tmdb.org/t/p/w500/pYcEeO1d0EqAR9epCapzPbdtWgP.jpg",
      genre: "Thriller",
      trailer: "https://www.w3schools.com/html/mov_bbb.mp4"
    },
    {
      title: "The Crown",
      image: "https://image.tmdb.org/t/p/w500/4jE6zxmj1CSDF3mybM2GmOBxu7m.jpg",
      genre: "Drama",
      trailer: "https://www.w3schools.com/html/movie.mp4"
    },
    {
      title: "Narcos",
      image: "https://image.tmdb.org/t/p/w500/rTmal9fDbwh5F0waol2hq35U4ah.jpg",
      genre: "Crime",
      trailer: "https://www.w3schools.com/html/mov_bbb.mp4"
    },
    {
      title: "Lupin",
      image: "https://image.tmdb.org/t/p/w500/ga8R3OiOMMgSvZ4cOj8x7prUNYZ.jpg",
      genre: "Mystery",
      trailer: "https://www.w3schools.com/html/movie.mp4"
    }
  ];

  const movieList = document.getElementById('movieList');
  const watchLater = document.getElementById('watchLater');
  const searchInput = document.getElementById('searchInput');

  let saved = JSON.parse(localStorage.getItem('watchLaterList')) || [];

  saved.forEach(title => addToWatchLater(title));

  function renderMovies(filter = "") {
    movieList.innerHTML = "";
    const filtered = movies.filter(movie =>
      movie.title.toLowerCase().includes(filter.toLowerCase())
    );

    filtered.forEach(movie => {
      const card = createMovieCard(movie);
      movieList.appendChild(card);
    });
  }

  function createMovieCard(movie) {
    const card = document.createElement('div');
    card.className = 'movie-card';
    card.draggable = true;
    card.dataset.title = movie.title;

    const img = document.createElement('img');
    img.src = movie.image;

    const video = document.createElement('video');
    video.src = movie.trailer;
    video.muted = true;
    video.autoplay = true;
    video.loop = true;

    card.appendChild(img);
    card.appendChild(video);

    // 🔔 Click to show subscription alert
    card.addEventListener('click', () => {
      alert(`⚠️ Take Subscription to watch "${movie.title}"!`);
    });

    card.addEventListener('dragstart', e => {
      e.dataTransfer.setData('text/plain', movie.title);
    });

    return card;
  }

  function addToWatchLater(title) {
    const div = document.createElement('div');
    div.className = 'movie-title';
    div.textContent = title;

    // 🗑️ Click to remove from watchlist
    div.addEventListener('click', () => {
      const confirmDelete = confirm(`Remove "${title}" from your watchlist?`);
      if (confirmDelete) {
        saved = saved.filter(t => t !== title);
        localStorage.setItem('watchLaterList', JSON.stringify(saved));
        div.remove();
      }
    });

    watchLater.appendChild(div);
  }

  watchLater.addEventListener('dragover', e => e.preventDefault());

  watchLater.addEventListener('drop', e => {
    e.preventDefault();
    const title = e.dataTransfer.getData('text/plain');
    if (!saved.includes(title)) {
      saved.push(title);
      localStorage.setItem('watchLaterList', JSON.stringify(saved));
      addToWatchLater(title);
    }
  });

  searchInput.addEventListener('input', () => {
    renderMovies(searchInput.value);
  });

  renderMovies();
</script>


</body>
</html>













<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Netflix UI with Hover + Search</title>
  <style>
    body {
      margin: 0;
      font-family: 'Helvetica Neue', sans-serif;
      background-color: #141414;
      color: white;
    }

    header {
      background-color: #000;
      padding: 1rem 2rem;
      display: flex;
      align-items: center;
      justify-content: space-between;
      flex-wrap: wrap;
    }

    header h1 {
      color: #e50914;
      margin: 0;
      font-size: 1.8rem;
    }

    .nav-links {
      display: flex;
      gap: 15px;
    }

    .nav-links a {
      color: white;
      text-decoration: none;
      font-size: 16px;
    }

    .nav-links a:hover {
      color: #e50914;
    }

    .search-bar input {
      padding: 8px 12px;
      font-size: 16px;
      border-radius: 5px;
      border: none;
      outline: none;
    }

    .container {
      display: flex;
      padding: 20px;
    }

    .carousel-container {
      flex: 3;
    }

    .carousel {
      margin-bottom: 2rem;
    }

    .carousel h2 {
      margin-bottom: 10px;
    }

    .scrolling-wrapper {
      display: flex;
      overflow-x: auto;
      gap: 10px;
      padding-bottom: 10px;
      flex-wrap: nowrap;
    }

    .movie-card {
      flex: 0 0 auto;
      width: 200px;
      height: 300px;
      background: #222;
      border-radius: 8px;
      position: relative;
      overflow: hidden;
      cursor: grab;
    }

    .movie-card img,
    .movie-card video {
      width: 100%;
      height: 100%;
      object-fit: cover;
      display: block;
    }

    .movie-card video {
      position: absolute;
      top: 0;
      left: 0;
      display: none;
      z-index: 2;
    }

    .movie-card:hover video {
      display: block;
    }

    .movie-card .add-to-watchlist {
      position: absolute;
      bottom: 10px;
      left: 10px;
      background-color: #e50914;
      color: white;
      padding: 5px 10px;
      border-radius: 5px;
      cursor: pointer;
      display: block;
    }

    .movie-card .added {
      background-color: #3e3e3e;
      cursor: not-allowed;
    }

    .watch-later {
      flex: 1;
      margin-left: 20px;
      background: #222;
      padding: 20px;
      border-radius: 10px;
      min-height: 300px;
    }

    .watch-later h3 {
      margin-top: 0;
    }

    .watch-later .movie-title {
      background: #444;
      margin-bottom: 10px;
      padding: 8px;
      border-radius: 5px;
      display: flex;
      justify-content: space-between;
      align-items: center;
    }

    .watch-later .delete-button {
      background-color: #e50914;
      color: white;
      border: none;
      padding: 5px 10px;
      border-radius: 5px;
      cursor: pointer;
    }

    .watch-later .delete-button:hover {
      background-color: #b40711;
    }

    /* Login Form Styles */
    .login-form {
      display: none;
      background-color: #222;
      padding: 20px;
      border-radius: 10px;
      max-width: 400px;
      margin: 0 auto;
    }

    .login-form input {
      width: 100%;
      padding: 10px;
      margin-bottom: 15px;
      border: 1px solid #444;
      border-radius: 5px;
      background-color: #333;
      color: white;
    }

    .login-form button {
      width: 100%;
      padding: 10px;
      background-color: #e50914;
      color: white;
      border: none;
      border-radius: 5px;
      cursor: pointer;
    }

    .login-form button:hover {
      background-color: #b40711;
    }

    /* Profile Styles */
    .profile-section {
      display: none;
      background-color: #222;
      padding: 20px;
      border-radius: 10px;
      max-width: 400px;
      margin: 0 auto;
    }

    .profile-section h2 {
      margin: 0;
      font-size: 1.8rem;
    }

    .profile-section p {
      font-size: 1.1rem;
      margin: 10px 0;
    }

    .profile-section button {
      padding: 10px;
      background-color: #e50914;
      color: white;
      border: none;
      border-radius: 5px;
      cursor: pointer;
    }

    .profile-section button:hover {
      background-color: #b40711;
    }

    @media (max-width: 768px) {
      .container {
        flex-direction: column;
      }
      .watch-later {
        margin-left: 0;
        margin-top: 20px;
      }
    }
  </style>
</head>
<body>

  <header>
    <h1>Netflix</h1>
    <div class="nav-links">
      <a href="#" id="homeLink">Home</a>
      <a href="#" id="profileLink">Profile</a>
      <a href="#" id="loginLink">Login</a>
    </div>
    <div class="search-bar">
      <input type="text" id="searchInput" placeholder="Search movies..." />
    </div>
  </header>

  <!-- Login Form -->
  <div class="login-form" id="loginForm">
    <h2>Login</h2>
    <input type="email" id="email" placeholder="Enter your email" required />
    <input type="password" id="password" placeholder="Enter your password" required />
    <button id="loginButton">Login</button>
  </div>

  <!-- Profile Section -->
  <div class="profile-section" id="profileSection">
    <h2>User Profile</h2>
    <p><strong>Name:</strong> John Doe</p>
    <p><strong>Email:</strong> johndoe@example.com</p>
    <button id="logoutButton">Logout</button>
  </div>

  <div class="container">
    <div class="carousel-container">
      <div class="carousel">
        <h2>Trending Now</h2>
        <div class="scrolling-wrapper" id="movieList"></div>
      </div>
    </div>

    <div class="watch-later" id="watchLater">
      <h3>📌 Watch Later</h3>
    </div>
  </div>

<script>
  const movies = [
    {
      title: "Stranger Things",
      image: "https://image.tmdb.org/t/p/w500/49WJfeN0moxb9IPfGn8AIqMGskD.jpg",
      genre: "Sci-Fi",
      trailer: "https://www.w3schools.com/html/mov_bbb.mp4"
    },
    {
      title: "Breaking Bad",
      image: "https://image.tmdb.org/t/p/w500/eSzpy96DwBujGFj0xMbXBcGcfxX.jpg",
      genre: "Crime",
      trailer: "https://www.w3schools.com/html/movie.mp4"
    },
    {
      title: "The Witcher",
      image: "https://image.tmdb.org/t/p/w500/zrPpUlehQaBf8YX2NrVrKK8IEpf.jpg",
      genre: "Fantasy",
      trailer: "https://www.w3schools.com/html/mov_bbb.mp4"
    },
    {
      title: "Money Heist",
      image: "https://image.tmdb.org/t/p/w500/reEMJA1uzscCbkpeRJeTT2bjqUp.jpg",
      genre: "Action",
      trailer: "https://www.w3schools.com/html/movie.mp4"
    },
    {
      title: "You",
      image: "https://image.tmdb.org/t/p/w500/pYcEeO1d0EqAR9epCapzPbdtWgP.jpg",
      genre: "Thriller",
      trailer: "https://www.w3schools.com/html/mov_bbb.mp4"
    },
    {
      title: "The Crown",
      image: "https://image.tmdb.org/t/p/w500/4jE6zxmj1CSDF3mybM2GmOBxu7m.jpg",
      genre: "Drama",
      trailer: "https://www.w3schools.com/html/movie.mp4"
    },
    {
      title: "Narcos",
      image: "https://image.tmdb.org/t/p/w500/rTmal9fDbwh5F0waol2hq35U4ah.jpg",
      genre: "Crime",
      trailer: "https://www.w3schools.com/html/mov_bbb.mp4"
    },
    {
      title: "Lupin",
      image: "https://image.tmdb.org/t/p/w500/ga8R3OiOMMgSvZ4cOj8x7prUNYZ.jpg",
      genre: "Mystery",
      trailer: "https://www.w3schools.com/html/movie.mp4"
    }
  ];

  const movieList = document.getElementById('movieList');
  const watchLater = document.getElementById('watchLater');
  const searchInput = document.getElementById('searchInput');
  const loginForm = document.getElementById('loginForm');
  const profileSection = document.getElementById('profileSection');
  const loginLink = document.getElementById('loginLink');
  const profileLink = document.getElementById('profileLink');
  const loginButton = document.getElementById('loginButton');
  const logoutButton = document.getElementById('logoutButton');
  const homeLink = document.getElementById('homeLink');

  let isLoggedIn = false;
  let watchlist = [];

  function toggleLoginForm(show) {
    loginForm.style.display = show ? 'block' : 'none';
    profileSection.style.display = 'none';
  }

  function toggleProfileSection(show) {
    profileSection.style.display = show ? 'block' : 'none';
    loginForm.style.display = 'none';
  }

  function handleLogin() {
    const email = document.getElementById('email').value;
    const password = document.getElementById('password').value;
    if (email && password) {
      isLoggedIn = true;
      localStorage.setItem('loggedIn', 'true');
      toggleProfileSection(true);
    }
  }

  function handleLogout() {
    isLoggedIn = false;
    localStorage.removeItem('loggedIn');
    toggleLoginForm(true);
  }

  // Set initial state based on login status
  if (localStorage.getItem('loggedIn')) {
    isLoggedIn = true;
    toggleProfileSection(true);
  } else {
    toggleLoginForm(true);
  }

  // Event Listeners
  loginButton.addEventListener('click', handleLogin);
  logoutButton.addEventListener('click', handleLogout);

  loginLink.addEventListener('click', () => toggleLoginForm(true));
  profileLink.addEventListener('click', () => toggleProfileSection(true));
  homeLink.addEventListener('click', () => {
    if (isLoggedIn) {
      toggleProfileSection(true);
    } else {
      toggleLoginForm(true);
    }
  });

  // Render Movies
  function renderMovies(filter = "") {
    movieList.innerHTML = "";
    const filtered = movies.filter(movie =>
      movie.title.toLowerCase().includes(filter.toLowerCase())
    );

    filtered.forEach(movie => {
      const card = createMovieCard(movie);
      movieList.appendChild(card);
    });
  }

  function createMovieCard(movie) {
    const card = document.createElement('div');
    card.className = 'movie-card';
    card.dataset.title = movie.title;

    const img = document.createElement('img');
    img.src = movie.image;

    const video = document.createElement('video');
    video.src = movie.trailer;
    video.muted = true;
    video.loop = true;

    const addButton = document.createElement('button');
    addButton.className = 'add-to-watchlist';
    addButton.textContent = 'Add to Watchlist';

    card.appendChild(img);
    card.appendChild(video);
    card.appendChild(addButton);

    addButton.addEventListener('click', () => addToWatchlist(movie, card));

    return card;
  }

  function addToWatchlist(movie, card) {
    // Check if movie is already in watchlist
    if (!watchlist.includes(movie.title)) {
      watchlist.push(movie.title);

      const movieCard = document.createElement('div');
      movieCard.className = 'movie-title';
      movieCard.textContent = movie.title;

      const deleteButton = document.createElement('button');
      deleteButton.className = 'delete-button';
      deleteButton.textContent = 'Delete';
      movieCard.appendChild(deleteButton);

      deleteButton.addEventListener('click', () => {
        watchLater.removeChild(movieCard);
        watchlist = watchlist.filter(item => item !== movie.title);
        card.querySelector('.add-to-watchlist').disabled = false;
        card.querySelector('.add-to-watchlist').textContent = 'Add to Watchlist';
      });

      watchLater.appendChild(movieCard);
      card.querySelector('.add-to-watchlist').disabled = true;
      card.querySelector('.add-to-watchlist').textContent = 'Added';
    }
  }

  // Search functionality
  searchInput.addEventListener('input', (e) => {
    renderMovies(e.target.value);
  });

  // Initial render
  renderMovies();
</script>

</body>
</html>





















<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Netflix UI with Hover + Search</title>
  <style>
    body {
      margin: 0;
      font-family: 'Helvetica Neue', sans-serif;
      background-color: #141414;
      color: white;
    }

    header {
      background-color: #000;
      padding: 1rem 2rem;
      display: flex;
      align-items: center;
      justify-content: space-between;
      flex-wrap: wrap;
    }

    header h1 {
      color: #e50914;
      margin: 0;
      font-size: 1.8rem;
    }

    .search-bar input {
      padding: 8px 12px;
      font-size: 16px;
      border-radius: 5px;
      border: none;
      outline: none;
    }

    .container {
      display: flex;
      padding: 20px;
    }

    .carousel-container {
      flex: 3;
    }

    .carousel {
      margin-bottom: 2rem;
    }

    .carousel h2 {
      margin-bottom: 10px;
    }

    .scrolling-wrapper {
      display: flex;
      overflow-x: auto;
      gap: 10px;
      padding-bottom: 10px;
      flex-wrap: nowrap;
    }

    .movie-card {
      flex: 0 0 auto;
      width: 200px;
      height: 300px;
      background: #222;
      border-radius: 8px;
      position: relative;
      overflow: hidden;
      cursor: grab;
    }

    .movie-card img,
    .movie-card video {
      width: 100%;
      height: 100%;
      object-fit: cover;
      display: block;
    }

    .movie-card video {
      position: absolute;
      top: 0;
      left: 0;
      display: none;
      z-index: 2;
    }

    .movie-card:hover video {
      display: block;
    }

    .watch-later {
      flex: 1;
      margin-left: 20px;
      background: #222;
      padding: 20px;
      border-radius: 10px;
      min-height: 300px;
    }

    .watch-later h3 {
      margin-top: 0;
    }

    .watch-later .movie-title {
      background: #444;
      margin-bottom: 10px;
      padding: 8px;
      border-radius: 5px;
    }

    .profile-form,
    .login-form {
      display: none;
      background: #333;
      padding: 20px;
      border-radius: 10px;
      width: 300px;
      margin: 20px auto;
    }

    .profile-form input,
    .login-form input {
      width: 100%;
      padding: 8px;
      margin: 10px 0;
      border-radius: 5px;
      border: none;
      background-color: #444;
      color: white;
    }

    .profile-form button,
    .login-form button {
      background-color: #e50914;
      color: white;
      padding: 10px;
      width: 100%;
      border: none;
      border-radius: 5px;
      cursor: pointer;
    }

    .profile-form button:disabled,
    .login-form button:disabled {
      background-color: #999;
    }

    .profile-header {
      margin-top: 20px;
      text-align: center;
      font-size: 1.5rem;
    }

    @media (max-width: 768px) {
      .container {
        flex-direction: column;
      }
      .watch-later {
        margin-left: 0;
        margin-top: 20px;
      }
    }
  </style>
</head>
<body>

  <header>
    <h1>Netflix</h1>
    <div class="search-bar">
      <input type="text" id="searchInput" placeholder="Search movies..." />
    </div>
  </header>

  <!-- Profile Form -->
  <div class="profile-form" id="profileForm">
    <h3>Update Profile</h3>
    <input type="text" id="profileName" placeholder="Name" />
    <input type="email" id="profileEmail" placeholder="Email" />
    <button id="saveProfileBtn">Save Profile</button>
  </div>

  <!-- Login Form -->
  <div class="login-form" id="loginForm">
    <h3>Login</h3>
    <input type="text" id="username" placeholder="Username" />
    <input type="password" id="password" placeholder="Password" />
    <button id="loginBtn">Login</button>
    <p>Don't have an account? <span id="registerLink" style="cursor: pointer; color: #e50914;">Register</span></p>
  </div>

  <div class="container">
    <div class="carousel-container">
      <div class="carousel">
        <h2>Trending Now</h2>
        <div class="scrolling-wrapper" id="movieList"></div>
      </div>
    </div>

    <div class="watch-later" id="watchLater">
      <h3>📌 Watch Later</h3>
    </div>
  </div>

<script>
  const movies = [
    {
      title: "Stranger Things",
      image: "https://image.tmdb.org/t/p/w500/49WJfeN0moxb9IPfGn8AIqMGskD.jpg",
      genre: "Sci-Fi",
      trailer: "https://www.w3schools.com/html/mov_bbb.mp4"
    },
    {
      title: "Breaking Bad",
      image: "https://image.tmdb.org/t/p/w500/eSzpy96DwBujGFj0xMbXBcGcfxX.jpg",
      genre: "Crime",
      trailer: "https://www.w3schools.com/html/movie.mp4"
    },
    {
      title: "The Witcher",
      image: "https://image.tmdb.org/t/p/w500/zrPpUlehQaBf8YX2NrVrKK8IEpf.jpg",
      genre: "Fantasy",
      trailer: "https://www.w3schools.com/html/mov_bbb.mp4"
    },
    {
      title: "Money Heist",
      image: "https://image.tmdb.org/t/p/w500/reEMJA1uzscCbkpeRJeTT2bjqUp.jpg",
      genre: "Action",
      trailer: "https://www.w3schools.com/html/movie.mp4"
    },
    {
      title: "You",
      image: "https://image.tmdb.org/t/p/w500/pYcEeO1d0EqAR9epCapzPbdtWgP.jpg",
      genre: "Thriller",
      trailer: "https://www.w3schools.com/html/mov_bbb.mp4"
    },
    {
      title: "The Crown",
      image: "https://image.tmdb.org/t/p/w500/4jE6zxmj1CSDF3mybM2GmOBxu7m.jpg",
      genre: "Drama",
      trailer: "https://www.w3schools.com/html/movie.mp4"
    },
    {
      title: "Narcos",
      image: "https://image.tmdb.org/t/p/w500/rTmal9fDbwh5F0waol2hq35U4ah.jpg",
      genre: "Crime",
      trailer: "https://www.w3schools.com/html/mov_bbb.mp4"
    },
    {
      title: "Lupin",
      image: "https://image.tmdb.org/t/p/w500/ga8R3OiOMMgSvZ4cOj8x7prUNYZ.jpg",
      genre: "Mystery",
      trailer: "https://www.w3schools.com/html/movie.mp4"
    }
  ];

  const movieList = document.getElementById('movieList');
  const watchLater = document.getElementById('watchLater');
  const searchInput = document.getElementById('searchInput');
  const profileForm = document.getElementById('profileForm');
  const loginForm = document.getElementById('loginForm');
  const saveProfileBtn = document.getElementById('saveProfileBtn');
  const loginBtn = document.getElementById('loginBtn');
  const registerLink = document.getElementById('registerLink');

  let saved = JSON.parse(localStorage.getItem('watchLaterList')) || [];
  let loggedInUser = JSON.parse(localStorage.getItem('loggedInUser')) || null;

  function renderMovies(filter = "") {
    movieList.innerHTML = "";
    const filtered = movies.filter(movie =>
      movie.title.toLowerCase().includes(filter.toLowerCase())
    );

    filtered.forEach(movie => {
      const card = createMovieCard(movie);
      movieList.appendChild(card);
    });
  }

  function createMovieCard(movie) {
    const card = document.createElement('div');
    card.className = 'movie-card';
    card.draggable = true;
    card.dataset.title = movie.title;

    const img = document.createElement('img');
    img.src = movie.image;

    const video = document.createElement('video');
    video.src = movie.trailer;
    video.muted = true;
    video.autoplay = true;
    video.loop = true;

    card.appendChild(img);
    card.appendChild(video);

    card.addEventListener('click', () => {
      alert(`⚠️ Take Subscription to watch "${movie.title}"!`);
    });

    card.addEventListener('dragstart', e => {
      e.dataTransfer.setData('text/plain', movie.title);
    });

    return card;
  }

  function addToWatchLater(title) {
    const div = document.createElement('div');
    div.className = 'movie-title';
    div.textContent = title;

    div.addEventListener('click', () => {
      const confirmDelete = confirm(`Remove "${title}" from your watchlist?`);
      if (confirmDelete) {
        saved = saved.filter(t => t !== title);
        localStorage.setItem('watchLaterList', JSON.stringify(saved));
        div.remove();
      }
    });

    watchLater.appendChild(div);
  }

  if (loggedInUser) {
    profileForm.style.display = 'block';
    loginForm.style.display = 'none';
    document.querySelector('.profile-header').textContent = `Welcome, ${loggedInUser.username}`;
  } else {
    profileForm.style.display = 'none';
    loginForm.style.display = 'block';
  }

  saveProfileBtn.addEventListener('click', () => {
    const name = document.getElementById('profileName').value;
    const email = document.getElementById('profileEmail').value;

    if (name && email) {
      loggedInUser.name = name;
      loggedInUser.email = email;
      localStorage.setItem('loggedInUser', JSON.stringify(loggedInUser));
      alert('Profile updated!');
    } else {
      alert('Please fill in both fields!');
    }
  });

  loginBtn.addEventListener('click', () => {
    const username = document.getElementById('username').value;
    const password = document.getElementById('password').value;

    if (username && password) {
      loggedInUser = { username, password };
      localStorage.setItem('loggedInUser', JSON.stringify(loggedInUser));
      alert('Logged in successfully!');
      profileForm.style.display = 'block';
      loginForm.style.display = 'none';
      renderMovies();
    } else {
      alert('Please enter a valid username and password.');
    }
  });

  registerLink.addEventListener('click', () => {
    alert('Registration feature coming soon!');
  });

  renderMovies();
</script>

</body>
</html>
