<!DOCTYPE html>
<html lang="es">
<cabeza>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Generador de series con TMDB</title>
  <style>
    /* Estilos del generador (base) */
    cuerpo {
      tipografía: Arial, sans-serif;
      color de fondo: #121212;
      color: blanco;
      relleno: 20px;
    }
    .contenedor {
      ancho máximo: 800 píxeles;
      margen: auto;
    }
    etiqueta {
      pantalla: bloque;
      margen superior: 10px;
    }
    entrada, área de texto, selección {
      ancho: 100%;
      relleno: 8px;
      margen superior: 5px;
      borde: ninguno;
      borde-radio: 4px;
      tamaño de caja: caja con borde;
    }
    botón {
      relleno: 10px;
      color de fondo: #6200ea;
      color: blanco;
      borde: ninguno;
      borde-radio: 4px;
      cursor: puntero;
      margen superior: 10px;
    }
    .contenedor-temporada, .contenedor-capítulo {
      borde: 1px sólido #444;
      relleno: 10px;
      margen superior: 10px;
    }
    #searchResults div {
      margen: 10px 0;
      relleno: 10px;
      borde: 1px sólido #333;
      borde-radio: 4px;
      pantalla: flexible;
      alinear elementos: centro;
    }
    #resultados de búsqueda img {
      borde-radio: 4px;
    }
    Botón #resultadosdebúsqueda {
      margen izquierdo: automático;
      color de fondo: #03a9f4;
    }
    #searchResults button:hover {
      color de fondo: #0288d1;
    }
    pre {
      color de fondo: #1e1e1e;
      relleno: 15px;
      desbordamiento: automático;
      espacio en blanco: pre-ajuste;
      margen superior: 20px;
    }
    código {
      familia tipográfica: monoespaciada;
    }
  </style>
</head>
<cuerpo>
  <div class="container">
    <h1>Generador de series (con TMDB)</h1>

    <!-- Selector para elegir si buscar Series o Películas -->
    <h2>Tipo de Alimentar</h2>
    <label for="searchType">Seleccione:</label>
    <select id="searchType">
      <option value="tv">Series</option>
      <option value="movie">Películas</option>
    </select>

    <!-- Campo para el servicio de streaming -->
    <h2>Servicio de Streaming</h2>
    <label for="streamingService">Servicio:</label>
    <input type="text" id="streamingService" placeholder="Ej: Netflix">

    <!-- Campo para URL del video (solo para películas) -->
    <h2>Video (solo para películas)</h2>
    <label for="videoUrl">URL del video:</label>
    <input type="url" id="videoUrl" placeholder="https://...">

    <!-- Sección de búsqueda en TMDB -->
    <h2>Búsqueda en TMDB</h2>
    <label for="search">Buscar Título:</label>
    <input type="text" id="search" placeholder="Ingrese el título">
    <button type="button" onclick="searchTMDB()">Buscar en TMDB</button>
    <div id="searchResults"></div>

    <!-- Información general -->
    <h2>Información general</h2>
    <label for="series-title">Título:</label>
    <input type="text" id="series-title" placeholder="Ej: Merlina">
    <label for="synopsis">Sinopsis global:</label>
    <textarea id="synopsis" rows="4" placeholder="Sinopsis global"></textarea>
    <label for="backgroundUrl">URL de Imagen de Fondo:</label>
    <input type="url" id="backgroundUrl" placeholder="https://...">
    <label for="posterUrl">URL del Póster Principal:</label>
    <input type="url" id="posterUrl" placeholder="https://...">
    <label for="tmdbId">ID de TMDB:</label>
    <input type="text" id="tmdbId" placeholder="ID de TMDB">
    <label for="pageUrl">URL de la página:</label>
    <input type="url" id="pageUrl" placeholder="https://...">

    <!-- Configuración de Temporadas (solo para series) -->
    <h2>Configuración de temporadas</h2>
    <div id="seasons-container"></div>
    <button type="button" onclick="addSeason()">Agregar Temporada</button>

    <button type="button" onclick="generateSeriesCode()">Generar código</button>

    <h3>Código Generado:</h3>
    <pre><code id="generatedCode"></code></pre>
    <button type="button" onclick="downloadHTML()">Descargar HTML</button>
  </div>

  <script>
    var TMDB_API_KEY = '686e8f50b2135e3c32f670ec018df888';

    función buscarTMDB() {
      var searchType = document.getElementById('searchType').value; // 'tv' o 'movie'
      var query = document.getElementById('search').value.trim();
      if (!query) {
        alert('Ingrese un título para buscar.');
        devolver;
      }
      var url = "";
      if (searchType === 'tv') {
        url = "https://api.themoviedb.org/3/search/tv?api_key=" + TMDB_API_KEY + "&language=es-ES&query=" + encodeURIComponent(query);
      } demás {
        url = "https://api.themoviedb.org/3/search/movie?api_key=" + TMDB_API_KEY + "&language=es-ES&query=" + encodeURIComponent(query);
      }
      obtener(url)
      .then(function(response) { return response.json(); })
      .entonces(función(datos) {
        if (data.results && data.results.length > 0) {
          mostrarResultadosBúsqueda(datos.resultados);
        } demás {
          document.getElementById('searchResults').innerHTML = "";
          alert('No se encontraron resultados.');
        }
      })
      .catch(function(error) {
        console.error('Error TMDB:', error);
        alert('Error al buscar en TMDB.');
      });
    }

    función mostrarResultadosBúsqueda(resultados) {
      var contenedor = document.getElementById('searchResults');
      contenedor.innerHTML = "";
      resultados.forEach(function(resultado) {
        var title = (result.name || result.title) || "";
        var fecha = (resultado.fecha_primera_emisión || resultado.fecha_lanzamiento) || "";
        var poster = result.poster_path ? "https://image.tmdb.org/t/p/w92" + result.poster_path : "";
        var div = document.createElement('div');
        div.innerHTML = '<img src="' + poster + '" style="width:50px; margin-right:10px;"> ' +
                        '<strong>' + título + '</strong> (' + fecha + ') ' +
                        '<button type="button">Seleccionar</button>';
        div.querySelector('button').addEventListener('click', function() { selectResult(result); });
        contenedor.appendChild(div);
      });
    }

    función selectResult(result) {
      var título = resultado.nombre || resultado.título || "";
      var overview = result.overview || "";
      var backdrop = result.backdrop_path ? "https://image.tmdb.org/t/p/original" + result.backdrop_path : "";
      var poster = result.poster_path ? "https://image.tmdb.org/t/p/w500" + result.poster_path : "";
      var id = result.id || "";
      document.getElementById('series-title').value = title;
      document.getElementById('synopsis').value = overview;
      document.getElementById('backgroundUrl').value = backdrop;
      document.getElementById('posterUrl').value = poster;
      document.getElementById('tmdbId').value = id;
      document.getElementById('searchResults').innerHTML = "";
    }

    // Manejo de temporadas (solo para serie)
    var seasonCount = 0;
    función agregarTemporada() {
      temporadaCount++;
      var contenedor = document.getElementById('seasons-container');
      var seasonDiv = document.createElement('div');
      seasonDiv.className = 'season-container';
      seasonDiv.id = 'season-' + seasonCount;
      seasonDiv.innerHTML = "\n" +
        "<h3>Temporada " + seasonCount + "</h3>\n" +
        "<label for='season-name-" + seasonCount + "'>Nombre de la Temporada:</label>\n" +
        "<input type='text' id='season-name-" + seasonCount + "' placeholder='Ej: Saga Edolas'>\n" +
        "<label for='season-synopsis-" + seasonCount + "'>Sinopsis de la Temporada:</label>\n" +
        "<textarea id='season-synopsis-" + seasonCount + "' rows='3' placeholder='Sinopsis de la temporada'></textarea>\n" +
        "<label for='season-poster-" + seasonCount + "'>URL del Póster de la Temporada:</label>\n" +
        "<input type='url' id='season-poster-" + seasonCount + "' placeholder='https://...'>\n" +
        "<div id='chapters-container-" + seasonCount + "'></div>\n" +
        "<button type='button' onclick='addChapter(" + seasonCount + ")'>Agregar Capítulo</button>\n";
      contenedor.appendChild(seasonDiv);
      agregarCapítulo(númeroDeTemporadas);
    }

    función agregarCapítulo(númeroTemporada) {
      var contenedor = document.getElementById("chapters-container-" + seasonNum);
      var chapterCount = container.childElementCount + 1;
      var chapterDiv = document.createElement('div');
      chapterDiv.className = 'chapter-container';
      chapterDiv.innerHTML = "\n" +
        "<label for='chapter-url-" + seasonNum + "-" + chapterCount + "'>URL del Capítulo " + chapterCount + ":</label>\n" +
        "<input type='text' id='chapter-url-" + seasonNum + "-" + chapterCount + "' placeholder='https://...'>\n" +
        "<label for='chapter-type-" + seasonNum + "-" + chapterCount + "'>Tipo del Capítulo:</label>\n" +
        "<select id='chapter-type-" + seasonNum + "-" + chapterCount + "'>\n" +
        " <option value='Capítulo'>Capítulo</option>\n" +
        " <option value='Especial'>Especial</option>\n" +
        " <option value='OVA'>OVA</option>\n" +
        "</select>\n";
      contenedor.appendChild(chapterDiv);
    }

    función generarCódigoDeSerie() {
      var searchType = document.getElementById('searchType').value; // 'tv' o 'movie'
      var streamingService = document.getElementById('streamingService').value || "Netflix";
      var seriesTitle = document.getElementById('series-title').value;
      var globalSynopsis = document.getElementById('synopsis').value;
      var backgroundUrl = document.getElementById('backgroundUrl').value;
      var posterUrl = document.getElementById('posterUrl').value;
      var tmdbId = document.getElementById('tmdbId').value;
      var pageUrl = document.getElementById('pageUrl').value;
      var videoUrl = document.getElementById('videoUrl').value; // campo para película

      var seasonsObj = {};
      var postersObj = {};
      var seasonOptions = "";
      if (searchType === 'tv') {
        for (var i = 1; i <= seasonCount; i++) {
          var seasonName = document.getElementById("season-name-" + i).value || ("Temporada " + i);
          var seasonSynopsis = document.getElementById("season-synopsis-" + i).value || globalSynopsis;
          var seasonPoster = document.getElementById("season-poster-" + i).value || posterUrl;
          var chaptersContainer = document.getElementById("chapters-container-" + i);
          var chapterDivs = chaptersContainer.getElementsByClassName("chapter-container");
          var capítulos = [];
          for (var j = 0; j < chapterDivs.length; j++) {
            var chapterUrl = document.getElementById("chapter-url-" + i + "-" + (j+1)).value;
            var chapterType = document.getElementById("chapter-type-" + i + "-" + (j+1)).value;
            capítulos.push({ url: urlCapítulo, tipo: tipoCapítulo });
          }
          seasonsObj[i] = { chapters: chapters, synopsis: seasonSynopsis, name: seasonName };
          cartelesObj[i] = póster de temporada;
          seasonOptions += "<option value='" + i + "'>Temporada " + i + "</option>\n";
        }
      }

      var líneas = [];
      líneas.push("<!DOCTYPE html>");
      líneas.push("<html lang='es'>");
      líneas.push("<head>");
      líneas.push(" <meta charset='UTF-8'>");
      líneas.push(" <meta name='viewport' content='width=device-width, initial-scale=1.0'>");
      líneas.push(" <título>" + títuloDeLaSerie + "</título>");
      líneas.push(" <style>");
      if (searchType === 'tv') {
        // Plantilla series.html con los estilos solicitados:
        lines.push(" /* Estilos para la plantilla final SERIES */");
        líneas.push(" cuerpo {");
        líneas.push(" margen: 0;");
        líneas.push(" font-family: Arial, sans-serif;");
        líneas.push(" background-color: #1c1c1c;");
        líneas.push(" color: blanco;");
        líneas.push(" text-align: center;");
        líneas.push(" }");
        líneas.push(" .contenedor {");
        líneas.push(" ancho máximo: 600px;");
        líneas.push(" margen: 0 auto;");
        líneas.push(" padding: 10px;");
        líneas.push(" text-align: left;");
        líneas.push(" }");
        líneas.push(" .imagen-de-fondo {");
        líneas.push(" ancho: 100%;");
        líneas.push(" altura: 250px;");
        líneas.push(" background-size: cover;");
        líneas.push(" background-position: center;");
        líneas.push("border-radius: 5px;");
        líneas.push(" margen-inferior: 10px;");
        líneas.push(" }");
        líneas.push(" #video-player {");
        líneas.push(" ancho: 100%;");
        líneas.push(" altura: 250px;");
        líneas.push(" mostrar: ninguno;");
        líneas.push("border-radius: 5px;");
        líneas.push(" margen-inferior: 10px;");
        líneas.push(" z-index: 5;");
        líneas.push(" }");
        líneas.push(" .poster {");
        líneas.push(" ancho: 120px;");
        líneas.push(" altura: 180px;");
        líneas.push("border-radius: 5px;");
        líneas.push(" box-shadow: 0 4px 8px rgba(0,0,0,0.5);");
        líneas.push(" posición: relativa;");
        líneas.push(" margen-izquierda: 15px;");
        líneas.push(" margen superior: -140px;");
        líneas.push(" margen-inferior: 10px;");
        líneas.push(" z-index: 2;");
        líneas.push(" }");
        líneas.push(" .botones {");
        líneas.push(" posición: relativa;");
        líneas.push(" margen superior: -30px;");
        líneas.push(" margen-izquierda: 190px;");
        líneas.push(" }");
        líneas.push(" #botón-favorito {");
        líneas.push(" posición: absoluta;");
        líneas.push(" z-index: 3;");
        líneas.push(" background-color: #cc0033;");
        líneas.push(" color: blanco;");
        líneas.push("borde: 1px sólido #555;");
        líneas.push(" padding: 5px 10px;");
        líneas.push(" cursor: puntero;");
        líneas.push(" tamaño de fuente: 14px;");
        líneas.push("border-radius: 5px;");
        líneas.push(" margen-izquierda: 93px;");
        líneas.push(" margen superior: -195px;");
        líneas.push(" limpiar: ambos;");
        líneas.push(" }");
      } demás {
        // Plantilla películas.html (dejamos intacta la mayoría, excepto el #favorite-button)
        lines.push(" /* Estilos para la plantilla final PELÍCULAS */");
        líneas.push(" cuerpo {");
        líneas.push(" margen: 0;");
        líneas.push(" font-family: Arial, sans-serif;");
        líneas.push(" background-color: #1c1c1c;");
        líneas.push(" color: blanco;");
        líneas.push(" text-align: center;");
        líneas.push(" }");
        líneas.push(" .contenedor {");
        líneas.push(" ancho máximo: 600px;");
        líneas.push(" margen: 0 auto;");
        líneas.push(" padding: 10px;");
        líneas.push(" text-align: left;");
        líneas.push(" }");
        líneas.push(" .imagen-de-fondo {");
        líneas.push(" ancho: 100%;");
        líneas.push(" altura: 250px;");
        líneas.push(" background-size: cover;");
        líneas.push(" background-position: center;");
        líneas.push("border-radius: 5px;");
        líneas.push(" margen-inferior: 10px;");
        líneas.push(" }");
        líneas.push(" #video-player {");
        líneas.push(" ancho: 100%;");
        líneas.push(" altura: 250px;");
        líneas.push(" mostrar: ninguno;");
        líneas.push("border-radius: 5px;");
        líneas.push(" margen-inferior: 10px;");
        líneas.push(" z-index: 5;");
        líneas.push(" }");
        líneas.push(" .poster {");
        líneas.push(" ancho: 120px;");
        líneas.push(" altura: 180px;");
        líneas.push("border-radius: 5px;");
        líneas.push(" box-shadow: 0 4px 8px rgba(0,0,0,0.5);");
        líneas.push(" posición: relativa;");
        líneas.push(" margen-izquierda: 15px;");
        líneas.push(" margen superior: -140px;");
        líneas.push(" margen-inferior: 10px;");
        líneas.push(" z-index: 2;");
        líneas.push(" }");
        líneas.push(" #botón-favorito {");
        líneas.push(" posición: absoluta;");
        líneas.push(" z-index: 3;");
        líneas.push(" background-color: #cc0033;");
        líneas.push(" color: blanco;");
        líneas.push("borde: 1px sólido #555;");
        líneas.push(" padding: 5px 10px;");
        líneas.push(" cursor: puntero;");
        líneas.push(" tamaño de fuente: 14px;");
        líneas.push("border-radius: 5px;");
        líneas.push(" margen-izquierda: -40px;");
        líneas.push(" margen superior: -140px;");
        líneas.push(" limpiar: ambos;");
        líneas.push(" }");
      }
      // Elementos comunes para ambas plantillas:
      líneas.push(" .menú-capítulo {");
      líneas.push(" display: flex;");
      líneas.push(" desbordamiento-x: auto;");
      líneas.push(" espacio: 8px;");
      líneas.push(" padding: 0 0 10px 0;");
      líneas.push(" ancho de la barra de desplazamiento: ninguno;");
      líneas.push(" limpiar: ambos;");
      líneas.push(" margen-izquierda: 15px;");
      if (searchType === 'tv') {
        lineas.push("margen-superior: 10px;"); // En series, 10px desde el póster (ya que el reproductor se muestra en su lugar)
      }
      líneas.push(" }");
      líneas.push(" .chapter-menu::-webkit-scrollbar {");
      líneas.push(" mostrar: ninguno;");
      líneas.push(" }");
      líneas.push(" .botón-capítulo {");
      líneas.push(" background-color: #333;");
      líneas.push(" color: blanco;");
      líneas.push("borde: 1px sólido #cc0033;");
      líneas.push(" padding: 6px 10px;");
      líneas.push(" tamaño de fuente: 12px;");
      líneas.push("border-radius: 5px;");
      líneas.push(" cursor: puntero;");
      líneas.push(" transición: 0.2s;");
      líneas.push("espacio en blanco: ahora;");
      líneas.push(" }");
      líneas.push(" .chapter-button:hover {");
      líneas.push(" background-color: #cc0033;");
      líneas.push(" }");
      líneas.push(" .capítulo-activo {");
      líneas.push(" background-color: #a30026 !important;");
      líneas.push("borde: 2px sólido #ff4d6a;");
      líneas.push(" }");
      líneas.push(" .content {");
      líneas.push(" margen superior: 10px;");
      líneas.push(" }");
      líneas.push(" .título {");
      líneas.push(" tamaño de fuente: 20px;");
      líneas.push(" font-weight: bold;");
      líneas.push(" margen superior: 10px;");
      líneas.push(" margen-inferior: 0;");
      líneas.push(" padding: 0 10px;");
      líneas.push(" }");
      líneas.push(" .type-line {");
      líneas.push(" tamaño de fuente: 14px;");
      líneas.push(" color: #999;");
      líneas.push(" margen superior: 5px;");
      líneas.push(" margen-inferior: 10px;");
      líneas.push(" padding: 0 10px;");
      líneas.push(" }");
      líneas.push(" .sinopsis {");
      líneas.push(" tamaño de fuente: 14px;");
      líneas.push(" color: #bdbdbd;");
      líneas.push(" margen superior: 10px;");
      líneas.push(" margen-inferior: 10px;");
      líneas.push(" padding: 0 10px;");
      líneas.push(" limpiar: ambos;");
      líneas.push(" }");
      líneas.push(" seleccionar {");
      líneas.push(" background-color: #cc0033;");
      líneas.push(" color: blanco;");
      líneas.push("border: none;");
      líneas.push(" padding: 7px;");
      líneas.push(" tamaño de fuente: 14px;");
      líneas.push("border-radius: 5px;");
      líneas.push(" cursor: puntero;");
      líneas.push(" }");
      líneas.push(" </style>");
      líneas.push("</head>");
      líneas.push("<cuerpo>");
      líneas.push(" <div class='container'>");
      lines.push(" <div class='background-image' id='background' style='background-image: url(\"" + backgroundUrl + "\");'></div>");lines.push(" <img class='poster' id='poster' src='" + posterUrl + "' alt='Póster'>");
      líneas.push(" <iframe id='video-player' src='' frameborder='0' allowfullscreen></iframe>");
      if (searchType === 'tv') {
        // En serie, el bloque .buttons se coloca justo debajo del reproductor con margen de 5px respecto al título (se restablece en changeSeason)
        líneas.push(" <div class='buttons'>");
        líneas.push(" <select id='season-selector' onchange='changeSeason(this.value)'>");
        líneas.push(opcionesTemporada);
        líneas.push(" </select>");
        líneas.push(" </div>");
      }
      líneas.push(" <button id='favorite-button' class='button' onclick='toggleFavorite()'>🤍</button>");
      líneas.push(" <div class='content'>");
      líneas.push(" <div class='title' id='title'>" + seriesTitle + (searchType === 'tv' && seasonsObj[1] ? " - " + seasonsObj[1].name : "") + "</div>");
      var tipoLabel = (searchType === 'tv')? "Serie" : "Película";
      líneas.push(" <div class='type-line' id='typeLine'>Tipo: " + tipoLabel + " | " + streamingService + "</div>");
      if (searchType === 'tv') {
        líneas.push(" <div class='chapter-menu' id='chapter-menu'></div>");
        var firstSynopsis = (seasonsObj[1] && seasonsObj[1].synopsis) ? seasonsObj[1].synopsis : globalSynopsis;
        líneas.push(" <div class='synopsis' id='synopsis'>" + firstSynopsis + "</div>");
      } demás {
        líneas.push(" <div class='chapter-menu' id='chapter-menu'>");
        líneas.push(" <button class='chapter-button' onclick='changeChapter(\"" + videoUrl + "\", this)'>Ver ahora</button>");
        líneas.push(" </div>");
        líneas.push(" <div class='synopsis' id='synopsis'>" + globalSynopsis + "</div>");
      }
      líneas.push(" </div>");
      líneas.push(" </div>");
      líneas.push(" <scr"+"ipt>");
      líneas.push(" const película = { id: '" + tmdbId + "', título: '" + títuloSerie + "', urlImagen: '" + urlPóster + "', enlace: '" + urlPágina + "' };");
      if (searchType === 'tv') {
        líneas.push(" const temporadas = " + JSON.stringify(seasonsObj) + ";");
        líneas.push(" const carteles = " + JSON.stringify(postersObj) + ";");
      } demás {
        líneas.push(" const estaciones = {};");
        líneas.push(" const carteles = {};");
      }
      líneas.push(" sea temporadaActual = 1;");
      líneas.push(" función cambiarTemporada(temporada) {");
      líneas.push(" temporada = parseInt(temporada, 10);");
      líneas.push(" temporadaActual = temporada;");
      líneas.push(" document.getElementById('title').textContent = movie.title + ' - ' + seasons[season].name;");
      líneas.push(" document.getElementById('synopsis').textContent = seasons[season].synopsis;");
      líneas.push(" document.getElementById('poster').src = posters[season];");
      líneas.push(" const chapterMenu = document.getElementById('chapter-menu');");
      líneas.push(" capítuloMenu.innerHTML = '';");
      líneas.push(" temporadas[temporada].capítulos.forEach(function(ep, índice) {");
      líneas.push(" const btn = document.createElement('button');");
      líneas.push(" btn.textContent = ep.type + ' ' + (index + 1);");
      líneas.push(" btn.className = 'chapter-button';");
      líneas.push(" btn.onclick = function() { cambiarCapítulo(ep.url, btn); };");
      líneas.push(" capítuloMenu.appendChild(btn);");
      líneas.push(" });");
      líneas.push(" document.getElementById('video-player').style.display = 'none';");
      líneas.push(" document.getElementById('background').style.display = 'block';");
      líneas.push(" document.getElementById('poster').style.display = 'block';");
      líneas.push(" document.getElementById('favorite-button').style.display = 'block';");
      lines.push(" // Restablece el margen de .buttons a su valor original");
      líneas.push(" var btns = document.getElementById('season-selector');");
      líneas.push(" if(btns && btns.parentElement) { btns.parentElement.style.marginTop = '-30px'; }");
      líneas.push(" }");
      líneas.push(" función cambiarCapítulo(url, botónSeleccionado) {");
      líneas.push(" document.getElementById('video-player').src = url;");
      líneas.push(" document.getElementById('video-player').style.display = 'block';");
      líneas.push(" document.getElementById('background').style.display = 'none';");
      líneas.push(" document.getElementById('poster').style.display = 'none';");
      líneas.push(" document.getElementById('favorite-button').style.display = 'none';");
      lines.push(" // Ajusta el margen de .buttons para dejar 15px de separación respecto al reproductor");
      líneas.push(" var btns = document.getElementById('season-selector');");
      líneas.push(" if(btns && btns.parentElement) { btns.parentElement.style.marginTop = '15px'; }");
      líneas.push(" var todosLosBotones = document.querySelectorAll('.chapter-button');");
      líneas.push(" todosLosBotones.forEach(function(btn) { btn.classList.remove('active-chapter'); });");
      líneas.push(" si (botónSeleccionado) {");
      líneas.push(" selectedButton.classList.add('active-chapter');");
      líneas.push(" }");
      líneas.push(" }");
      líneas.push(" función alternarFavoritos() {");
      líneas.push(" let favorites = JSON.parse(localStorage.getItem('favorites')) || [];");
      líneas.push(" const favBtn = document.getElementById('favorite-button');");
      líneas.push(" const isFav = favorites.some(function(f) { return f.id === movie.id; });");
      líneas.push(" if(esFavorito) {");
      líneas.push(" favoritos = favoritos.filter(function(f) { return f.id !== película.id; });");
      líneas.push(" favBtn.textContent = '🤍';");
      lines.push(" alert('Removido de favoritos');");
      líneas.push(" } else {");
      líneas.push(" favoritos.push(película);");
      líneas.push(" favBtn.textContent = '💓';");
      lines.push(" alert('Agregado a favoritos');");
      líneas.push(" }");
      líneas.push("localStorage.setItem('favorites', JSON.stringify(favorites));");
      líneas.push(" }");
      if (searchType === 'tv') {
        líneas.push(" window.onload = function() { cambiarTemporada(1); };");
      } demás {
        lines.push(" // Película: no se requiere cargar temporada al inicio");
      }
      líneas.push(" </scr"+"ipt>");
      líneas.push("</body>");
      líneas.push("</html>");

      var códigoFinal = líneas.join("\n");
      document.getElementById('generatedCode').textContent = finalCode;
    }

    función descargarHTML() {
      var codeText = document.getElementById('generatedCode').textContent;
      if (!codeText) {
        alert('Genera el código primero.');
        devolver;
      }
      var blob = new Blob([codeText], { type: 'text/html' });
      var a = document.createElement('a');
      a.href = URL.createObjectURL(blob);
      a.descargar = 'serie.html';
      a.click();
    }
  </script>
</body>
</html>
