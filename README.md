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
        a { color: #f1c40f; font-size: 18px; text-decoration: none; margin-top: 20px; }
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
    
    <!-- L'URL de l'adhan -->
    <audio id="adhan" class="adhan" src="http://islammedia.free.fr/audio/Adhan/Adhan_meshary_al_fasy.mp3"></audio>

    <p>Pour plus d'informations, consultez les horaires de prière sur <a href="https://www.guidemusulman.com/horaires-prieres/mensuel/ville/36261/rosny-sous-bois-93110" target="_blank">Guide Musulman</a>.</p>

    <script>
        // Afficher la date actuelle au format français
        function formatDate(date) {
            const days = ['Dimanche', 'Lundi', 'Mardi', 'Mercredi', 'Jeudi', 'Vendredi', 'Samedi'];
            const months = ['Janvier', 'Février', 'Mars', 'Avril', 'Mai', 'Juin', 'Juillet', 'Août', 'Septembre', 'Octobre', 'Novembre', 'Décembre'];
            const day = days[date.getDay()];
            const dayOfMonth = date.getDate();
            const month = months[date.getMonth()];
            const year = date.getFullYear();

            return `${day} ${dayOfMonth} ${month} ${year}`;
        }

        document.getElementById("date").textContent = formatDate(new Date());

        async function getPrayerTimes() {
            const city = "Rosny-sous-Bois";  
            const country = "France";  
            const method = 3;  // MWL (Méthode 12° pour France)

            const url = `https://api.aladhan.com/v1/timingsByCity?city=${city}&country=${country}&method=${method}`;
            const response = await fetch(url);
            const data = await response.json();

            // Affiche la réponse dans la console
            console.log(data);

            if (data.code === 200) {
                const timings = data.data.timings;
                const hijriDate = data.data.date.hijri;
                const gregorianDate = data.data.date.gregorian;

                document.getElementById("date").textContent = `${gregorianDate.weekday.fr} ${gregorianDate.day} ${gregorianDate.month.fr} ${gregorianDate.year} | ${hijriDate.day} ${hijriDate.month.fr} ${hijriDate.year}H`;

                document.getElementById("Fajr").textContent = timings.Fajr;
                document.getElementById("Dhuhr").textContent = timings.Dhuhr;
                document.getElementById("Asr").textContent = timings.Asr;
                document.getElementById("Maghrib").textContent = timings.Maghrib;
                document.getElementById("Isha").textContent = timings.Isha;

                // Vérifier l'heure actuelle et comparer avec les horaires de prière
                const currentTime = new Date();
                const currentHour = currentTime.getHours();
                const currentMinute = currentTime.getMinutes();
                const currentTimeString = `${currentHour}:${currentMinute < 10 ? '0' : ''}${currentMinute}`;

                const prayerTimes = [
                    { time: timings.Fajr, name: "Fajr" },
                    { time: timings.Dhuhr, name: "Dhuhr" },
                    { time: timings.Asr, name: "Asr" },
                    { time: timings.Maghrib, name: "Maghrib" },
                    { time: timings.Isha, name: "Isha" }
                ];

                prayerTimes.forEach(prayer => {
                    if (prayer.time === currentTimeString) {
                        document.getElementById("adhan").play(); // Jouer l'adhan
                    }
                });
            } else {
                console.error("Erreur dans la récupération des horaires");
            }
        }

        getPrayerTimes();
    </script>
</body>
</html>
