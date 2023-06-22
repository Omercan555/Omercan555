<!DOCTYPE html>
<html>
<head>
    <title>IP TV Yayınları</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            overflow: hidden;
        }
        .category {
            margin-bottom: 10px;
        }
        .category-title {
            font-weight: bold;
            font-size: 18px;
            margin-bottom: 5px;
        }
        .player {
            width: 100%;
            height: 400px;
            margin-bottom: 10px;
        }
    </style>
</head>
<body>
    <div id="iptv-container"></div>
    <script src="https://cdn.jsdelivr.net/npm/hls.js@latest"></script>
    <script>
        function createPlayer(containerId, streamUrl) {
            var container = document.getElementById(containerId);
            var video = document.createElement('video');
            video.className = 'player';
            video.autoplay = true;
            video.controls = true;
            container.appendChild(video);

            if (Hls.isSupported()) {
                var hls = new Hls();
                hls.loadSource(streamUrl);
                hls.attachMedia(video);
            } else if (video.canPlayType('application/vnd.apple.mpegurl')) {
                video.src = streamUrl;
            }
        }

        function createCategory(categoryName, streams) {
            var iptvContainer = document.getElementById('iptv-container');
            var categoryDiv = document.createElement('div');
            categoryDiv.className = 'category';

            var categoryTitle = document.createElement('div');
            categoryTitle.className = 'category-title';
            categoryTitle.textContent = categoryName;
            categoryDiv.appendChild(categoryTitle);

            streams.forEach(function(stream) {
                createPlayer(categoryDiv.id, stream);
            });

            iptvContainer.appendChild(categoryDiv);
        }

        // M3U URL'sinden yayınları almak için istek gönderme
        var m3uUrl = 'nnnjj';

        fetch(m3uUrl)
            .then(function(response) {
                return response.text();
            })
            .then(function(data) {
                // M3U dosyasından yayınları ayrıştırma
                var lines = data.split('\n');
                var categoryName = '';
                var categoryStreams = [];

                lines.forEach(function(line) {
                    if (line.startsWith('#EXTINF:')) {
                        // Yayın bilgileri satırı
                        var streamInfo = line.split(',')[1];
                        categoryName = streamInfo;
                    } else if (line.startsWith('http')) {
                        // Yayın URL'si
                        categoryStreams.push(line);
                    } else if (line.trim().length === 0) {
                        // Kategori tamamlandı, yeni bir kategori oluşturma
                        createCategory(categoryName, categoryStreams);
                        categoryName = '';
                        categoryStreams = [];
                    }
                });
            })
            .catch(function(error) {
                console.log('Hata:', error);
            });
    </script>
</body>
</html>
