<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mobil Uyumlu Oyun</title>
    <style>
        /* Genel Stil */
        body, html {
            margin: 0;
            padding: 0;
            overflow: hidden;
            font-family: Arial, sans-serif;
        }

        /* Cinsiyet Seçim Ekranı */
        .gender-selection {
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            height: 100vh;
        }
        .gender-selection button {
            margin: 10px;
            padding: 20px 40px;
            font-size: 30px;
            cursor: pointer;
        }

        /* Skor ve Zaman Göstergesi */
        .score-board, .timer {
            position: absolute;
            top: 10px;
            left: 10px;
            background-color: rgba(0, 0, 0, 0.5);
            color: white;
            padding: 10px 20px;
            border-radius: 8px;
            font-size: 24px;
        }
        .timer {
            right: 10px;
            left: auto;
        }

        /* Mobil Uyum İçin Medya Sorgusu */
        @media (max-width: 768px) {
            .gender-selection button {
                padding: 15px 30px;
                font-size: 24px;
            }
            .score-board, .timer {
                font-size: 20px;
                padding: 8px 16px;
            }
        }
    </style>
</head>
<body>

    <!-- Cinsiyet Seçim Ekranı -->
    <div class="gender-selection">
        <button onclick="startGame('male')">♂ Erkek</button>
        <button onclick="startGame('female')">♀ Kadın</button>
    </div>

    <!-- Skor ve Zaman Göstergesi -->
    <div class="score-board" style="display: none;">Skor: <span id="score">0</span></div>
    <div class="timer" style="display: none;">Süre: <span id="time">60</span></div>

    <!-- Oyun Alanı -->
    <canvas id="gameCanvas" style="display: none;"></canvas>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        const genderSelection = document.querySelector('.gender-selection');
        const scoreBoard = document.getElementById('score');
        const timerDisplay = document.getElementById('time');
        let score = 0;
        let timeLeft = 60;
        let gender;
        let selectedPalette;

        const colorPalettes = {
            male: ["#1E90FF", "#4169E1", "#0000FF"],
            female: ["#FF69B4", "#FF1493", "#C71585"]
        };

        // Oyun Başlatma Fonksiyonu
        function startGame(selectedGender) {
            gender = selectedGender;
            genderSelection.style.display = 'none';
            canvas.style.display = 'block';
            document.querySelector('.score-board').style.display = 'flex';
            document.querySelector('.timer').style.display = 'block';

            if (canvas.requestFullscreen) {
                canvas.requestFullscreen();
            }

            selectedPalette = colorPalettes[gender];
            resetGame();
            startTimer();
            update();
        }

        // Kanvas Boyutlandırma
        function resizeCanvas() {
            canvas.width = window.innerWidth;
            canvas.height = window.innerHeight;
        }
        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();

        // Patlama ve Arkaplan Rengini Değiştirme
        function createExplosion(x, y) {
            ctx.beginPath();
            ctx.arc(x, y, 50, 0, Math.PI * 2);
            ctx.fillStyle = selectedPalette[Math.floor(Math.random() * selectedPalette.length)];
            ctx.fill();
        }
        function changeBackgroundColor() {
            document.body.style.backgroundColor = selectedPalette[Math.floor(Math.random() * selectedPalette.length)];
        }

        // Skoru Artırma
        function incrementScore(points) {
            score += points;
            scoreBoard.textContent = score;
        }

        // Sayaç
        function startTimer() {
            const timerInterval = setInterval(() => {
                if (timeLeft > 0) {
                    timeLeft--;
                    timerDisplay.textContent = timeLeft;
                } else {
                    clearInterval(timerInterval);
                    endGame();
                }
            }, 1000);
        }

        // Oyun Güncelleme Döngüsü
        function update() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            requestAnimationFrame(update);
        }

        // Oyun Bittiğinde Çalıştır
        function endGame() {
            alert(`Oyun Bitti! Skorunuz: ${score}`);
            resetGame();
        }

        // Oyunu Sıfırla
        function resetGame() {
            score = 0;
            timeLeft = 60;
            scoreBoard.textContent = score;
            timerDisplay.textContent = timeLeft;
        }

        // Dokunmatik ve Tıklama Desteği
        canvas.addEventListener('click', (event) => {
            handleCanvasClick(event.clientX, event.clientY);
        });
        canvas.addEventListener('touchstart', (event) => {
            const touch = event.touches[0];
            handleCanvasClick(touch.clientX, touch.clientY);
        });

        function handleCanvasClick(x, y) {
            createExplosion(x, y);
            changeBackgroundColor();
            incrementScore(1);
        }
    </script>
</body>
</html>
