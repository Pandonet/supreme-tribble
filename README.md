
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>PandoNettV</title>

    <!-- Manifest PWA -->
    <link rel="manifest" href="/manifest.json">

    <!-- Importar Video.js desde Cloudflare -->
    <link href="https://cdnjs.cloudflare.com/ajax/libs/video.js/7.14.3/video-js.min.css" rel="stylesheet">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/video.js/7.14.3/video.min.js" type="text/javascript"></script>

    <style>
        /* Variables para el diseño moderno y suave */
        :root {
            --primary-bg: #f5f7fa;
            --card-bg: #ffffff;
            --primary-color: #6366f1;
            --secondary-color: #a5b4fc;
            --text-color: #334155;
            --light-text: #64748b;
            --border-radius: 16px;
            --shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.05), 0 4px 6px -2px rgba(0, 0, 0, 0.025);
            --transition: all 0.3s ease;
            --content-max-width: 1200px;
            --card-padding: 24px;
        }

        /* Reset y estilos base */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            background-color: var(--primary-bg);
            color: var(--text-color);
            font-family: 'Inter', system-ui, -apple-system, sans-serif;
            line-height: 1.6;
            margin: 0;
            padding: 0;
        }

        h1, h2, h3 {
            font-weight: 600;
            color: var(--text-color);
            text-align: center;
        }

        h1 {
            font-size: 1.8rem;
            margin-bottom: 1.5rem;
        }

        h2 {
            font-size: 1.5rem;
            margin-bottom: 1.25rem;
        }

        h3 {
            font-size: 1.2rem;
            margin-bottom: 1rem;
        }

        a {
            color: var(--primary-color);
            text-decoration: none;
            transition: var(--transition);
        }

        a:hover {
            color: var(--secondary-color);
        }

        img {
            max-width: 100%;
            height: auto;
            border-radius: 8px;
        }

        /* Estructura de la página */
        .container {
            max-width: var(--content-max-width);
            margin: 0 auto;
            padding: 20px;
        }

        /* Sección de reproductor y canales en disposición horizontal */
        .player-channels-wrapper {
            display: grid;
            grid-template-columns: 2fr 1fr;
            gap: 20px;
            margin-bottom: 30px;
        }

        /* Sección del reproductor */
        .player-section {
            background-color: var(--card-bg);
            padding: 20px;
            border-radius: var(--border-radius);
            box-shadow: var(--shadow);
            display: flex;
            justify-content: center;
            align-items: center;
            width: 100%;
        }

        .video-container {
            width: 100%;
            position: relative;
            padding-bottom: 56.25%; /* 16:9 Aspect Ratio (9 / 16 = 0.5625 o 56.25%) */
            height: 0;
            overflow: hidden;
            border-radius: var(--border-radius);
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.05);
        }

        #my-player {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            border-radius: var(--border-radius);
            overflow: hidden;
        }

        /* Lista de canales */
        .channels-section {
            background-color: var(--card-bg);
            padding: 20px;
            border-radius: var(--border-radius);
            box-shadow: var(--shadow);
            display: flex;
            flex-direction: column;
        }

        .channels-title {
            margin-bottom: 20px;
            position: relative;
            padding-bottom: 10px;
        }

        .channels-title::after {
            content: '';
            position: absolute;
            width: 60px;
            height: 3px;
            background: var(--primary-color);
            bottom: 0;
            left: 50%;
            transform: translateX(-50%);
            border-radius: 2px;
        }

        .channels-grid {
            list-style: none;
            padding: 0;
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(100px, 1fr));
            gap: 15px;
            width: 100%;
            overflow-y: auto;
            max-height: 400px;
        }

        .channel-item {
            cursor: pointer;
            background-color: var(--primary-bg);
            border-radius: var(--border-radius);
            padding: 12px;
            box-shadow: var(--shadow);
            transition: var(--transition);
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            text-align: center;
        }

        .channel-item:hover {
            transform: translateY(-5px);
            box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 10px 10px -5px rgba(0, 0, 0, 0.04);
        }

        .channel-item img {
            width: 50px;
            height: 50px;
            object-fit: contain;
            margin-bottom: 8px;
            border-radius: 8px;
        }

        /* Estructura de contenido y sidebar */
        .content-wrapper {
            display: grid;
            grid-template-columns: 3fr 1fr;
            gap: 30px;
            margin-bottom: 40px;
        }

        /* Contenido principal */
        .content {
            background-color: var(--card-bg);
            padding: var(--card-padding);
            border-radius: var(--border-radius);
            box-shadow: var(--shadow);
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        .content img {
            border-radius: var(--border-radius);
            transition: var(--transition);
            max-width: 100%;
            height: auto;
        }

        .content img:hover {
            transform: scale(1.02);
        }

        /* Sidebar */
        .sidebar {
            background-color: var(--card-bg);
            padding: var(--card-padding);
            border-radius: var(--border-radius);
            box-shadow: var(--shadow);
        }

        .sidebar h2 {
            position: relative;
            padding-bottom: 12px;
            margin-bottom: 20px;
        }

        .sidebar h2::after {
            content: '';
            position: absolute;
            width: 50px;
            height: 3px;
            background: var(--primary-color);
            bottom: 0;
            left: 50%;
            transform: translateX(-50%);
            border-radius: 2px;
        }

        .categories-list {
            list-style: none;
            padding: 0;
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        .categories-list li {
            width: 100%;
            margin-bottom: 8px;
            text-align: center;
        }

        .categories-list li a {
            display: block;
            padding: 10px;
            background-color: rgba(99, 102, 241, 0.05);
            border-radius: 8px;
            transition: var(--transition);
            font-weight: 500;
        }

        .categories-list li a:hover {
            background-color: rgba(99, 102, 241, 0.1);
            transform: translateY(-2px);
        }

        /* Gadgets */
        .gadgets-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
            gap: 25px;
            width: 100%;
        }

        .gadget-item {
            background-color: var(--card-bg);
            padding: var(--card-padding);
            border-radius: var(--border-radius);
            box-shadow: var(--shadow);
            transition: var(--transition);
            display: flex;
            flex-direction: column;
            align-items: center;
            text-align: center;
        }

        .gadget-item:hover {
            transform: translateY(-5px);
            box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 10px 10px -5px rgba(0, 0, 0, 0.04);
        }

        .gadget-item h3 {
            margin-bottom: 15px;
        }

        .gadget-item img {
            max-width: 80%;
            margin-top: 10px;
            border-radius: 8px;
            transition: var(--transition);
        }

        .gadget-item img:hover {
            transform: scale(1.05);
        }

        /* Responsive design */
        @media (max-width: 900px) {
            .player-channels-wrapper {
                grid-template-columns: 1fr;
            }

            .content-wrapper {
                grid-template-columns: 1fr;
            }

            .channels-grid {
                grid-template-columns: repeat(auto-fill, minmax(120px, 1fr));
                max-height: none;
            }
        }

        @media (max-width: 600px) {
            :root {
                --card-padding: 20px;
            }

            .gadgets-grid {
                grid-template-columns: 1fr;
            }

            h1 {
                font-size: 1.5rem;
            }

            h2 {
                font-size: 1.3rem;
            }

            h3 {
                font-size: 1.1rem;
            }

            .channels-grid {
                grid-template-columns: repeat(auto-fill, minmax(100px, 1fr));
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <!-- Wrapper para reproductor y canales en disposición horizontal -->
        <div class="player-channels-wrapper">
            <!-- Reproductor de video -->
            <div class="player-section">
                <div class="video-container">
                    <video id="my-player" class="video-js vjs-default-skin" controls>
                        <source src="https://live20.bozztv.com/giatvplayout7/giatv-209235/playlist.m3u8" type="application/x-mpegURL">
                    </video>
                </div>
            </div>

            <!-- Lista de canales (ahora a la derecha) -->
            <div class="channels-section">
                <h2 class="channels-title">PandoNetTv</h2>
                <ul class="channels-grid">
                    <li class="channel-item" data-source="https://live20.bozztv.com/giatvplayout7/giatv-209235/playlist.m3u8">
                        <img alt="Logo" src="https://i.ibb.co/P1W0g7G/418462893-884374087021844-4453603128248385175-n.jpg" />
                        <a href="javascript:void(0);"></a>
                    </li>
                    <li class="channel-item" data-source="https://live20.bozztv.com/giatvplayout7/giatv-209405/playlist.m3u8">
                        <img src="https://i.ibb.co/twh0GgvR/Utool-20250401-224313622.png" alt="KNP TELEVISION" />
                        <a href="javascript:void(0);"></a>
                    </li>
                    <li class="channel-item" data-source="http://boliviatv1.srfms.com:5735/live/livestream/playlist.m3u8">
                        <img alt="Logo" src="https://seeklogo.com/images/B/bolivia-tv-logo-92C4C6471A-seeklogo.com.png" />
                        <a href="javascript:void(0);"></a>
                    </li>
                    <li class="channel-item" data-source="https://tv2.bitstreaming.net:3235/multi_live/play.m3u8">
                        <img alt="Logo" src="https://f10.tv.bo/wp-content/uploads/2024/06/351466087_3521344231429745_2162507638893969516_n-1.jpg" />
                        <a href="javascript:void(0);"></a>
                    </li>
                    <li class="channel-item" data-source="http://tvpremiund.ddns.me:2082/live/Pedroarias/87HTfMZaKw4q/49660.m3u8">
                        <img alt="Logo" src="https://i.ibb.co/0Bghpr7/babytv-2017.png" />
                        <a href="javascript:void(0);"></a>
                    </li>
                    <li class="channel-item" data-source="http://tv.proyectox.vip:8080/live/ibarrauru2019/PvwxELbOxu/74885.m3u8">
                        <img alt="Logo" src="https://w7.pngwing.com/pngs/148/627/png-transparent-america-television-television-channel-logo-america-tv-others-television-text-logo.png" />
                        <a href="javascript:void(0);"></a>
                    </li>
                </ul>
            </div>
        </div>

        <!-- Contenido principal y sidebar -->
        <div class="content-wrapper">
            <!-- Columna central -->
            <div class="content">
                <h1>PANDONETTV</h1>
                <p>
                    <a href="" target="_blank">
                    </a>
                </p>
            </div>

            <!-- Sidebar -->
            <div class="sidebar">
                <h2>Categorías</h2>
                <ul class="categories-list">
                    <li><a href="index.html">TV</a></li>
                    <li><a href="radio.html">RADIO</a></li>
                </ul>
            </div>
        </div>

        <!-- Gadgets -->
        <div class="gadgets-grid">
            <div class="gadget-item">
                <h3>Juan Jose Navala Moye</h3>
                <a href="https://wa.me/message/QTXCYIEPUNLVC1" target="_blank">
                    <img src="https://i.ibb.co/VWs3g8H0/489459720-9332958076772780-6650694656068909126-n.jpg" alt="WhatsApp">
                </a>
            </div>
            <div class="gadget-item">
                <h3>para instalar y pagar por Qr PANDONETTV desde la app Downloader</h3>
                <a href="https://drive.google.com/file/d/1Vta0YRgb3b0vSB-Zmnm3Wl3qm1SDYrZv/view?usp=sharing" target="_blank">
                    <img src="https://i.ibb.co/q31hC5LL/IMG-20250323-213853.jpg" alt="Downloader">
                </a>
            </div>
            <div class="gadget-item">
                <h3>Download Pandonettv</h3>
                <a href="https://drive.google.com/file/d/1Vta0YRgb3b0vSB-Zmnm3Wl3qm1SDYrZv/view?usp=sharing" target="_blank" target="_blank">
                    <img src="https://i.ibb.co/sdR69vMR/multimedia-61194b1ec505c2e180f86de9abc5f595.png" alt="Pandonettv app">
                </a>
            </div>
        </div>
    </div>

    <script>
        // Inicialización del reproductor de Video.js
        var player = videojs('my-player', {
            autoplay: true,
            controls: true,
            preload: 'auto',
            loop: true,
            muted: false,
            fluid: true
        });

        // Función para cambiar la fuente y reproducir el video
        function changeSource(newSource) {
            player.src({ type: "application/x-mpegURL", src: newSource });
            player.load();
            player.play();
        }

        // Evento de clic en la lista de canales
        document.querySelector('.channels-grid').addEventListener('click', function(event) {
            var channelItem = event.target.closest('.channel-item');
            if (channelItem) {
                var newSource = channelItem.getAttribute('data-source');
                changeSource(newSource);

                // En el diseño actual no necesitamos hacer scroll al reproductor
                // ya que ambos elementos están visibles al mismo tiempo
            }
        });
    </script>
<script defer src="https://static.cloudflareinsights.com/beacon.min.js/vcd15cbe7772f49c399c6a5babf22c1241717689176015" integrity="sha512-ZpsOmlRQV6y907TI0dKBHq9Md29nnaEIPlkf84rnaERnq6zvWvPUqr2ft8M1aS28oN72PdrCzSjY4U6VaAw1EQ==" data-cf-beacon='{"version":"2024.11.0","token":"d41f273397284550956fed833180ecf4","r":1,"server_timing":{"name":{"cfCacheStatus":true,"cfEdge":true,"cfExtPri":true,"cfL4":true,"cfOrigin":true,"cfSpeedBrain":true},"location_startswith":null}}' crossorigin="anonymous"></script>
</body>
</html>
