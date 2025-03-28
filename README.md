# programesanadarbsgithub
Roberts Silgals, Artūrs Gavars, Niks Strautnieks, Haralds Matīsiņš.
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Weather Dashboard</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            margin: 0;
            padding: 0;
            background: linear-gradient(to right, #4facfe, #00f2fe);
            color: white;
        }
        .container {
            max-width: 600px;
            margin: auto;
            padding: 20px;
            background: rgba(255, 255, 255, 0.2);
            border-radius: 10px;
            margin-top: 20px;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Weather Dashboard</h1>
        <label for="city">Select a City:</label>
        <select id="city" onchange="fetchWeather()">
            <option value="40.7128,-74.0060">New York</option>
            <option value="34.0522,-118.2437">Los Angeles</option>
            <option value="51.5074,-0.1278">London</option>
            <option value="35.6895,139.6917">Tokyo</option>
        </select>
        <p>Temperature: <span id="temperature">...</span>°C</p>
        <p>Humidity: <span id="humidity">...</span>%</p>
        <p>Sunrise: <span id="sunrise">...</span></p>
        <p>Sunset: <span id="sunset">...</span></p>
        <canvas id="temperatureChart"></canvas>
    </div>

    <script>
        async function fetchWeather() {
            const citySelect = document.getElementById('city');
            const [latitude, longitude] = citySelect.value.split(',');
            try {
                const response = await fetch(`https://api.open-meteo.com/v1/forecast?latitude=${latitude}&longitude=${longitude}&daily=temperature_2m_max,relative_humidity_2m_max,sunrise,sunset&timezone=auto`);
                if (!response.ok) throw new Error(`HTTP error! Status: ${response.status}`);
                
                const data = await response.json();
                const daily = data.daily;

                if (!daily || !daily.temperature_2m_max) throw new Error('Invalid API data');

                document.getElementById('temperature').innerText = daily.temperature_2m_max[0] || 'N/A';
                document.getElementById('humidity').innerText = daily.relative_humidity_2m_max[0] || 'N/A';

                // Convert sunrise & sunset (from "2025-03-28T06:30" to "06:30")
                document.getElementById('sunrise').innerText = daily.sunrise[0].split('T')[1] || 'N/A';
                document.getElementById('sunset').innerText = daily.sunset[0].split('T')[1] || 'N/A';

                // Chart
                const ctx = document.getElementById('temperatureChart').getContext('2d');
                if (window.myChart) window.myChart.destroy();

                window.myChart = new Chart(ctx, {
                    type: 'line',
                    data: {
                        labels: daily.temperature_2m_max.map((_, i) => `Day ${i + 1}`),
                        datasets: [{
                            label: 'Temperature (°C)',
                            data: daily.temperature_2m_max,
                            borderColor: 'yellow',
                            fill: false
                        }]
                    }
                });

            } catch (error) {
                console.error('Error fetching weather data:', error);
                document.getElementById('temperature').innerText = 'Error';
                document.getElementById('humidity').innerText = 'Error';
                document.getElementById('sunrise').innerText = 'Error';
                document.getElementById('sunset').innerText = 'Error';
            }
        }
        
        fetchWeather();
    </script>
</body>
</html>
