<script>
  // @ts-nocheck
  import Chart from 'chart.js/auto';
  
  let location = "cafeteria";
  let mood = "feliz";
  let seed_genres = 'pop';
  let danceability = 5.0;
  let energy = 5.0;
  let loudness = 5.0;
  let speechiness = 5.0;
  let acousticness = 5.0;
  let instrumentalness = 5.0;
  let liveness = 5.0;
  let tempo = 120;
  let genreChart;
  let artistChart;
  let songs = [];
  
  class SongDM {
    constructor(sacousticness, sartist, sdanceability, senergy, sgenres, sinstrumentalness, sliveness, slocation, sloudness, sspeechiness, stempo, strack_name) {
      this.sacousticness = sacousticness;
      this.sartist = sartist;
      this.sdanceability = sdanceability;
      this.senergy = senergy;
      this.sgenres = sgenres;
      this.sinstrumentalness = sinstrumentalness;
      this.sliveness = sliveness;
      this.slocation = slocation;
      this.sloudness = sloudness;
      this.sspeechiness = sspeechiness;
      this.stempo = stempo;
      this.strack_name = strack_name;
    }
  }
  
  async function handleSubmit() {
    const formData = {
      location: location,
      mood: mood,
      danceability: danceability / 10,
      energy: energy / 10,
      loudness: loudness / 10,
      speechiness: speechiness / 10,
      acousticness: acousticness / 10,
      instrumentalness: instrumentalness / 10,
      liveness: liveness / 10,
      tempo: tempo
    };
  
    console.log(formData);
    const response = await fetch('http://127.0.0.1:8000/etl/run-etl/', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(formData)
    });
  
    const result = await response.json();
    songs = result.playlist_info.map(song => {
      // Aseguramos que 'genres' sea un array
      return {
        ...song,
        genres: Array.isArray(song.genres) ? song.genres : [song.genres]
      };
    });
    updateCharts();

    // Cambiar el estilo del div cuando se obtengan los resultados
    const resultDiv = document.getElementById('resultContainer');
    resultDiv.style.display = 'block'; // Mostrar el div
  }
  
  function updateCharts() {
  const genres = {};
  const artists = {};

  songs.forEach(song => {
    song.genres.forEach(genre => {
      // Dividir géneros compuestos y contarlos individualmente
      genre.split(',').forEach(subGenre => { // Cambiado el divisor a coma
        subGenre = subGenre.trim();
        if (subGenre) {
          genres[subGenre] = (genres[subGenre] || 0) + 1;
        }
      });
    });
    artists[song.artist] = (artists[song.artist] || 0) + 1;
  });

  const genreLabels = Object.keys(genres);
  const genreData = Object.values(genres);

  const artistLabels = Object.keys(artists);
  const artistData = Object.values(artists);

  if (genreChart) genreChart.destroy();
  if (artistChart) artistChart.destroy();

  genreChart = new Chart(document.getElementById('genreChart'), {
    type: 'bar',
    data: {
      labels: genreLabels,
      datasets: [{
        label: 'Number of Songs',
        data: genreData,
        backgroundColor: 'rgba(75, 192, 192, 0.2)',
        borderColor: 'rgba(75, 192, 192, 1)',
        borderWidth: 1
      }]
    },
    options: {
      responsive: true,
      maintainAspectRatio: false,
      scales: {
        y: {
          beginAtZero: true
        }
      }
    }
  });

  artistChart = new Chart(document.getElementById('artistChart'), {
    type: 'pie',
    data: {
      labels: artistLabels,
      datasets: [{
        label: 'Number of Songs',
        data: artistData,
        backgroundColor: [
          'rgba(255, 99, 132, 0.2)',
          'rgba(54, 162, 235, 0.2)',
          'rgba(255, 206, 86, 0.2)',
          'rgba(75, 192, 192, 0.2)',
          'rgba(153, 102, 255, 0.2)',
          'rgba(255, 159, 64, 0.2)'
        ],
        borderColor: [
          'rgba(255, 99, 132, 1)',
          'rgba(54, 162, 235, 1)',
          'rgba(255, 206, 86, 1)',
          'rgba(75, 192, 192, 1)',
          'rgba(153, 102, 255, 1)',
          'rgba(255, 159, 64, 1)'
        ],
        borderWidth: 1
      }]
    },
    options: {
      responsive: true,
      maintainAspectRatio: false
    }
  });

  // Actualizar la información de la tarjeta
  document.getElementById('totalSongs').textContent = songs.length;
  document.getElementById('mostFrequentArtist').textContent = artistLabels[artistData.indexOf(Math.max(...artistData))];

  const topGenres = genreLabels
    .map((label, index) => ({ genre: label, count: genreData[index] }))
    .sort((a, b) => b.count - a.count)
    .slice(0, 3)
    .map(item => item.genre)
    .join(', ');

  document.getElementById('top3Genres').textContent = topGenres;
}
</script>

<style>
  .form-label {
    font-weight: bold;
  }

  .form-range {
    width: 100%;
  }

  .result-table {
    width: 100%;
    margin-top: 20px;
  }

  .result-table th, .result-table td {
    padding: 10px;
    text-align: left;
  }

  .canvas-container {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
    margin-top: 20px;
    height: 500px
  }

  canvas {
    max-width: 45%;
    height: 400px; /* Ajusta la altura de los gráficos */
    margin-bottom: 20px;
  }

  .card {
    border-radius: 15px; /* Bordes redondeados */
    transition: transform 0.3s ease, box-shadow 0.3s ease; /* Transición suave */
  }

  .card:hover {
    transform: scale(1.05); /* Expansión sutil */
    box-shadow: 0 10px 20px rgba(0, 0, 0, 0.15); /* Sombra adicional al hacer hover */
  }
</style>

<head>
  <!-- Bootstrap CSS -->
  <link href="https://maxcdn.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css" rel="stylesheet">

  <!-- Bootstrap Icons -->
  <link href="https://cdnjs.cloudflare.com/ajax/libs/bootstrap-icons/1.5.0/font/bootstrap-icons.min.css" rel="stylesheet">
</head>

<h1 class="text-center">Generador y Analizador de Playlist</h1>
<br>
<div class="container text-center">
  <div class="row">
    <div class="col">

    </div>
    <div class="col-12">
      <form on:submit|preventDefault={handleSubmit} class="p-4 shadow rounded bg-light" style="width: fit-content; margin:auto;">
        <div class="form-group">
          <label for="location" class="form-label">Nombre de tu Playlist:</label>
          <input id="location" placeholder="Inserte el nombre" bind:value={location} class="form-control" type="text">
        </div>

        <div class="form-group">
          <label for="seed_genres" class="form-label">Generos preferido:</label>
          <select id="seed_genres" bind:value={seed_genres} class="form-control">
            <option value="pop">Pop</option>
            <option value="rock">Rock</option>
            <option value="alternative">Alternativa</option>
            <option value="dance">Dance</option>
          </select>
        </div>

        <div class="form-group">
          <label for="danceability" class="form-label">Danceability: {danceability}</label>
          <input id="danceability" type="range" min="1" max="10" bind:value={danceability} class="form-range" />
        </div>

        <div class="form-group">
          <label for="energy" class="form-label">Energy: {energy}</label>
          <input id="energy" type="range" min="1" max="10" bind:value={energy} class="form-range" />
        </div>

        <div class="form-group">
          <label for="loudness" class="form-label">Loudness: {loudness}</label>
          <input id="loudness" type="range" min="1" max="10" bind:value={loudness} class="form-range" />
        </div>

        <div class="form-group">
          <label for="speechiness" class="form-label">Speechiness: {speechiness}</label>
          <input id="speechiness" type="range" min="1" max="10" bind:value={speechiness} class="form-range" />
        </div>

        <div class="form-group">
          <label for="acousticness" class="form-label">Acousticness: {acousticness}</label>
          <input id="acousticness" type="range" min="1" max="10" bind:value={acousticness} class="form-range" />
        </div>

        <div class="form-group">
          <label for="instrumentalness" class="form-label">Instrumentalness: {instrumentalness}</label>
          <input id="instrumentalness" type="range" min="1" max="10" bind:value={instrumentalness} class="form-range" />
        </div>

        <div class="form-group">
          <label for="liveness" class="form-label">Liveness: {liveness}</label>
          <input id="liveness" type="range" min="1" max="10" bind:value={liveness} class="form-range" />
        </div>

        <div class="form-group">
          <label for="tempo" class="form-label">Tempo: {tempo}</label>
          <input id="tempo" type="range" min="0" max="220" bind:value={tempo} class="form-range" />
        </div>

        <button type="submit"  class="btn btn-primary btn-block mt-3" style="width: fit-content; margin:auto; ">Obtener Recomendaciones</button>
      </form>
    </div>

    <div class="col-md-auto"  id="resultContainer" style="display: none;">
      <div>
        {#if songs.length > 0}
          <h2 class="mt-5">La playlist generada tiene las siguientes características:</h2>
          <div class="container-fluid">
            <table class="table table-striped result-table" style="table-layout:fixed; white-space-collapse:collapse;">
              <thead>
                <tr>
                  <th>Nombre</th>
                  <th>Artista</th>
                  <th>Generos</th>
                  <th>Bailabilidad</th>
                  <th>Energía</th>
                  <th>Volumen</th>
                  <th>Habla</th>
                  <th>Acústica</th>
                  <th>Instrumental</th>
                  <th>Vivacidad</th>
                  <th>Tempo</th>
                </tr>
              </thead>
              <tbody>
                {#each songs as song}
                  <tr>
                    <td>{song.track_name}</td>
                    <td>{song.artist}</td>
                    <td>{song.genres.map(genre => genre.trim()).join(',')}</td>
                    <td>{song.danceability}</td>
                    <td>{song.energy}</td>
                    <td>{song.loudness}</td>
                    <td>{song.speechiness}</td>
                    <td>{song.acousticness}</td>
                    <td>{song.instrumentalness}</td>
                    <td>{song.liveness}</td>
                    <td>{song.tempo}</td>
                  </tr>
                {/each}
              </tbody>
            </table>
          </div>
        {/if}
      </div>

      <div class="canvas-container">
        <canvas id="genreChart"></canvas>
        <canvas id="artistChart"></canvas>
      </div>

      <div class="row mt-4">
        <div class="col-12 col-md-4">
          <div class="card shadow-sm">
            <div class="card-body">
              <h5 class="card-title"><i class="bi bi-music-note-list"></i> Total de Canciones</h5>
              <p class="card-text" id="totalSongs"></p>
            </div>
          </div>
        </div>
        <div class="col-12 col-md-4">
          <div class="card shadow-sm">
            <div class="card-body">
              <h5 class="card-title"><i class="bi bi-person-fill"></i> Artista Más Frecuente</h5>
              <p class="card-text" id="mostFrequentArtist"></p>
            </div>
          </div>
        </div>
        <div class="col-12 col-md-4">
          <div class="card shadow-sm">
            <div class="card-body">
              <h5 class="card-title"><i class="bi bi-graph-up"></i> Top 3 Géneros</h5>
              <p class="card-text" id="top3Genres"></p>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="col">

    </div>
  </div>
</div>
<br>
<br>


  
 