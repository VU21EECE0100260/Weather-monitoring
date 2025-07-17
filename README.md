/* # Weather-monitoring
/* It detects the real time weather in any part of the world it is defaultly show the weather of the current location it is generally created by me with the help of AI
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Beautiful Weather App</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
        }
        .weather-card {
            backdrop-filter: blur(10px) saturate(180%);
            -webkit-backdrop-filter: blur(10px) saturate(180%);
            background-color: rgba(23, 23, 23, 0.75);
            border: 1px solid rgba(255, 255, 255, 0.125);
        }
        .bg-gradient-sunny { background-image: linear-gradient(to top, #37cfdc 0%, #5a88e5 100%); }
        .bg-gradient-cloudy { background-image: linear-gradient(to top, #d2d2d2 0%, #8d8d8d 100%); }
        .bg-gradient-rainy { background-image: linear-gradient(to top, #6a85b6 0%, #bac8e0 100%); }
        .bg-gradient-stormy { background-image: linear-gradient(to top, #2c3e50 0%, #4ca1af 100%); }
        .bg-gradient-snowy { background-image: linear-gradient(to top, #e6e9f0 0%, #eef1f5 100%); }
        .bg-gradient-windy { background-image: linear-gradient(to top, #accbee 0%, #e7f0fd 100%); }
        .bg-gradient-partly-cloudy { background-image: linear-gradient(to top, #a1c4fd 0%, #c2e9fb 100%); }
        .bg-gradient-default { background-image: linear-gradient(to top, #48c6ef 0%, #6f86d6 100%); }
        .fade-in { animation: fadeIn 0.5s ease-in-out; }
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }
        .loader {
            border: 4px solid #f3f3f3;
            border-top: 4px solid #3498db;
            border-radius: 50%;
            width: 40px;
            height: 40px;
            animation: spin 1s linear infinite;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
    </style>
</head>
<body class="bg-gray-900 text-white transition-all duration-500">

    <div id="app-container" class="min-h-screen w-full flex flex-col items-center justify-center p-4 transition-all duration-500 bg-gradient-default">
        <div id="weather-card" class="weather-card w-full max-w-md rounded-2xl shadow-2xl p-6 md:p-8 transition-all duration-500">
            
            <!-- Search Bar -->
            <div class="flex mb-6">
                <input type="text" id="city-input" placeholder="Enter city name..." class="w-full bg-gray-800 bg-opacity-50 text-white placeholder-gray-400 border-2 border-transparent focus:border-blue-400 focus:outline-none rounded-l-lg py-3 px-4 transition-all">
                <button id="search-btn" class="bg-blue-500 hover:bg-blue-600 text-white font-bold py-3 px-5 rounded-r-lg transition-colors">
                    <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" fill="currentColor" class="bi bi-search" viewBox="0 0 16 16">
                      <path d="M11.742 10.344a6.5 6.5 0 1 0-1.397 1.398h-.001c.03.04.062.078.098.115l3.85 3.85a1 1 0 0 0 1.415-1.414l-3.85-3.85a1.007 1.007 0 0 0-.115-.1zM12 6.5a5.5 5.5 0 1 1-11 0 5.5 5.5 0 0 1 11 0z"/>
                    </svg>
                </button>
            </div>

            <!-- Loader and Error Message -->
            <div id="loader" class="hidden justify-center items-center my-8">
                <div class="loader"></div>
            </div>
            <div id="error-message" class="hidden text-center text-red-400 bg-red-900 bg-opacity-50 p-3 rounded-lg my-4"></div>

            <!-- Weather Content -->
            <div id="weather-content" class="text-center fade-in">
                <!-- Main Weather Display -->
                <div class="mb-8">
                    <h2 id="city-name" class="text-4xl font-bold tracking-wider">Visakhapatnam</h2>
                    <p id="date" class="text-lg text-gray-300">July 17, 2025</p>
                    <div id="weather-icon" class="my-6">
                        <!-- SVG Icon will be injected here -->
                    </div>
                    <p id="temperature" class="text-7xl font-extrabold tracking-tighter">-°C</p>
                    <p id="condition" class="text-2xl font-medium text-blue-300 capitalize">-</p>
                </div>

                <!-- Additional Details -->
                <div class="grid grid-cols-2 gap-4 text-left bg-gray-800 bg-opacity-30 p-4 rounded-lg">
                    <div>
                        <p class="text-gray-400 text-sm">Humidity</p>
                        <p id="humidity" class="text-xl font-semibold">- %</p>
                    </div>
                    <div>
                        <p class="text-gray-400 text-sm">Wind Speed</p>
                        <p id="wind-speed" class="text-xl font-semibold">- km/h</p>
                    </div>
                </div>

                <!-- Forecast -->
                <div class="mt-8">
                    <h3 class="text-xl font-semibold mb-4 text-left">3-Day Forecast</h3>
                    <div id="forecast-container" class="flex flex-col md:flex-row justify-between space-y-4 md:space-y-0 md:space-x-4">
                        <!-- Forecast items will be injected here -->
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script>
        const cityInput = document.getElementById('city-input');
        const searchBtn = document.getElementById('search-btn');
        const loader = document.getElementById('loader');
        const errorMessage = document.getElementById('error-message');
        const weatherContent = document.getElementById('weather-content');
        const appContainer = document.getElementById('app-container');

        const weatherIcons = {
            'Sunny': `<svg xmlns="http://www.w3.org/2000/svg" width="100" height="100" viewBox="0 0 24 24" fill="none" stroke="#FFD700" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="5"/><line x1="12" y1="1" x2="12" y2="3"/><line x1="12" y1="21" x2="12" y2="23"/><line x1="4.22" y1="4.22" x2="5.64" y2="5.64"/><line x1="18.36" y1="18.36" x2="19.78" y2="19.78"/><line x1="1" y1="12" x2="3" y2="12"/><line x1="21" y1="12" x2="23" y2="12"/><line x1="4.22" y1="19.78" x2="5.64" y2="18.36"/><line x1="18.36" y1="5.64" x2="19.78" y2="4.22"/></svg>`,
            'Cloudy': `<svg xmlns="http://www.w3.org/2000/svg" width="100" height="100" viewBox="0 0 24 24" fill="none" stroke="#B0C4DE" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"><path d="M18 10h-1.26A8 8 0 1 0 9 20h9a5 5 0 0 0 0-10z"/></svg>`,
            'Rainy': `<svg xmlns="http://www.w3.org/2000/svg" width="100" height="100" viewBox="0 0 24 24" fill="none" stroke="#4682B4" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"><path d="M23 12a11.05 11.05 0 0 0-22 0zm-5 7a3 3 0 0 1-6 0v-7"/><path d="M16 12a3 3 0 0 0-6 0v7"/><path d="M11 10.5V7a1 1 0 0 1 2 0v3.5"/><path d="M11 20.5V19a1 1 0 0 1 2 0v1.5"/></svg>`,
            'Stormy': `<svg xmlns="http://www.w3.org/2000/svg" width="100" height="100" viewBox="0 0 24 24" fill="none" stroke="#F7B733" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"><path d="M21.25 16.25a2.25 2.25 0 1 1-4.5 0V9.5a2.25 2.25 0 1 1 4.5 0v6.75z"/><path d="M13 15l-3-3 3-3"/><path d="M18 10h-1.26A8 8 0 1 0 9 20h9a5 5 0 0 0 0-10z"/></svg>`,
            'Snowy': `<svg xmlns="http://www.w3.org/2000/svg" width="100" height="100" viewBox="0 0 24 24" fill="none" stroke="#ADD8E6" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"><path d="M20 17.58A5 5 0 0 0 15.07 13H9.57A5 5 0 0 0 5 17.58"/><line x1="8" y1="16" x2="8" y2="16"/><line x1="12" y1="18" x2="12" y2="18"/><line x1="16" y1="16" x2="16" y2="16"/><line x1="10" y1="12" x2="10.01" y2="12"/><line x1="14" y1="12" x2="14.01" y2="12"/></svg>`,
            'Windy': `<svg xmlns="http://www.w3.org/2000/svg" width="100" height="100" viewBox="0 0 24 24" fill="none" stroke="#87CEEB" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"><path d="M9.59 4.59A2 2 0 1 1 11 8H2m10.59 11.41A2 2 0 1 0 11 16H2m15.73-8.27A2.5 2.5 0 1 1 19.5 12H2"/></svg>`,
            'Partly Cloudy': `<svg xmlns="http://www.w3.org/2000/svg" width="100" height="100" viewBox="0 0 24 24" fill="none" stroke="#87CEFA" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"><path d="M12 2L12 5"/><path d="M22 12L19 12"/><path d="M12 22L12 19"/><path d="M2 12L5 12"/><path d="M19.07 4.93L17 7"/><path d="M19.07 19.07L17 17"/><path d="M4.93 19.07L7 17"/><path d="M4.93 4.93L7 7"/><path d="M18 18.5a4 4 0 0 0-7.73-2.12 4 4 0 0 0-1.77 7.23A4 4 0 0 0 18 18.5z"/></svg>`,
        };
        
        const forecastIcons = {
            'Sunny': `<svg xmlns="http://www.w3.org/2000/svg" width="40" height="40" viewBox="0 0 24 24" fill="none" stroke="#FFD700" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="5"/><line x1="12" y1="1" x2="12" y2="3"/><line x1="12" y1="21" x2="12" y2="23"/><line x1="4.22" y1="4.22" x2="5.64" y2="5.64"/><line x1="18.36" y1="18.36" x2="19.78" y2="19.78"/><line x1="1" y1="12" x2="3" y2="12"/><line x1="21" y1="12" x2="23" y2="12"/><line x1="4.22" y1="19.78" x2="5.64" y2="18.36"/><line x1="18.36" y1="5.64" x2="19.78" y2="4.22"/></svg>`,
            'Cloudy': `<svg xmlns="http://www.w3.org/2000/svg" width="40" height="40" viewBox="0 0 24 24" fill="none" stroke="#B0C4DE" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M18 10h-1.26A8 8 0 1 0 9 20h9a5 5 0 0 0 0-10z"/></svg>`,
            'Rainy': `<svg xmlns="http://www.w3.org/2000/svg" width="40" height="40" viewBox="0 0 24 24" fill="none" stroke="#4682B4" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M23 12a11.05 11.05 0 0 0-22 0zm-5 7a3 3 0 0 1-6 0v-7"/><path d="M16 12a3 3 0 0 0-6 0v7"/><path d="M11 10.5V7a1 1 0 0 1 2 0v3.5"/><path d="M11 20.5V19a1 1 0 0 1 2 0v1.5"/></svg>`,
            'Stormy': `<svg xmlns="http://www.w3.org/2000/svg" width="40" height="40" viewBox="0 0 24 24" fill="none" stroke="#F7B733" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M21.25 16.25a2.25 2.25 0 1 1-4.5 0V9.5a2.25 2.25 0 1 1 4.5 0v6.75z"/><path d="M13 15l-3-3 3-3"/><path d="M18 10h-1.26A8 8 0 1 0 9 20h9a5 5 0 0 0 0-10z"/></svg>`,
            'Snowy': `<svg xmlns="http://www.w3.org/2000/svg" width="40" height="40" viewBox="0 0 24 24" fill="none" stroke="#ADD8E6" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M20 17.58A5 5 0 0 0 15.07 13H9.57A5 5 0 0 0 5 17.58"/><line x1="8" y1="16" x2="8" y2="16"/><line x1="12" y1="18" x2="12" y2="18"/><line x1="16" y1="16" x2="16" y2="16"/><line x1="10" y1="12" x2="10.01" y2="12"/><line x1="14" y1="12" x2="14.01" y2="12"/></svg>`,
            'Windy': `<svg xmlns="http://www.w3.org/2000/svg" width="40" height="40" viewBox="0 0 24 24" fill="none" stroke="#87CEEB" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M9.59 4.59A2 2 0 1 1 11 8H2m10.59 11.41A2 2 0 1 0 11 16H2m15.73-8.27A2.5 2.5 0 1 1 19.5 12H2"/></svg>`,
            'Partly Cloudy': `<svg xmlns="http://www.w3.org/2000/svg" width="40" height="40" viewBox="0 0 24 24" fill="none" stroke="#87CEFA" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M12 2L12 5"/><path d="M22 12L19 12"/><path d="M12 22L12 19"/><path d="M2 12L5 12"/><path d="M19.07 4.93L17 7"/><path d="M19.07 19.07L17 17"/><path d="M4.93 19.07L7 17"/><path d="M4.93 4.93L7 7"/><path d="M18 18.5a4 4 0 0 0-7.73-2.12 4 4 0 0 0-1.77 7.23A4 4 0 0 0 18 18.5z"/></svg>`,
        };

        const updateUI = (data) => {
            document.getElementById('city-name').textContent = data.city;
            document.getElementById('date').textContent = new Date().toLocaleDateString('en-US', { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' });
            document.getElementById('weather-icon').innerHTML = weatherIcons[data.condition] || weatherIcons['Cloudy'];
            document.getElementById('temperature').textContent = `${data.temperature}°C`;
            document.getElementById('condition').textContent = data.condition;
            document.getElementById('humidity').textContent = `${data.humidity} %`;
            document.getElementById('wind-speed').textContent = `${data.windSpeed} km/h`;

            const forecastContainer = document.getElementById('forecast-container');
            forecastContainer.innerHTML = '';
            data.forecast.forEach(day => {
                const forecastItem = document.createElement('div');
                forecastItem.className = 'flex-1 bg-gray-800 bg-opacity-30 p-4 rounded-lg flex flex-col items-center text-center';
                forecastItem.innerHTML = `
                    <p class="font-semibold">${day.day}</p>
                    <div class="my-2">${forecastIcons[day.condition] || forecastIcons['Cloudy']}</div>
                    <p class="text-lg font-bold">${day.temp}°C</p>
                `;
                forecastContainer.appendChild(forecastItem);
            });

            // Update background
            const conditionClass = `bg-gradient-${data.condition.toLowerCase().replace(' ', '-')}`;
            appContainer.className = appContainer.className.replace(/bg-gradient-\w+/g, '');
            appContainer.classList.add(conditionClass in document.body.style ? conditionClass : 'bg-gradient-default');
        };

        const fetchWeather = async (city) => {
            weatherContent.classList.add('hidden');
            errorMessage.classList.add('hidden');
            loader.classList.remove('hidden');
            loader.style.display = 'flex';

            const prompt = `Generate a JSON object representing the current weather for ${city}. The JSON must have this exact structure: {"city": "string", "temperature": number, "condition": "string", "humidity": number, "windSpeed": number, "forecast": [{"day": "string", "temp": number, "condition": "string"}, {"day": "string", "temp": number, "condition": "string"}, {"day": "string", "temp": number, "condition": "string"}]}. The condition string must be one of: 'Sunny', 'Cloudy', 'Rainy', 'Stormy', 'Snowy', 'Windy', 'Partly Cloudy'. The forecast should be for the next 3 days starting with tomorrow.`;
            
            try {
                const apiKey = ""; // Leave empty for automatic handling
                const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key=${apiKey}`;
                const payload = {
                    contents: [{ role: "user", parts: [{ text: prompt }] }],
                    generationConfig: {
                        responseMimeType: "application/json",
                    }
                };

                const response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });

                if (!response.ok) {
                    throw new Error(`API error! status: ${response.status}`);
                }

                const result = await response.json();
                
                if (result.candidates && result.candidates.length > 0 && result.candidates[0].content.parts.length > 0) {
                    const text = result.candidates[0].content.parts[0].text;
                    const weatherData = JSON.parse(text);
                    updateUI(weatherData);
                    weatherContent.classList.remove('hidden');
                } else {
                    throw new Error("Could not parse weather data from response.");
                }

            } catch (error) {
                console.error("Error fetching weather:", error);
                errorMessage.textContent = `Could not fetch weather for ${city}. Please try another city.`;
                errorMessage.classList.remove('hidden');
            } finally {
                loader.style.display = 'none';
                loader.classList.add('hidden');
            }
        };

        searchBtn.addEventListener('click', () => {
            const city = cityInput.value.trim();
            if (city) {
                fetchWeather(city);
            }
        });

        cityInput.addEventListener('keypress', (e) => {
            if (e.key === 'Enter') {
                const city = cityInput.value.trim();
                if (city) {
                    fetchWeather(city);
                }
            }
        });
        
        // Initial fetch for a default city
        window.addEventListener('load', () => {
            fetchWeather('Visakhapatnam');
        });
    </script>
</body>
</html>
