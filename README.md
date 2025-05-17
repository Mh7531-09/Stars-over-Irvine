<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Stars Over Irvine</title>
  <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600&display=swap" rel="stylesheet" />
  
  <!-- Leaflet CSS -->
  <link
    rel="stylesheet"
    href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"
    integrity="sha256-sA+zA7KfRjHqzBY4wWxFCOAvf2b5H1HUAXt3mZLi9v0="
    crossorigin=""
  />
  
  <style>
    :root {
      --bg: #0d1b2a;
      --bg-card: #16213e;
      --primary: #e94560;
      --text: #f0f0f5;
      --text-muted: #a0a0b5;
    }
    * { box-sizing: border-box; margin: 0; padding: 0; }
    body {
      font-family: 'Poppins', Arial, sans-serif;
      background: var(--bg);
      color: var(--text);
      min-height: 100vh;
      display: flex;
      height: 100vh;
      overflow: hidden;
    }
    .sidebar {
      width: 220px;
      background: var(--bg-card);
      display: flex;
      flex-direction: column;
      padding: 1.5rem 1rem;
      box-shadow: 2px 0 8px rgba(0,0,0,0.3);
    }
    .sidebar button {
      background: none;
      border: none;
      color: var(--text);
      font-size: 1.1rem;
      font-weight: 600;
      padding: 12px 8px;
      margin-bottom: 1rem;
      cursor: pointer;
      border-radius: 6px;
      transition: background-color 0.3s;
      text-align: left;
    }
    .sidebar button:hover,
    .sidebar button.active {
      background-color: var(--primary);
      color: white;
    }
    .content {
      flex: 1;
      overflow-y: auto;
      padding: 2rem 3rem;
      background: var(--bg);
    }
    .container {
      max-width: 900px;
      margin: 0 auto;
    }
    h1 {
      font-size: 2.75rem;
      font-weight: 600;
      margin-bottom: 0.5rem;
      text-align: center;
    }
    .subtitle {
      text-align: center;
      color: var(--text-muted);
      margin-bottom: 2rem;
    }
    h2 {
      color: var(--primary);
      font-size: 1.6rem;
      margin: 2rem 0 0.75rem;
    }
    ul {
      list-style: disc;
      padding-left: 1.25rem;
    }
    ul li {
      margin-bottom: 0.6rem;
    }
    form input {
      width: 100%;
      background: #20334d;
      border: none;
      color: var(--text);
      border-radius: 8px;
      padding: 0.75rem 1rem;
      margin-bottom: 1rem;
      font-size: 1rem;
    }
    form button {
      background: var(--primary);
      border: none;
      color: #fff;
      padding: 0.75rem 1.5rem;
      font-size: 1rem;
      border-radius: 8px;
      cursor: pointer;
      transition: background 0.3s;
    }
    form button:hover {
      background: #d8344f;
    }
    footer {
      text-align: center;
      color: var(--text-muted);
      margin-top: 2.5rem;
      font-size: 0.9rem;
    }
    /* Leaflet map styling */
    #map {
      height: 100%;
      width: 100%;
      border-radius: 12px;
      box-shadow: 0 8px 16px rgba(0,0,0,0.25);
    }
  </style>
</head>
<body>
  <nav class="sidebar">
    <button id="btn-home" class="active">Home</button>
    <button id="btn-map">Map</button>
  </nav>
  
  <main class="content">
    <section id="home-section" class="container">
      <h1>Stars Over Irvine</h1>
      <p class="subtitle">Lighting up the sky</p>

      <h2>Why Dark Skies Matter</h2>
      <p>Light pollution obscures the stars, wastes energy, and disrupts wildlife and human health. Together we can bring back the Milky Way to Irvine!</p>

      <h2>Simple Steps You Can Take</h2>
      <ul>
        <li>Install fully shielded, downward-facing fixtures outdoors.</li>
        <li>Use warm-white LEDs (&lt;3000 K) at the lowest brightness you need.</li>
        <li>Put exterior lights on motion sensors or timers.</li>
        <li>Close curtains at night to prevent indoor light spill.</li>
        <li>Advocate for dark-sky ordinances in your HOA or city council.</li>
      </ul>

      <h2>Get Involved</h2>
      <p>Join our newsletter for local events, night-sky walks, and volunteer projects.</p>
      <form name="newsletter" method="post" netlify>
        <input type="email" name="email" placeholder="Email address" required />
        <button type="submit">Subscribe</button>
      </form>
    </section>
    
    <section id="map-section" style="display:none; height: 100vh;">
      <div id="map"></div>
    </section>
  </main>

  <footer>© 2025 Stars Over Irvine · Built with 💡 for darker nights</footer>

  <!-- Leaflet JS -->
  <script
    src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"
    integrity="sha256-o9N1j7oG4xqoY9B7Fnq+6FxyFC1kJ9A2zY+R7m+IKLc="
    crossorigin=""
  ></script>
  
  <script>
    // Sidebar buttons
    const btnHome = document.getElementById('btn-home');
    const btnMap = document.getElementById('btn-map');
    const homeSection = document.getElementById('home-section');
    const mapSection = document.getElementById('map-section');

    btnHome.addEventListener('click', () => {
      btnHome.classList.add('active');
      btnMap.classList.remove('active');
      homeSection.style.display = 'block';
      mapSection.style.display = 'none';
    });

    btnMap.addEventListener('click', () => {
      btnMap.classList.add('active');
      btnHome.classList.remove('active');
      homeSection.style.display = 'none';
      mapSection.style.display = 'block';
      map.invalidateSize(); // Fix map sizing issues when showing it
    });

    // Initialize Leaflet map
    const map = L.map('map').setView([33.6846, -117.8265], 12); // Coordinates of Irvine, CA

    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
      attribution: '&copy; OpenStreetMap contributors'
    }).addTo(map);

    // Example marker (you can add more or use dynamic data)
    L.marker([33.6846, -117.8265]).addTo(map)
      .bindPopup('Welcome to Stars Over Irvine!')
      .openPopup();
  </script>
</body>
</html>
