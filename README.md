<!--
    <!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Emergency Contact</title>
    <style>
        :root {
            --primary-color: #007bff;
            --primary-hover: #0056b3;
            --danger-color: #dc3545;
            --warning-color: #ffc107;
            --background-color: #f4f4f4;
            --text-color: #555555;
            --font-family: Arial, sans-serif;
            --border-radius: 8px;
        }

        body {
            font-family: var(--font-family);
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: flex-start;
            padding: 20px;
            background-color: var(--background-color);
            margin: 0;
        }

        #container {
            width: 100%;
            max-width: 400px;
            background-color: #ffffff;
            padding: 20px;
            border-radius: var(--border-radius);
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
            margin-bottom: 20px;
        }

        h1 {
            font-size: 6vw; /* Responsive font size */
            margin-bottom: 20px;
            text-align: center;
            color: var(--primary-color);
        }

        button {
            width: 100%;
            padding: 12px;
            font-size: 4vw; /* Responsive font size */
            color: #ffffff;
            background-color: var(--primary-color);
            border: none;
            border-radius: 5px;
            cursor: pointer;
            margin-top: 10px;
            transition: background-color 0.3s ease, transform 0.1s ease;
        }

        button:active {
            transform: scale(0.98);
        }

        button:hover {
            background-color: var(--primary-hover);
        }

        button:focus {
            outline: 2px solid var(--primary-hover);
        }

        #error,
        #alert,
        #batteryWarning {
            color: var(--danger-color);
            margin-top: 15px;
            display: none;
            font-size: 14px;
            text-align: center;
        }

        #resources,
        #procedures {
            margin-top: 20px;
            font-size: 14px;
            color: var(--text-color);
        }

        #resources p,
        #procedures p {
            margin-bottom: 10px;
        }

        .emergency_procedures a {
            text-decoration: none;
            color: var(--text-color);
            font-weight: bold;
        }

        .emergency_procedures a:hover {
            text-decoration: underline;
        }

        @media (min-width: 768px) {
            h1 {
                font-size: 24px; /* Standard size for larger screens */
            }

            button {
                font-size: 18px; /* Standard size for larger screens */
            }

            #resources,
            #procedures {
                font-size: 14px;
            }
        }

        @media (max-width: 480px) {
            body {
                padding: 10px;
            }

            #resources,
            #procedures {
                font-size: 12px;
            }
        }
    </style>
</head>

<body>
    <div id="container">
        <h1 data-i18n="emergency_contact">Emergency Contact</h1>
        <button id="callButton" aria-label="Call Emergency Contact" data-i18n="call_button">Call Emergency Contact</button>
        <button id="sosButton" aria-label="Send SOS" data-i18n="send_sos">Send SOS Message</button>
        <p id="error" data-i18n="error_message">Your device cannot make phone calls. Please try an alternative method.</p>
        <p id="batteryWarning" data-i18n="battery_warning">Warning: Your battery level is low, which may affect emergency communication.</p>
        <div id="alert" data-i18n="disaster_alert">Fetching disaster alerts...</div>
        <div id="resources" data-i18n="emergency_resources">
            <h2>Nearby Emergency Resources</h2>
            <p id="shelters" data-i18n="shelters">Nearby Shelters: Fetching...</p>
            <p id="hospitals" data-i18n="hospitals">Nearby Hospitals: Fetching...</p>
            <p id="services" data-i18n="services">Emergency Services: Fetching...</p>
        </div>
        <div class="emergency_procedures">
            <a href="emergencyprocedures.html">Emergency Procedures</a>
        </div>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', function () {
            const emergencyNumber = sessionStorage.getItem('emergencyNumber') || '+916363101505';
            const callButton = document.getElementById('callButton');
            const sosButton = document.getElementById('sosButton');
            const errorElement = document.getElementById('error');
            const alertElement = document.getElementById('alert');
            const batteryWarning = document.getElementById('batteryWarning');
            const shelters = document.getElementById('shelters');
            const hospitals = document.getElementById('hospitals');
            const services = document.getElementById('services');

            const i18nStrings = {
                en: {
                    emergency_contact: 'Emergency Contact',
                    call_button: 'Call Emergency Contact',
                    send_sos: 'Send SOS Message',
                    error_message: 'Your device cannot make phone calls. Please try an alternative method.',
                    battery_warning: 'Warning: Your battery level is low, which may affect emergency communication.',
                    disaster_alert: 'Fetching disaster alerts...',
                    emergency_resources: 'Nearby Emergency Resources',
                    shelters: 'Nearby Shelters: Fetching...',
                    hospitals: 'Nearby Hospitals: Fetching...',
                    services: 'Emergency Services: Fetching...',
                    emergency_procedures: 'Emergency Procedures',
                },
                es: {
                    emergency_contact: 'Contacto de Emergencia',
                    call_button: 'Llamar a Contacto de Emergencia',
                    send_sos: 'Enviar Mensaje SOS',
                    error_message: 'Su dispositivo no puede hacer llamadas telefónicas. Por favor, intente otro método.',
                    battery_warning: 'Advertencia: Su nivel de batería es bajo, lo que puede afectar la comunicación de emergencia.',
                    disaster_alert: 'Obteniendo alertas de desastres...',
                    emergency_resources: 'Recursos de Emergencia Cercanos',
                    shelters: 'Refugios Cercanos: Obteniendo...',
                    hospitals: 'Hospitales Cercanos: Obteniendo...',
                    services: 'Servicios de Emergencia: Obteniendo...',
                    emergency_procedures: 'Procedimientos de Emergencia',
                }
            };

            const userLang = navigator.language || navigator.userLanguage;
            const lang = i18nStrings[userLang.split('-')[0]] ? userLang.split('-')[0] : 'en';

            document.querySelectorAll('[data-i18n]').forEach(element => {
                element.textContent = i18nStrings[lang][element.getAttribute('data-i18n')];
            });

            if (navigator.getBattery) {
                navigator.getBattery().then(function (battery) {
                    if (battery.level < 0.2) {
                        batteryWarning.style.display = 'block';
                    }
                });
            }

            function fetchDisasterAlerts() {
                setTimeout(function () {
                    alertElement.textContent = "No current disaster alerts in your area.";
                }, 2000);
            }

            function fetchNearbyResources() {
                setTimeout(function () {
                    shelters.textContent = "Nearby Shelters: Shelter A, Shelter B";
                    hospitals.textContent = "Nearby Hospitals: Hospital X, Hospital Y";
                    services.textContent = "Emergency Services: Police Station, Fire Station";
                }, 2000);
            }

            fetchDisasterAlerts();
            fetchNearbyResources();

            callButton.addEventListener('click', function () {
                const isTelSupported = document.createElement('a').href = `tel:${emergencyNumber}`;
                const isMobileDevice = /Android|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini|Mobile/i.test(navigator.userAgent);

                if (isMobileDevice && isTelSupported) {
                    if (confirm(i18nStrings[lang].call_button + "?")) {
                        window.location.href = `tel:${emergencyNumber}`;
                    }
                } else {
                    errorElement.style.display = 'block';
                }
            });

            sosButton.addEventListener('click', function () {
                // Add SOS message sending logic here
                alert("SOS message sent to your emergency contact.");
            });
        });
    </script>
</body>

</html>
-->


<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Emergency Contact</title>
    <style>
        :root {
            --primary-color: #007bff;
            --primary-hover: #0056b3;
            --danger-color: #dc3545;
            --warning-color: #ffc107;
            --background-color: #f4f4f4;
            --text-color: #555555;
            --font-family: Arial, sans-serif;
            --border-radius: 8px;
        }

        body {
            font-family: var(--font-family);
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: flex-start;
            padding: 20px;
            background-color: var(--background-color);
            margin: 0;
        }

        #container {
            width: 100%;
            max-width: 400px;
            background-color: #ffffff;
            padding: 20px;
            border-radius: var(--border-radius);
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
            margin-bottom: 20px;
        }

        h1 {
            font-size: 24px;
            margin-bottom: 20px;
            text-align: center;
            color: var(--primary-color);
        }

        button {
            width: 100%;
            padding: 12px;
            font-size: 18px;
            color: #ffffff;
            background-color: var(--primary-color);
            border: none;
            border-radius: 5px;
            cursor: pointer;
            margin-top: 10px;
            transition: background-color 0.3s ease, transform 0.1s ease;
        }

        button:active {
            transform: scale(0.98);
        }

        button:hover {
            background-color: var(--primary-hover);
        }

        button:focus {
            outline: 2px solid var(--primary-hover);
        }

        #error,
        #alert,
        #batteryWarning {
            color: var(--danger-color);
            margin-top: 15px;
            display: none;
            font-size: 14px;
            text-align: center;
        }

        #resources,
        #procedures {
            margin-top: 20px;
            font-size: 14px;
            color: var(--text-color);
        }

        #resources p,
        #procedures p {
            margin-bottom: 10px;
        }

        .emergency_procedures a {
            text-decoration: none;
            color: var(--text-color);
            font-weight: bold;
        }

        .emergency_procedures a:hover {
            text-decoration: underline;
        }

        @media (max-width: 480px) {
            body {
                padding: 10px;
            }

            h1 {
                font-size: 20px;
            }

            button {
                font-size: 16px;
            }

            #resources,
            #procedures {
                font-size: 12px;
            }
        }
    </style>
</head>

<body>
    <div id="container">
        <h1 data-i18n="emergency_contact">Emergency Contact</h1>
        <button id="callButton" aria-label="Call Emergency Contact" data-i18n="call_button">Call Emergency Contact</button>
        <button id="sosButton" aria-label="Send SOS" data-i18n="send_sos">Send SOS Message</button>
        <p id="error" data-i18n="error_message">Your device cannot make phone calls. Please try an alternative method.</p>
        <p id="batteryWarning" data-i18n="battery_warning">Warning: Your battery level is low, which may affect emergency communication.</p>
        <div id="alert" data-i18n="disaster_alert">Fetching disaster alerts...</div>
        <div id="resources" data-i18n="emergency_resources">
            <h2>Nearby Emergency Resources</h2>
            <p id="shelters" data-i18n="shelters">Nearby Shelters: Fetching...</p>
            <p id="hospitals" data-i18n="hospitals">Nearby Hospitals: Fetching...</p>
            <p id="services" data-i18n="services">Emergency Services: Fetching...</p>
        </div>
        <div class="emergency_procedures">
            <a href="emergencyprocedures.html">Emergency Procedures</a>
        </div>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', function () {
            const emergencyNumber = sessionStorage.getItem('emergencyNumber') || '+916363101505';
            const callButton = document.getElementById('callButton');
            const sosButton = document.getElementById('sosButton');
            const errorElement = document.getElementById('error');
            const alertElement = document.getElementById('alert');
            const batteryWarning = document.getElementById('batteryWarning');
            const shelters = document.getElementById('shelters');
            const hospitals = document.getElementById('hospitals');
            const services = document.getElementById('services');

            const i18nStrings = {
                en: {
                    emergency_contact: 'Emergency Contact',
                    call_button: 'Call Emergency Contact',
                    send_sos: 'Send SOS Message',
                    error_message: 'Your device cannot make phone calls. Please try an alternative method.',
                    battery_warning: 'Warning: Your battery level is low, which may affect emergency communication.',
                    disaster_alert: 'Fetching disaster alerts...',
                    emergency_resources: 'Nearby Emergency Resources',
                    shelters: 'Nearby Shelters: Fetching...',
                    hospitals: 'Nearby Hospitals: Fetching...',
                    services: 'Emergency Services: Fetching...',
                    emergency_procedures: 'Emergency Procedures',
                    earthquake_procedure: 'Earthquake: Drop, Cover, and Hold On.',
                    flood_procedure: 'Flood: Move to higher ground and avoid floodwaters.',
                    fire_procedure: 'Fire: Evacuate the building and call emergency services.'
                },
                es: {
                    emergency_contact: 'Contacto de Emergencia',
                    call_button: 'Llamar a Contacto de Emergencia',
                    send_sos: 'Enviar Mensaje SOS',
                    error_message: 'Su dispositivo no puede hacer llamadas telefónicas. Por favor, intente otro método.',
                    battery_warning: 'Advertencia: Su nivel de batería es bajo, lo que puede afectar la comunicación de emergencia.',
                    disaster_alert: 'Obteniendo alertas de desastres...',
                    emergency_resources: 'Recursos de Emergencia Cercanos',
                    shelters: 'Refugios Cercanos: Obteniendo...',
                    hospitals: 'Hospitales Cercanos: Obteniendo...',
                    services: 'Servicios de Emergencia: Obteniendo...',
                    emergency_procedures: 'Procedimientos de Emergencia',
                    earthquake_procedure: 'Terremoto: Agáchate, cúbrete y agárrate.',
                    flood_procedure: 'Inundación: Muévete a un lugar más alto y evita las aguas de la inundación.',
                    fire_procedure: 'Incendio: Evacúa el edificio y llama a los servicios de emergencia.'
                }
            };

            const userLang = navigator.language || navigator.userLanguage;
            const lang = i18nStrings[userLang.split('-')[0]] ? userLang.split('-')[0] : 'en';

            document.querySelectorAll('[data-i18n]').forEach(element => {
                element.textContent = i18nStrings[lang][element.getAttribute('data-i18n')];
            });

            if (navigator.getBattery) {
                navigator.getBattery().then(function (battery) {
                    if (battery.level < 0.2) {
                        batteryWarning.style.display = 'block';
                    }
                });
            }

            function fetchDisasterAlerts() {
                setTimeout(function () {
                    alertElement.textContent = "No current disaster alerts in your area.";
                }, 2000);
            }

            function fetchNearbyResources() {
                setTimeout(function () {
                    shelters.textContent = "Nearby Shelters: Shelter A, Shelter B";
                    hospitals.textContent = "Nearby Hospitals: Hospital X, Hospital Y";
                    services.textContent = "Emergency Services: Police Station, Fire Station";
                }, 2000);
            }

            fetchDisasterAlerts();
            fetchNearbyResources();

            callButton.addEventListener('click', function () {
                const isTelSupported = 'tel' in document.createElement('a');
                const isMobileDevice = /Android|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini|Mobile/i.test(navigator.userAgent);

                if (isMobileDevice && isTelSupported) {
                    if (confirm(i18nStrings[lang].call_button + "?")) {
                        window.location.href = `tel:${emergencyNumber}`;
                    }
                } else {
                    errorElement.style.display = 'block';
                }
            });

            sosButton.addEventListener('click', function () {
                alert('SOS Message Sent!');
            });
        });
    </script>
</body>

</html>
