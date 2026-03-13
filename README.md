<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>HellmouthCon Adventure</title>
<link href="https://fonts.googleapis.com/css2?family=Creepster&family=UnifrakturMaguntia&family=Raleway:wght@300;400;600&display=swap" rel="stylesheet">
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/leaflet.min.css"/>
<script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/leaflet.min.js"></script>
<style>
  :root {
    --blood: #8b0000;
    --blood-bright: #cc0000;
    --black: #0a0a0a;
    --dark: #111111;
    --panel: #161616;
    --border: #2a0a0a;
    --text: #d4c4b0;
    --muted: #7a6a5a;
    --gold: #c9a84c;
  }
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body {
    background: var(--black);
    color: var(--text);
    font-family: 'Raleway', sans-serif;
    height: 100vh;
    display: flex;
    flex-direction: column;
    overflow: hidden;
  }

  /* HEADER */
  header {
    background: linear-gradient(180deg, #1a0000 0%, #0d0000 100%);
    border-bottom: 2px solid var(--blood);
    padding: 10px 20px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    flex-shrink: 0;
    position: relative;
    overflow: hidden;
  }
  header::before {
    content: '';
    position: absolute; inset: 0;
    background: url("data:image/svg+xml,%3Csvg width='60' height='60' viewBox='0 0 60 60' xmlns='http://www.w3.org/2000/svg'%3E%3Cg fill='none' fill-rule='evenodd'%3E%3Cg fill='%238b0000' fill-opacity='0.08'%3E%3Cpath d='M36 34v-4h-2v4h-4v2h4v4h2v-4h4v-2h-4zm0-30V0h-2v4h-4v2h4v4h2V6h4V4h-4zM6 34v-4H4v4H0v2h4v4h2v-4h4v-2H6zM6 4V0H4v4H0v2h4v4h2V6h4V4H6z'/%3E%3C/g%3E%3C/g%3E%3C/svg%3E");
    pointer-events: none;
  }
  .header-title {
    display: flex;
    flex-direction: column;
    line-height: 1;
  }
  .header-title h1 {
    font-family: 'Creepster', cursive;
    font-size: 2rem;
    color: var(--blood-bright);
    text-shadow: 0 0 20px rgba(200,0,0,0.5), 2px 2px 0 #000;
    letter-spacing: 3px;
  }
  .header-title span {
    font-size: 0.7rem;
    letter-spacing: 4px;
    color: var(--muted);
    text-transform: uppercase;
  }
  .header-stats {
    display: flex;
    gap: 20px;
    font-size: 0.75rem;
  }
  .stat-pill {
    background: rgba(139,0,0,0.15);
    border: 1px solid var(--blood);
    border-radius: 4px;
    padding: 4px 12px;
    text-align: center;
  }
  .stat-pill .num { font-size: 1.2rem; font-weight: 600; color: var(--blood-bright); display: block; }
  .stat-pill .lbl { color: var(--muted); font-size: 0.65rem; text-transform: uppercase; letter-spacing: 1px; }

  /* FILTERS */
  .filter-bar {
    background: var(--panel);
    border-bottom: 1px solid var(--border);
    padding: 8px 16px;
    display: flex;
    gap: 8px;
    align-items: center;
    flex-shrink: 0;
    flex-wrap: wrap;
  }
  .filter-bar span { font-size: 0.7rem; color: var(--muted); text-transform: uppercase; letter-spacing: 1px; margin-right: 4px; }
  .filter-btn {
    padding: 4px 14px;
    border-radius: 20px;
    border: 1px solid;
    cursor: pointer;
    font-size: 0.75rem;
    font-family: 'Raleway', sans-serif;
    transition: all 0.2s;
    background: transparent;
  }
  .filter-btn.active { color: #000 !important; }
  .filter-btn[data-cat="all"] { border-color: #555; color: #aaa; }
  .filter-btn[data-cat="all"].active { background: #aaa; border-color: #aaa; }
  .filter-btn[data-cat="filming"] { border-color: #e74c3c; color: #e74c3c; }
  .filter-btn[data-cat="filming"].active { background: #e74c3c; }
  .filter-btn[data-cat="restaurant"] { border-color: #f39c12; color: #f39c12; }
  .filter-btn[data-cat="restaurant"].active { background: #f39c12; }
  .filter-btn[data-cat="bar"] { border-color: #9b59b6; color: #9b59b6; }
  .filter-btn[data-cat="bar"].active { background: #9b59b6; }
  .filter-btn[data-cat="sightseeing"] { border-color: #2ecc71; color: #2ecc71; }
  .filter-btn[data-cat="sightseeing"].active { background: #2ecc71; }

  .search-input {
    margin-left: auto;
    background: rgba(255,255,255,0.05);
    border: 1px solid var(--border);
    color: var(--text);
    padding: 4px 12px;
    border-radius: 20px;
    font-family: 'Raleway', sans-serif;
    font-size: 0.75rem;
    width: 200px;
    outline: none;
  }
  .search-input:focus { border-color: var(--blood); }
  .search-input::placeholder { color: var(--muted); }

  /* MAIN LAYOUT */
  .main {
    display: flex;
    flex: 1;
    overflow: hidden;
  }

  /* SIDEBAR */
  .sidebar {
    width: 320px;
    min-width: 320px;
    background: var(--panel);
    border-right: 1px solid var(--border);
    display: flex;
    flex-direction: column;
    overflow: hidden;
  }
  .sidebar-header {
    padding: 10px 14px;
    border-bottom: 1px solid var(--border);
    font-size: 0.7rem;
    color: var(--muted);
    text-transform: uppercase;
    letter-spacing: 2px;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  .sidebar-header .count { color: var(--blood-bright); font-weight: 600; }
  .location-list {
    flex: 1;
    overflow-y: auto;
    padding: 6px;
  }
  .location-list::-webkit-scrollbar { width: 4px; }
  .location-list::-webkit-scrollbar-track { background: var(--dark); }
  .location-list::-webkit-scrollbar-thumb { background: var(--blood); border-radius: 2px; }

  .loc-card {
    background: rgba(255,255,255,0.03);
    border: 1px solid var(--border);
    border-radius: 6px;
    padding: 8px 10px;
    margin-bottom: 5px;
    cursor: pointer;
    transition: all 0.15s;
    position: relative;
    overflow: hidden;
  }
  .loc-card::before {
    content: '';
    position: absolute;
    left: 0; top: 0; bottom: 0;
    width: 3px;
  }
  .loc-card[data-cat="filming"]::before { background: #e74c3c; }
  .loc-card[data-cat="restaurant"]::before { background: #f39c12; }
  .loc-card[data-cat="bar"]::before { background: #9b59b6; }
  .loc-card[data-cat="sightseeing"]::before { background: #2ecc71; }
  .loc-card:hover { background: rgba(139,0,0,0.1); border-color: var(--blood); }
  .loc-card.active { background: rgba(139,0,0,0.2); border-color: var(--blood-bright); }
  .loc-card-name {
    font-size: 0.8rem;
    font-weight: 600;
    color: var(--text);
    margin-bottom: 3px;
    padding-left: 6px;
    line-height: 1.3;
  }
  .loc-card-meta {
    display: flex;
    align-items: center;
    gap: 6px;
    padding-left: 6px;
  }
  .cat-badge {
    font-size: 0.6rem;
    padding: 1px 7px;
    border-radius: 10px;
    text-transform: uppercase;
    letter-spacing: 1px;
    font-weight: 600;
  }
  .cat-badge.filming { background: rgba(231,76,60,0.2); color: #e74c3c; border: 1px solid #e74c3c55; }
  .cat-badge.restaurant { background: rgba(243,156,18,0.2); color: #f39c12; border: 1px solid #f39c1255; }
  .cat-badge.bar { background: rgba(155,89,182,0.2); color: #9b59b6; border: 1px solid #9b59b655; }
  .cat-badge.sightseeing { background: rgba(46,204,113,0.2); color: #2ecc71; border: 1px solid #2ecc7155; }
  .visited-check {
    margin-left: auto;
    width: 16px; height: 16px;
    border: 1px solid var(--muted);
    border-radius: 3px;
    cursor: pointer;
    display: flex; align-items: center; justify-content: center;
    font-size: 0.7rem;
    transition: all 0.15s;
    flex-shrink: 0;
  }
  .visited-check.checked { background: var(--blood); border-color: var(--blood-bright); color: #fff; }

  /* ADD FORM */
  .add-section {
    border-top: 1px solid var(--border);
    padding: 10px;
  }
  .add-toggle {
    width: 100%;
    background: rgba(139,0,0,0.15);
    border: 1px solid var(--blood);
    color: var(--blood-bright);
    padding: 7px;
    border-radius: 6px;
    cursor: pointer;
    font-family: 'Raleway', sans-serif;
    font-size: 0.8rem;
    font-weight: 600;
    letter-spacing: 1px;
    text-transform: uppercase;
    transition: all 0.2s;
  }
  .add-toggle:hover { background: rgba(139,0,0,0.3); }
  .add-form { display: none; margin-top: 8px; }
  .add-form.open { display: block; }
  .add-form input, .add-form select {
    width: 100%;
    background: rgba(255,255,255,0.05);
    border: 1px solid var(--border);
    color: var(--text);
    padding: 6px 10px;
    border-radius: 4px;
    font-family: 'Raleway', sans-serif;
    font-size: 0.75rem;
    margin-bottom: 6px;
    outline: none;
  }
  .add-form input:focus, .add-form select:focus { border-color: var(--blood); }
  .add-form select option { background: #1a0000; }
  .add-form input::placeholder { color: var(--muted); }
  .submit-btn {
    width: 100%;
    background: var(--blood);
    border: none;
    color: #fff;
    padding: 7px;
    border-radius: 4px;
    cursor: pointer;
    font-family: 'Raleway', sans-serif;
    font-size: 0.8rem;
    font-weight: 600;
    transition: background 0.2s;
  }
  .submit-btn:hover { background: var(--blood-bright); }

  /* MAP */
  #map {
    flex: 1;
    background: #0a0a0a;
  }

  /* POPUP OVERRIDE */
  .leaflet-popup-content-wrapper {
    background: #1a0505;
    border: 1px solid var(--blood);
    border-radius: 6px;
    color: var(--text);
    font-family: 'Raleway', sans-serif;
    box-shadow: 0 4px 20px rgba(139,0,0,0.4);
  }
  .leaflet-popup-tip { background: var(--blood); }
  .leaflet-popup-content { font-size: 0.8rem; line-height: 1.5; }
  .popup-title { font-weight: 700; font-size: 0.9rem; color: #fff; margin-bottom: 4px; }
  .popup-badge { display: inline-block; margin-bottom: 6px; }
  .popup-desc { color: var(--muted); font-size: 0.75rem; }
  .popup-coords { color: #444; font-size: 0.65rem; margin-top: 4px; }

  /* DETAIL PANEL */
  .detail-panel {
    position: absolute;
    bottom: 20px;
    right: 20px;
    width: 280px;
    background: rgba(20,0,0,0.95);
    border: 1px solid var(--blood);
    border-radius: 8px;
    padding: 14px;
    z-index: 1000;
    display: none;
    box-shadow: 0 4px 30px rgba(139,0,0,0.3);
    backdrop-filter: blur(4px);
  }
  .detail-panel.open { display: block; }
  .detail-close {
    position: absolute; top: 8px; right: 10px;
    background: none; border: none; color: var(--muted);
    cursor: pointer; font-size: 1rem; line-height: 1;
  }
  .detail-close:hover { color: var(--blood-bright); }

  .hidden { display: none !important; }
</style>
</head>
<body>

<header>
  <div class="header-title">
    <h1>☠ HellmouthCon Adventure ☠</h1>
    <span>Los Angeles Horror &amp; Film Location Guide</span>
  </div>
  <div class="header-stats">
    <div class="stat-pill"><span class="num" id="totalCount">0</span><span class="lbl">Locations</span></div>
    <div class="stat-pill"><span class="num" id="visitedCount">0</span><span class="lbl">Visited</span></div>
    <div class="stat-pill"><span class="num" id="filmingCount">0</span><span class="lbl">Film/TV</span></div>
  </div>
</header>

<div class="filter-bar">
  <span>Filter:</span>
  <button class="filter-btn active" data-cat="all">⚰ All</button>
  <button class="filter-btn" data-cat="filming">🎬 Filming</button>
  <button class="filter-btn" data-cat="restaurant">🍽 Restaurant</button>
  <button class="filter-btn" data-cat="bar">🍸 Bar</button>
  <button class="filter-btn" data-cat="sightseeing">👁 Sightseeing</button>
  <input class="search-input" id="searchInput" type="text" placeholder="🔍 Search locations...">
</div>

<div class="main">
  <div class="sidebar">
    <div class="sidebar-header">
      <span>Locations</span>
      <span class="count" id="listCount">0 shown</span>
    </div>
    <div class="location-list" id="locationList"></div>
    <div class="add-section">
      <button class="add-toggle" onclick="toggleForm()">+ Add New Location</button>
      <div class="add-form" id="addForm">
        <input type="text" id="newName" placeholder="Location name *">
        <input type="text" id="newAddress" placeholder="Address or lat,lng *">
        <input type="text" id="newNotes" placeholder="Notes (optional)">
        <select id="newCat">
          <option value="filming">🎬 Filming Location</option>
          <option value="restaurant">🍽 Restaurant</option>
          <option value="bar">🍸 Bar</option>
          <option value="sightseeing">👁 Sightseeing</option>
        </select>
        <button class="submit-btn" onclick="addLocation()">Add to Map</button>
      </div>
    </div>
  </div>

  <div id="map"></div>
</div>

<script>
// ── DATA ──────────────────────────────────────────────────────────────────────
const VEGAN_RESTAURANTS = [
  {name:"Green Leaves Vegan", lat:34.1033964, lng:-118.28764, cat:"restaurant", notes:"100% vegan Thai & American comfort food. Expansive menu, great prices. ⭐ 4.6"},
  {name:"Salaya Plant-Based Kitchen", lat:34.1021059, lng:-118.30326, cat:"restaurant", notes:"100% vegan Thai Town gem — burgers, sushi, ramen, curry & more. ⭐ 4.8"},
  {name:"Doomie's Home Cookin'", lat:34.0940367, lng:-118.327677, cat:"restaurant", notes:"Legendary vegan comfort food — fried chicken, mac & cheese, open late. ⭐ 4.4"},
  {name:"Pura Vita", lat:34.0905005, lng:-118.3695355, cat:"restaurant", notes:"Fully vegan Italian — pasta, pizza, tiramisu. WeHo staple. ⭐ 4.7"},
  {name:"Love Organic Cafe", lat:34.0911294, lng:-118.367858, cat:"restaurant", notes:"Vegan Asian in WeHo — pho, curries, mango sticky rice. ⭐ 4.8"},
  {name:"Variable Cafe", lat:33.9923694, lng:-118.4435786, cat:"restaurant", notes:"Fully vegan — incredible pho, burgers, pasta, carrot cake. ⭐ 4.8"},
  {name:"LA Vegan", lat:33.9925942, lng:-118.42324, cat:"restaurant", notes:"100% vegan Thai — orange chicken, pineapple fried rice, pancakes. ⭐ 4.6"},
  {name:"My Vegan Restaurant", lat:34.134375, lng:-118.14781, cat:"restaurant", notes:"100% vegan Thai in Pasadena — Angry Pork, Pad Thai, mango sticky rice. ⭐ 4.5"},
  {name:"BeeWali's Vegan AF", lat:34.1342954, lng:-118.21529, cat:"restaurant", notes:"100% vegan Mediterranean diner in Eagle Rock. Shawarma, falafel, burgers. ⭐ 4.9"},
  {name:"Othello's Plant Based Kitchen", lat:34.1693894, lng:-118.6021097, cat:"restaurant", notes:"100% vegan — spicy chicken sandwiches, pulled pork, fettuccine alfredo. ⭐ 4.9"},
  {name:"The Vegan Joint", lat:34.166835, lng:-118.587868, cat:"restaurant", notes:"100% vegan Thai-American comfort food — pancakes, burritos, ch'ken sandwich. ⭐ 4.4"},
  {name:"Âu Lạc", lat:34.0562211, lng:-118.2507494, cat:"restaurant", notes:"Beloved DTLA vegan Vietnamese — pho, spring rolls, dumplings, octopus mushroom. ⭐ 4.6"},
  {name:"White Springs Cafe", lat:34.1407078, lng:-118.0281403, cat:"restaurant", notes:"All-vegan Thai in Arcadia — Pad See Ew, curries, thoughtful presentation. ⭐ 4.7"},
  {name:"Garden Fresh Vegan Restaurant", lat:34.0085208, lng:-117.9570952, cat:"restaurant", notes:"Fully vegan Taiwanese/Korean/Chinese — miso ramen, kimchi pancakes, dumplings. ⭐ 4.7"},
];

const RAW = [
  {name:"Shop from The Craft", lat:34.1019, lng:-118.30363, cat:"filming", notes:""},
  {name:"Sunnydale High School", lat:33.830818, lng:-118.3212898, cat:"filming", notes:""},
  {name:"Buffy Summers' House", lat:33.8350272, lng:-118.3228591, cat:"filming", notes:""},
  {name:"UC Sunnydale", lat:34.0728982, lng:-118.4421664, cat:"filming", notes:""},
  {name:"Giles' Apartment", lat:34.1082554, lng:-118.2909456, cat:"filming", notes:""},
  {name:"AHS Murder House / Frat House from Buffy 'Fear Itself'", lat:34.0500842, lng:-118.3172676, cat:"filming", notes:""},
  {name:"Cemetery Scenes, Buffy Season 1", lat:34.0432765, lng:-118.2990097, cat:"sightseeing", notes:""},
  {name:"Buffy: Sunnydale Hospital", lat:34.0382285, lng:-118.2169686, cat:"filming", notes:""},
  {name:"Buffy: Sunnydale City Hall", lat:33.8632989, lng:-118.3270671, cat:"filming", notes:""},
  {name:"Buffy: Kingman's Bluff (Temple of Proserpexa)", lat:33.7119759, lng:-118.2939773, cat:"sightseeing", notes:"Temple of Proserpexa"},
  {name:"Ennis House: Angel's Mansion", lat:34.1162349, lng:-118.2929076, cat:"filming", notes:""},
  {name:"Buffy: Xander & Anya's Apartment", lat:34.058475, lng:-118.324156, cat:"filming", notes:""},
  {name:"Once More With Feeling: Under Your Spell Park Scene", lat:34.012322, lng:-118.3717552, cat:"sightseeing", notes:""},
  {name:"Buffy Desert Scenes: Vasquez Rocks", lat:34.4885168, lng:-118.3214923, cat:"sightseeing", notes:"'Restless,' 'Intervention,' and 'Get It Done'"},
  {name:"Gas Station from Buffy 'Spiral' / House of 1000 Corpses", lat:34.5876607, lng:-117.8715054, cat:"filming", notes:""},
  {name:"Doublemeat Palace", lat:33.9923614, lng:-118.4080351, cat:"filming", notes:""},
  {name:"Buffy: Palmetto Street", lat:34.0402719, lng:-118.2345999, cat:"filming", notes:"Multiple episodes including 'No Place Like Home,' 'Bargaining,' 'Wrecked,' and 'Two to Go'"},
  {name:"Buffy Desert Scenes: Piute Butte", lat:34.6606916, lng:-117.863027, cat:"sightseeing", notes:"Northwest corner of Piute Butte — Buffy's dream encounters"},
  {name:"The Craft: Nancy's New Apartment", lat:33.7659735, lng:-118.1858155, cat:"filming", notes:""},
  {name:"The Craft: Calling the Corners Beach", lat:34.0445508, lng:-118.9406949, cat:"sightseeing", notes:""},
  {name:"The Craft: High School", lat:34.2616257, lng:-118.2984535, cat:"filming", notes:""},
  {name:"The Craft: Sarah's House", lat:34.2518057, lng:-118.3155605, cat:"filming", notes:""},
  {name:"The Craft: Street Preacher and Red Light Scenes", lat:34.0457858, lng:-118.2525542, cat:"filming", notes:""},
  {name:"The Craft: Chris & Sarah Rooftop Scene", lat:34.0772018, lng:-118.2588277, cat:"filming", notes:"Sign is still there!"},
  {name:"The Craft: Watch Out for Those Weirdos Bus Scene", lat:34.2288108, lng:-118.8021933, cat:"filming", notes:"Approximate location"},
  {name:"The Craft: School Chapel Interior", lat:34.067973, lng:-118.2350417, cat:"filming", notes:""},
  {name:"The Craft: House Party Scene", lat:34.1242217, lng:-117.9965747, cat:"filming", notes:""},
  {name:"Hocus Pocus: 'Devil's' House Scene", lat:33.9815722, lng:-118.0336028, cat:"filming", notes:""},
  {name:"Nightmare on Elm Street House", lat:34.0970858, lng:-118.357917, cat:"filming", notes:""},
  {name:"Insidious: Lambert Family House", lat:34.0467399, lng:-118.3304651, cat:"filming", notes:""},
  {name:"Insidious: Lambert Family House #2", lat:34.0548125, lng:-118.3690928, cat:"filming", notes:""},
  {name:"Halloween II: Hospital", lat:34.2440722, lng:-118.4834578, cat:"filming", notes:""},
  {name:"Scream II: Theater", lat:34.098375, lng:-118.2869522, cat:"filming", notes:""},
  {name:"Halloween H20: Academy / Scream 3 Final Scene", lat:34.0947731, lng:-118.2717843, cat:"filming", notes:""},
  {name:"Buffy / Dream Warriors: UC Sunnydale & Dream Warriors Hospital", lat:34.0728552, lng:-118.4421766, cat:"filming", notes:""},
  {name:"Drag Me to Hell: Mansion", lat:34.0301008, lng:-118.2786081, cat:"filming", notes:""},
  {name:"Nightmare on Elm Street: Freddy's Boiler Room", lat:34.0774519, lng:-118.2250545, cat:"filming", notes:""},
  {name:"Nightmare on Elm Street: Chapel from Rod's Funeral", lat:34.041977, lng:-118.2011409, cat:"filming", notes:""},
  {name:"Buffy: Glory's Apartment", lat:34.1439447, lng:-118.1489932, cat:"filming", notes:""},
  {name:"Halloween: Michael Myers' House", lat:34.1161574, lng:-118.1569582, cat:"filming", notes:""},
  {name:"Halloween: Loomis Opens the Gate", lat:34.1265133, lng:-118.3308422, cat:"filming", notes:""},
  {name:"Halloween: Haddonfield Establishing Shot", lat:34.1140832, lng:-118.1427217, cat:"filming", notes:"Camera looking west down Oxley St — famous hedge nearby"},
  {name:"Halloween: Strode House", lat:34.1135357, lng:-118.1555515, cat:"filming", notes:""},
  {name:"Halloween: School", lat:34.101159, lng:-118.1329946, cat:"filming", notes:"Opened Sept 15, 1886 — Laurie's classroom used"},
  {name:"Halloween: 'Speed Kills!'", lat:34.1218479, lng:-118.1554825, cat:"filming", notes:""},
  {name:"Halloween: Hedge Michael Disappears Behind", lat:34.1147688, lng:-118.1430921, cat:"filming", notes:""},
  {name:"Halloween: Pedestal Laurie Sits On", lat:34.1137113, lng:-118.1552652, cat:"filming", notes:""},
  {name:"Halloween: Hardware Store", lat:34.1158998, lng:-118.1572957, cat:"restaurant", notes:"Now an Indian restaurant — book here for lunch!"},
  {name:"Halloween: Wallace House", lat:34.0992596, lng:-118.3607818, cat:"filming", notes:""},
  {name:"Halloween: Tommy Doyle's House", lat:34.0990499, lng:-118.360219, cat:"filming", notes:""},
  {name:"Buffy: The Initiative Frat Residence", lat:34.0496853, lng:-118.3174053, cat:"filming", notes:""},
  {name:"Buffy: The 'New' Sunnydale High School (Post-Graduation)", lat:34.2406756, lng:-118.5300196, cat:"filming", notes:""},
  {name:"Buffy: Griffith Park Merry-Go-Round", lat:34.1326527, lng:-118.2837929, cat:"sightseeing", notes:"Visited by Riley and Dawn in 'Shadow'"},
  {name:"Scream 2: Interior Theater Final Scene", lat:34.0453165, lng:-118.2631005, cat:"filming", notes:""},
  {name:"Scream 2: 'Stab' Premiere Theater", lat:34.0981641, lng:-118.2870922, cat:"filming", notes:""},
  {name:"Scream 3: Cotton Weary's Home", lat:34.0954416, lng:-118.3684685, cat:"filming", notes:""},
  {name:"Scream 2: Cafe Where Randy Explains Sequel Rules", lat:34.0737271, lng:-118.445199, cat:"restaurant", notes:""},
  {name:"Poltergeist House", lat:34.2996736, lng:-118.7112939, cat:"filming", notes:""},
  {name:"The Ring: House Opening Scene", lat:34.0665052, lng:-118.3374971, cat:"filming", notes:""},
  {name:"Drag Me to Hell: Christine's House", lat:34.0707307, lng:-118.2486157, cat:"filming", notes:""},
  {name:"Annabelle: John and Mia's Apartment", lat:34.0593848, lng:-118.3000844, cat:"filming", notes:""},
  {name:"Annabelle: Neighbor / Manson Murders Scene", lat:34.075377, lng:-118.3122058, cat:"filming", notes:""},
  {name:"Birdbox: The Safe House", lat:34.1572121, lng:-117.9964158, cat:"filming", notes:""},
  {name:"Child's Play 2: Andy's Foster House", lat:34.1002216, lng:-118.144108, cat:"filming", notes:""},
  {name:"Elvira, Witchboard, Waxwork & Willard House", lat:34.0624636, lng:-118.3247801, cat:"filming", notes:""},
  {name:"Lost Boys: Bridge Hang Scene", lat:34.4246064, lng:-118.5771789, cat:"filming", notes:""},
  {name:"Paranormal Activity 2: Rey House", lat:34.1815677, lng:-118.6656675, cat:"filming", notes:""},
  {name:"AHS Cult: Ally's House", lat:34.0990499, lng:-118.360219, cat:"filming", notes:""},
  {name:"AHS Cult: The Butchery on Main", lat:33.7883517, lng:-117.8532819, cat:"filming", notes:""},
  {name:"Ouija + Ouija II House", lat:34.1099551, lng:-118.1900298, cat:"filming", notes:""},
  {name:"Matilda's House", lat:33.9563619, lng:-117.9915607, cat:"filming", notes:""},
];

// Add IDs and visited state
let locations = [...RAW, ...VEGAN_RESTAURANTS].map((l, i) => ({...l, id: i, visited: false}));

// ── MAP SETUP ─────────────────────────────────────────────────────────────────
const map = L.map('map', {
  center: [34.05, -118.35],
  zoom: 10,
  zoomControl: true
});

L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
  attribution: '&copy; OpenStreetMap contributors',
  maxZoom: 19
}).addTo(map);

// ── MARKER COLORS ─────────────────────────────────────────────────────────────
const CAT_COLORS = {
  filming: '#e74c3c',
  restaurant: '#f39c12',
  bar: '#9b59b6',
  sightseeing: '#2ecc71'
};
const CAT_ICONS = { filming:'🎬', restaurant:'🍽', bar:'🍸', sightseeing:'👁' };
const CAT_LABELS = { filming:'Filming', restaurant:'Restaurant', bar:'Bar', sightseeing:'Sightseeing' };

function makeIcon(cat, visited, isVeganRestaurant) {
  const c = CAT_COLORS[cat] || '#e74c3c';
  const op = visited ? '0.5' : '1';
  const icon = isVeganRestaurant ? '🍕' : (CAT_ICONS[cat]||'📍');
  const svg = `<svg xmlns='http://www.w3.org/2000/svg' width='28' height='36' viewBox='0 0 28 36'>
    <path d='M14 0C6.3 0 0 6.3 0 14c0 9.8 14 22 14 22s14-12.2 14-22C28 6.3 21.7 0 14 0z' fill='${c}' opacity='${op}'/>
    <circle cx='14' cy='14' r='7' fill='rgba(0,0,0,0.5)'/>
    <text x='14' y='18' text-anchor='middle' font-size='9' fill='white'>${icon}</text>
  </svg>`;
  return L.divIcon({
    html: svg, className: '', iconSize:[28,36], iconAnchor:[14,36], popupAnchor:[0,-36]
  });
}

// ── STATE ─────────────────────────────────────────────────────────────────────
let activeFilter = 'all';
let searchTerm = '';
let markers = {};
let activeCard = null;

// ── RENDER ────────────────────────────────────────────────────────────────────
function getFiltered() {
  return locations.filter(l => {
    const matchCat = activeFilter === 'all' || l.cat === activeFilter;
    const matchSearch = !searchTerm || l.name.toLowerCase().includes(searchTerm);
    return matchCat && matchSearch;
  });
}

function renderAll() {
  renderList();
  renderMarkers();
  updateStats();
}

function renderList() {
  const list = document.getElementById('locationList');
  const filtered = getFiltered();
  document.getElementById('listCount').textContent = filtered.length + ' shown';
  list.innerHTML = '';
  filtered.forEach(l => {
    const div = document.createElement('div');
    div.className = 'loc-card' + (l.visited ? ' visited' : '');
    div.dataset.cat = l.cat;
    div.dataset.id = l.id;
    div.innerHTML = `
      <div class="loc-card-name">${l.name}</div>
      <div class="loc-card-meta">
        <span class="cat-badge ${l.cat}">${CAT_ICONS[l.cat]} ${CAT_LABELS[l.cat]}</span>
        <div class="visited-check ${l.visited?'checked':''}" title="Mark visited" onclick="toggleVisited(event,${l.id})">
          ${l.visited?'✓':''}
        </div>
      </div>`;
    div.addEventListener('click', () => focusLocation(l.id));
    list.appendChild(div);
  });
}

function renderMarkers() {
  // Clear old
  Object.values(markers).forEach(m => map.removeLayer(m));
  markers = {};
  const filtered = getFiltered();
  filtered.forEach(l => {
    const isVeganR = VEGAN_RESTAURANTS.some(v => v.name === l.name);
    const m = L.marker([l.lat, l.lng], {icon: makeIcon(l.cat, l.visited, isVeganR)})
      .addTo(map)
      .bindPopup(`
        <div class="popup-title">${l.name}</div>
        <span class="cat-badge ${l.cat} popup-badge">${CAT_ICONS[l.cat]} ${CAT_LABELS[l.cat]}</span>
        ${l.notes ? `<div class="popup-desc">${l.notes}</div>` : ''}
        <div class="popup-coords">${l.lat.toFixed(4)}, ${l.lng.toFixed(4)}</div>
      `, {maxWidth: 240});
    m.on('click', () => highlightCard(l.id));
    markers[l.id] = m;
  });
}

function updateStats() {
  document.getElementById('totalCount').textContent = locations.length;
  document.getElementById('visitedCount').textContent = locations.filter(l=>l.visited).length;
  document.getElementById('filmingCount').textContent = locations.filter(l=>l.cat==='filming').length;
}

function focusLocation(id) {
  const l = locations.find(x => x.id === id);
  if (!l) return;
  map.setView([l.lat, l.lng], 15, {animate: true});
  if (markers[id]) markers[id].openPopup();
  highlightCard(id);
}

function highlightCard(id) {
  document.querySelectorAll('.loc-card').forEach(c => c.classList.remove('active'));
  const card = document.querySelector(`.loc-card[data-id="${id}"]`);
  if (card) {
    card.classList.add('active');
    card.scrollIntoView({behavior:'smooth', block:'nearest'});
  }
}

function toggleVisited(e, id) {
  e.stopPropagation();
  const l = locations.find(x => x.id === id);
  if (l) {
    l.visited = !l.visited;
    renderAll();
  }
}

// ── FILTERS ───────────────────────────────────────────────────────────────────
document.querySelectorAll('.filter-btn').forEach(btn => {
  btn.addEventListener('click', () => {
    document.querySelectorAll('.filter-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    activeFilter = btn.dataset.cat;
    renderAll();
  });
});

document.getElementById('searchInput').addEventListener('input', e => {
  searchTerm = e.target.value.toLowerCase().trim();
  renderAll();
});

// ── ADD FORM ──────────────────────────────────────────────────────────────────
function toggleForm() {
  const f = document.getElementById('addForm');
  f.classList.toggle('open');
}

async function addLocation() {
  const name = document.getElementById('newName').value.trim();
  const address = document.getElementById('newAddress').value.trim();
  const notes = document.getElementById('newNotes').value.trim();
  const cat = document.getElementById('newCat').value;
  if (!name || !address) { alert('Name and address are required.'); return; }

  let lat, lng;
  // Check if coords entered directly
  const coordMatch = address.match(/^(-?\d+\.?\d*)\s*,\s*(-?\d+\.?\d*)$/);
  if (coordMatch) {
    lat = parseFloat(coordMatch[1]);
    lng = parseFloat(coordMatch[2]);
  } else {
    // Geocode via Nominatim
    try {
      const r = await fetch(`https://nominatim.openstreetmap.org/search?format=json&q=${encodeURIComponent(address)}&limit=1`);
      const data = await r.json();
      if (!data.length) { alert('Address not found. Try a more specific address or enter lat,lng directly.'); return; }
      lat = parseFloat(data[0].lat);
      lng = parseFloat(data[0].lon);
    } catch(e) { alert('Geocoding failed. Try entering coordinates as lat,lng instead.'); return; }
  }

  const newLoc = { name, lat, lng, cat, notes, id: Date.now(), visited: false };
  locations.push(newLoc);
  document.getElementById('newName').value = '';
  document.getElementById('newAddress').value = '';
  document.getElementById('newNotes').value = '';
  document.getElementById('addForm').classList.remove('open');
  renderAll();
  setTimeout(() => focusLocation(newLoc.id), 300);
}

// ── INIT ──────────────────────────────────────────────────────────────────────
renderAll();
</script>
</body>
</html>
