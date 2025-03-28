<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, orientation=landscape">
    <title>Horaires de Prière</title>
    <style>
        body { 
            background-color: black; 
            color: white; 
            font-family: Arial, sans-serif; 
            text-align: center; 
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            height: 100vh;
            overflow: hidden;
        }
        h1 { font-size: 40px; margin: 10px 0; }
        #date { font-size: 30px; margin-bottom: 20px; }
        .prayer-times {
            display: flex;
            justify-content: center;
            gap: 30px;
        }
        .prayer {
            font-size: 50px;
            font-weight: bold;
        }
        .adhan { display: none; }
    </style>
</head>
<body>
    <h1>Horaires de Prière - Rosny-sous-Bois</h1>
    <div id="date">Chargement...</div>

    <div class="prayer-times">
        <p class="prayer">Fajr : <span id="Fajr">--:--</span></p>
        <p class="prayer">Dhuhr : <span id="Dhuhr">--:--</span></p>
        <p class="prayer">Asr : <span id="Asr">--:--</span></p>
        <p class="prayer">Maghrib : <span id="Maghrib">--:--</span></p>
        <p class="prayer">Isha : <span id="Isha">--:--</span></p>
    </div>
    
    <audio id="adhan" class="adhan" src="https://www.islamcan.com/audio/adhan/azan1.mp3"></audio>

    <script>
        async function getPrayerTimes() {
            const city = "Rosny-sous-Bois";  
            const country = "France";  
            const method = 3;  // Méthode MWL = 12° (UOIF France)

            const url = `http://api.aladhan.com/v1/timingsByCity?city=${city}&country=${country}&method=${method}`;
            const response = await fetch(url);
            const data = await response.json();
            const timings = data.data.timings;
            const hijriDate = data.data.date.hijri;
            const gregorianDate = data.data.date.gregorian;

            document.getElementById("date").textContent = `${gregorianDate.weekday.en} ${gregorianDate.day} ${gregorianDate.month.en} ${gregorianDate.year} | ${hijriDate.day} ${hijriDate.month.en} ${hijriDate.year}H`;

            document.getElementById("Fajr").textContent = timings.Fajr;
            document.getElementById("Dhuhr").textContent = timings.Dhuhr;
            document.getElementById("Asr").textContent = timings.Asr;
            document.getElementById("Maghrib").textContent = timings.Maghrib;
            document.getElementById("Isha").textContent = timings.Isha;

            checkAdhanTimes(timings);
        }

        function checkAdhanTimes(timings) {
            setInterval(() => {
                const now = new Date();
                const currentTime = now.getHours().toString().padStart(2, '0') + ":" + now.getMinutes().toString().padStart(2, '0');

                Object.keys(timings).forEach(prayer => {
                    if (timings[prayer] === currentTime) {
                        document.getElementById("adhan").play();
                    }
                });
            }, 60000); // Vérifie chaque minute
        }

        getPrayerTimes();
    </script>
</body>
</html>
