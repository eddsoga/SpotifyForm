<script>
    let location = "cafeteria";
    let mood = "feliz";
    let seed_genres = 'pop';
    let danceability = 5;
    let energy = 5;
    let loudness = 5;
    let speechiness = 5;
    let acousticness = 5;
    let instrumentalness = 5;
    let liveness = 5;
    let tempo = 120;
  
    async function handleSubmit() {
      const formData = {
      location:location,
      mood:mood,
      danceability: danceability / 10,
      energy: energy / 10,
      loudness: loudness / 10,
      speechiness: speechiness / 10,
      acousticness: acousticness / 10,
      instrumentalness: instrumentalness / 10,
      liveness: liveness / 10,
      tempo:tempo
    };
      console.log(formData);
      const response = await fetch('http://localhost:8000/etl/playlist_info/', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(formData)
      });
  
      const result = await response.json();
      console.log(result);
    }
  </script>
  
  <style>
    .form-label {
      font-weight: bold;
    }
  
    .form-range {
      width: 100%;
    }
  </style>
  <h1 style="align-content: center;">Generadador y Analizador de Playlist </h1>
  <form on:submit|preventDefault={handleSubmit} class="p-4 shadow rounded bg-light">
    <div class="form-group">
      <label for="location" class="form-label">Tipo de lugar:</label>
      <select id="location" bind:value={location} class="form-control">
        <option value="cafeteria">Cafetería</option>
        <option value="gym">Gimnasio</option>
        <option value="restaurant">Restaurante</option>
        <option value="hotel">Hotel</option>
      </select>
    </div>

    <div class="form-group">
        <label for="seed_genres" class="form-label">Generos incluidos:</label>
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
  
    <button type="submit" class="btn btn-primary btn-block mt-3">Obtener Recomendaciones</button>
  </form>
  <br>
  <h1>La playlist generada tiene las siguientes carcaterísticas:</h1>
  
  
  
  