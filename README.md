# Asya-4
<!DOCTYPE html>
<html lang="tr">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Asya Ülkeleri Harita Projesi</title>
  <style>
    body { margin: 0; overflow: hidden; font-family: sans-serif; }
    #infoBox {
      position: absolute;
      top: 10px;
      left: 10px;
      background: rgba(255,255,255,0.95);
      padding: 15px;
      border-radius: 8px;
      box-shadow: 0 0 10px rgba(0,0,0,0.3);
      max-width: 300px;
      display: none;
    }
    h2 { margin-top: 0; font-size: 18px; }
    p { margin: 4px 0; font-size: 14px; }
  </style>
</head>
<body>
  <div id="infoBox">
    <h2 id="countryName"></h2>
    <p><strong>Kuruluş:</strong> <span id="founding"></span></p>
    <p><strong>Başkent:</strong> <span id="capital"></span></p>
    <p><strong>Nüfus:</strong> <span id="population"></span></p>
    <p><strong>İklim:</strong> <span id="climate"></span></p>
    <p><strong>Yeraltı Kaynakları:</strong> <span id="resources"></span></p>
    <p><strong>Doğal Güzellikler:</strong> <span id="natural"></span></p>
    <p><strong>Tarihi Mekanlar:</strong> <span id="history"></span></p>
  </div>

  <script src="https://unpkg.com/three@0.152.2/build/three.min.js"></script>
  <script src="https://unpkg.com/globe.gl"></script>
  <script src="https://unpkg.com/topojson@3"></script>

  <script>
    const countryDetails = {
      "China": {
        founding: "1 Ekim 1949",
        capital: "Pekin",
        population: "1.4 milyar",
        climate: "Karasal, Muson",
        resources: "Kömür, demir, nadir toprak elementleri",
        natural: "Yangtze Nehri, Guilin",
        history: "Çin Seddi, Yasak Şehir"
      },
      "India": {
        founding: "15 Ağustos 1947",
        capital: "Yeni Delhi",
        population: "1.4 milyar",
        climate: "Tropikal, Muson",
        resources: "Kömür, demir cevheri, boksit",
        natural: "Himalayalar, Kerala Backwaters",
        history: "Tac Mahal, Varanasi"
      },
      "Japan": {
        founding: "11 Şubat MÖ 660",
        capital: "Tokyo",
        population: "125 milyon",
        climate: "Ilıman",
        resources: "Balık, nadir mineraller (ithalat)",
        natural: "Fuji Dağı, Okinawa",
        history: "Kyoto Tapınakları, Hiroshima Anıtı"
      }
    };

    const world = Globe()(document.body)
      .globeImageUrl('//unpkg.com/three-globe/example/img/earth-blue-marble.jpg')
      .bumpImageUrl('//unpkg.com/three-globe/example/img/earth-topology.png')
      .backgroundColor('#001d3d')
      .pointOfView({ lat: 30, lng: 90, altitude: 2.5 }, 4000);

    fetch('https://unpkg.com/world-atlas@1/world/110m.json')
      .then(res => res.json())
      .then(worldData => {
        const countries = topojson.feature(worldData, worldData.objects.countries).features;
        const asiaCountries = countries.filter(d => ["China", "India", "Japan"].includes(d.properties.name));

        world
          .polygonsData(asiaCountries)
          .polygonAltitude(0.06)
          .polygonCapColor(d => {
            const colors = { China: "rgba(255,0,0,0.8)", India: "rgba(0,255,0,0.8)", Japan: "rgba(0,0,255,0.8)" };
            return colors[d.properties.name] || "rgba(200,200,200,0.8)";
          })
          .polygonSideColor(() => 'rgba(0, 100, 0, 0.15)')
          .polygonStrokeColor(() => '#111')
          .onPolygonClick((polygon) => {
            const countryName = polygon.properties.name;
            const data = countryDetails[countryName];
            if (data) {
              document.getElementById('infoBox').style.display = 'block';
              document.getElementById('countryName').textContent = countryName;
              document.getElementById('founding').textContent = data.founding;
              document.getElementById('capital').textContent = data.capital;
              document.getElementById('population').textContent = data.population;
              document.getElementById('climate').textContent = data.climate;
              document.getElementById('resources').textContent = data.resources;
              document.getElementById('natural').textContent = data.natural;
              document.getElementById('history').textContent = data.history;
            }
          });
      });
  </script>
</body>
</html>
