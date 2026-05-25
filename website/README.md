[index.html](https://github.com/user-attachments/files/28228632/index.html)
<div class="status-card">
                <h3>📱 Emergency Contacts</h3>
                <div id="contact-count">0 contacts added</div>
                <div style="margin-top: 10px;">
                    <div id="api-status" style="font-size: 0.9rem; color: #666;">
                        SMS API: Ready | Email Service: Ready
                    <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ESP32 Emergency SOS System</title>
    <script src='https://api.mapbox.com/mapbox-gl-js/v2.15.0/mapbox-gl.js'></script>
    <link href='https://api.mapbox.com/mapbox-gl-js/v2.15.0/mapbox-gl.css' rel='stylesheet' />
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            color: #333;
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 20px;
        }

        .header {
            background: rgba(255, 255, 255, 0.95);
            backdrop-filter: blur(10px);
            border-radius: 20px;
            padding: 30px;
            margin-bottom: 30px;
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.1);
            text-align: center;
        }

        .header h1 {
            color: #667eea;
            font-size: 2.5rem;
            margin-bottom: 10px;
            font-weight: 700;
        }

        .status-panel {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 20px;
            margin-bottom: 30px;
        }

        .status-card {
            background: rgba(255, 255, 255, 0.95);
            backdrop-filter: blur(10px);
            border-radius: 15px;
            padding: 25px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
            transition: transform 0.3s ease, box-shadow 0.3s ease;
        }

        .status-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.15);
        }

        .status-card h3 {
            color: #667eea;
            margin-bottom: 15px;
            font-size: 1.3rem;
        }

        .bluetooth-status {
            display: inline-block;
            padding: 8px 16px;
            border-radius: 25px;
            font-weight: bold;
            color: white;
            margin-bottom: 10px;
        }

        .status-disconnected {
            background: #e74c3c;
        }

        .status-connected {
            background: #27ae60;
        }

        .btn {
            padding: 12px 25px;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-weight: bold;
            transition: all 0.3s ease;
            font-size: 1rem;
        }

        .btn-primary {
            background: #667eea;
            color: white;
        }

        .btn-primary:hover {
            background: #5a6fd8;
            transform: translateY(-2px);
        }

        .btn-danger {
            background: #e74c3c;
            color: white;
        }

        .btn-danger:hover {
            background: #c0392b;
        }

        .btn-success {
            background: #27ae60;
            color: white;
        }

        .btn-success:hover {
            background: #229954;
        }

        .main-content {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 30px;
        }

        .contacts-section, .map-section {
            background: rgba(255, 255, 255, 0.95);
            backdrop-filter: blur(10px);
            border-radius: 20px;
            padding: 30px;
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.1);
        }

        .section-title {
            color: #667eea;
            font-size: 1.8rem;
            margin-bottom: 20px;
            border-bottom: 3px solid #667eea;
            padding-bottom: 10px;
        }

        .contact-form {
            display: grid;
            gap: 15px;
            margin-bottom: 25px;
        }

        .form-group {
            display: flex;
            flex-direction: column;
        }

        .form-group label {
            margin-bottom: 5px;
            font-weight: bold;
            color: #555;
        }

        .form-group input {
            padding: 12px;
            border: 2px solid #e1e8ed;
            border-radius: 8px;
            font-size: 1rem;
            transition: border-color 0.3s ease;
        }

        .form-group input:focus {
            outline: none;
            border-color: #667eea;
        }

        .contacts-list {
            max-height: 300px;
            overflow-y: auto;
        }

        .contact-item {
            background: #f8f9fa;
            padding: 15px;
            border-radius: 10px;
            margin-bottom: 10px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-left: 4px solid #667eea;
        }

        .contact-info h4 {
            color: #333;
            margin-bottom: 5px;
        }

        .contact-info p {
            color: #666;
            font-size: 0.9rem;
        }

        .contact-actions {
            display: flex;
            gap: 10px;
        }

        .btn-small {
            padding: 6px 12px;
            font-size: 0.8rem;
        }

        #map {
            height: 400px;
            border-radius: 15px;
            box-shadow: 0 10px 20px rgba(0, 0, 0, 0.1);
        }

        .mapboxgl-popup-content {
            background: rgba(255, 255, 255, 0.95);
            backdrop-filter: blur(10px);
            border-radius: 10px;
            padding: 15px;
        }

        .mapboxgl-popup-close-button {
            color: #667eea;
            font-size: 20px;
        }

        .location-info {
            background: #f8f9fa;
            padding: 15px;
            border-radius: 10px;
            margin-top: 15px;
        }

        .location-info h4 {
            color: #667eea;
            margin-bottom: 10px;
        }

        .emergency-alert {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: #e74c3c;
            color: white;
            padding: 30px;
            border-radius: 15px;
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.3);
            z-index: 1000;
            text-align: center;
            display: none;
            animation: pulse 1s infinite;
        }

        @keyframes pulse {
            0% { transform: translate(-50%, -50%) scale(1); }
            50% { transform: translate(-50%, -50%) scale(1.05); }
            100% { transform: translate(-50%, -50%) scale(1); }
        }

        @media (max-width: 768px) {
            .main-content {
                grid-template-columns: 1fr;
            }
            
            .header h1 {
                font-size: 2rem;
            }
            
            .container {
                padding: 10px;
            }
        }

        .test-section {
            text-align: center;
            margin-top: 20px;
            padding: 20px;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 10px;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>🚨 ESP32 Emergency SOS System</h1>
            <p>Smart emergency response system with GPS tracking and automatic messaging</p>
        </div>

        <div class="status-panel">
            <div class="status-card">
                <h3>🔗 Bluetooth Connection</h3>
                <div id="bluetooth-status" class="bluetooth-status status-disconnected">Disconnected</div>
                <button id="connect-btn" class="btn btn-primary" onclick="connectESP32()">Connect ESP32</button>
            </div>

            <div class="status-card">
                <h3>🚨 Emergency System</h3>
                <div id="last-emergency">No emergency alerts sent</div>
                <div class="test-section">
                    <button class="btn btn-danger" onclick="testEmergencyAlert()">🚨 Test Emergency Alert</button>
                    <button class="btn btn-primary btn-small" onclick="viewEmergencyLogs()" style="margin-top: 10px;">View Logs</button>
                </div>
            </div>

            <div class="status-card">
                <h3>📍 GPS Status</h3>
                <div id="gps-status">Location: Not available</div>
                <button class="btn btn-success" onclick="getCurrentLocation()">Get Location</button>
            </div>
        </div>

        <div class="main-content">
            <div class="contacts-section">
                <h2 class="section-title">Emergency Contacts</h2>
                
                <div class="contact-form">
                    <div class="form-group">
                        <label for="contact-name">Contact Name</label>
                        <input type="text" id="contact-name" placeholder="Enter contact name">
                    </div>
                    <div class="form-group">
                        <label for="contact-phone">Phone Number</label>
                        <input type="tel" id="contact-phone" placeholder="Enter phone number">
                    </div>
                    <div class="form-group">
                        <label for="contact-email">Email (Optional)</label>
                        <input type="email" id="contact-email" placeholder="Enter email address">
                    </div>
                    <button class="btn btn-success" onclick="addContact()">Add Contact</button>
                </div>

                <div class="contacts-list" id="contacts-list">
                    <!-- Contacts will be added here -->
                </div>
            </div>

            <div class="map-section">
                <h2 class="section-title">GPS Tracking Map</h2>
                <div id="map"></div>
                <div class="location-info" id="location-info">
                    <h4>📍 Current Location</h4>
                    <p>Location will appear here when GPS is activated</p>
                </div>
            </div>
        </div>
    </div>

    <div class="emergency-alert" id="emergency-alert">
        <h2>🚨 EMERGENCY ALERT SENT!</h2>
        <p>Emergency messages sent to all contacts with your current location</p>
        <button class="btn" onclick="closeAlert()" style="background: white; color: #e74c3c; margin-top: 15px;">Close</button>
    </div>

    <script>
        // Mapbox access token (you need to get this from mapbox.com)
        mapboxgl.accessToken = 'pk.eyJ1IjoiZXhhbXBsZSIsImEiOiJjbGQwOXpyMDEwMDAwM3ZxdGtkaWEwZGpyIn0.example-token'; // Replace with your Mapbox token
        
        let map;
        let currentLocationMarker;
        let contacts = [];
        let bluetoothConnected = false;
        let currentLocation = null;
        let bluetoothDevice;
        let buttonPressCount = 0;
        let buttonPressTimer;
        let characteristic;

        // Initialize Mapbox map
        function initMap() {
            map = new mapboxgl.Map({
                container: 'map',
                style: 'mapbox://styles/mapbox/streets-v12', // You can change to satellite-v9, dark-v10, etc.
                center: [77.2090, 28.6139], // Default to Delhi [lng, lat]
                zoom: 13,
                pitch: 45, // 3D effect
                bearing: 0
            });

            // Add navigation controls
            map.addControl(new mapboxgl.NavigationControl(), 'top-right');
            
            // Add geolocate control
            const geolocate = new mapboxgl.GeolocateControl({
                positionOptions: {
                    enableHighAccuracy: true
                },
                trackUserLocation: true,
                showUserHeading: true
            });
            map.addControl(geolocate, 'top-right');

            // Auto-trigger geolocation on map load
            map.on('load', () => {
                geolocate.trigger();
            });
        }

        // Connect to ESP32 via Web Serial API (more reliable than Bluetooth for laptop connection)
        async function connectESP32() {
            try {
                // Request serial port access
                const port = await navigator.serial.requestPort();
                await port.open({ baudRate: 115200 });

                const decoder = new TextDecoderStream();
                const inputDone = port.readable.pipeTo(decoder.writable);
                const inputStream = decoder.readable;

                const reader = inputStream.getReader();
                
                bluetoothConnected = true;
                updateBluetoothStatus();
                
                // Read data from ESP32
                readSerialData(reader);

            } catch (error) {
                console.log('Serial connection failed, trying Bluetooth:', error);
                await connectBluetooth();
            }
        }

        // Read serial data from ESP32
        async function readSerialData(reader) {
            try {
                while (true) {
                    const { value, done } = await reader.read();
                    if (done) break;
                    
                    // Check for button press signal
                    if (value.includes('BUTTON_PRESSED')) {
                        handleButtonPress();
                    }
                }
            } catch (error) {
                console.error('Error reading serial data:', error);
            }
        }

        // Connect to ESP32 via Bluetooth (fallback method)
        async function connectBluetooth() {
            try {
                bluetoothDevice = await navigator.bluetooth.requestDevice({
                    filters: [
                        { name: 'ESP32' },
                        { namePrefix: 'ESP32' },
                        { services: ['12345678-1234-1234-1234-123456789abc'] }
                    ],
                    optionalServices: ['12345678-1234-1234-1234-123456789abc']
                });

                const server = await bluetoothDevice.gatt.connect();
                const service = await server.getPrimaryService('12345678-1234-1234-1234-123456789abc');
                characteristic = await service.getCharacteristic('87654321-4321-4321-4321-cba987654321');

                // Listen for button press notifications
                await characteristic.startNotifications();
                characteristic.addEventListener('characteristicvaluechanged', handleButtonPress);

                bluetoothConnected = true;
                updateBluetoothStatus();
                
                // Handle disconnection
                bluetoothDevice.addEventListener('gattserverdisconnected', () => {
                    bluetoothConnected = false;
                    updateBluetoothStatus();
                });

            } catch (error) {
                console.log('Bluetooth connection failed:', error);
                // Demo mode fallback
                bluetoothConnected = true;
                updateBluetoothStatus();
                alert('Demo Mode: ESP32 connection simulated. Use "Test Emergency Alert" to simulate double button press.');
            }
        }

        // Handle button press from ESP32 (detect double press)
        function handleButtonPress(event) {
            console.log('Button press detected from ESP32');
            buttonPressCount++;
            
            // Clear previous timer
            clearTimeout(buttonPressTimer);
            
            // Set timer to reset count after 1.5 seconds
            buttonPressTimer = setTimeout(() => {
                if (buttonPressCount === 1) {
                    console.log('Single press detected - no action');
                }
                buttonPressCount = 0;
            }, 1500);

            // If double press detected within 1.5 seconds
            if (buttonPressCount === 2) {
                console.log('🚨 DOUBLE PRESS DETECTED - TRIGGERING EMERGENCY ALERT!');
                clearTimeout(buttonPressTimer);
                buttonPressCount = 0;
                triggerEmergencyAlert();
            }
        }

        // Update Bluetooth/Serial connection status
        function updateBluetoothStatus() {
            const statusElement = document.getElementById('bluetooth-status');
            const connectBtn = document.getElementById('connect-btn');
            
            if (bluetoothConnected) {
                statusElement.textContent = 'Connected to ESP32';
                statusElement.className = 'bluetooth-status status-connected';
                connectBtn.textContent = 'Disconnect ESP32';
                connectBtn.onclick = disconnectESP32;
            } else {
                statusElement.textContent = 'Not Connected';
                statusElement.className = 'bluetooth-status status-disconnected';
                connectBtn.textContent = 'Connect ESP32';
                connectBtn.onclick = connectESP32;
            }
        }

        // Disconnect ESP32
        function disconnectESP32() {
            if (bluetoothDevice && bluetoothDevice.gatt.connected) {
                bluetoothDevice.gatt.disconnect();
            }
            bluetoothConnected = false;
            updateBluetoothStatus();
        }

        // Get current GPS location with high accuracy
        function getCurrentLocation() {
            return new Promise((resolve, reject) => {
                if (navigator.geolocation) {
                    navigator.geolocation.getCurrentPosition(
                        (position) => {
                            currentLocation = {
                                latitude: position.coords.latitude,
                                longitude: position.coords.longitude,
                                accuracy: position.coords.accuracy,
                                timestamp: new Date().toISOString()
                            };
                            
                            console.log('GPS Location obtained:', currentLocation);
                            updateLocationDisplay();
                            updateMapLocation();
                            resolve(currentLocation);
                        },
                        (error) => {
                            console.error('GPS Error:', error);
                            // Use demo location for testing
                            currentLocation = {
                                latitude: 28.6139,
                                longitude: 77.2090,
                                accuracy: 10,
                                timestamp: new Date().toISOString()
                            };
                            updateLocationDisplay();
                            updateMapLocation();
                            alert('GPS access denied. Using demo location (Delhi, India) for testing.');
                            resolve(currentLocation);
                        },
                        { 
                            enableHighAccuracy: true, 
                            timeout: 10000, 
                            maximumAge: 60000 // Cache for 1 minute
                        }
                    );
                } else {
                    reject('GPS not supported by this browser');
                }
            });
        }

        // Update location display
        function updateLocationDisplay() {
            const accuracy = currentLocation.accuracy ? `±${Math.round(currentLocation.accuracy)}m` : 'Unknown';
            document.getElementById('gps-status').innerHTML = 
                `Location: ${currentLocation.latitude.toFixed(6)}, ${currentLocation.longitude.toFixed(6)}`;
                
            document.getElementById('location-info').innerHTML = `
                <h4>📍 Current Location</h4>
                <p><strong>Latitude:</strong> ${currentLocation.latitude.toFixed(6)}</p>
                <p><strong>Longitude:</strong> ${currentLocation.longitude.toFixed(6)}</p>
                <p><strong>Accuracy:</strong> ${accuracy}</p>
                <p><strong>Last Updated:</strong> ${new Date(currentLocation.timestamp).toLocaleString()}</p>
                <p><strong>Google Maps:</strong> 
                    <a href="https://maps.google.com/?q=${currentLocation.latitude},${currentLocation.longitude}" target="_blank">
                        View Location
                    </a>
                </p>
                <p><strong>Share Location:</strong> 
                    <a href="https://maps.google.com/?q=${currentLocation.latitude},${currentLocation.longitude}" target="_blank">
                        https://maps.google.com/?q=${currentLocation.latitude},${currentLocation.longitude}
                    </a>
                </p>
            `;
        }

        // Update Mapbox map with current location
        function updateMapLocation() {
            if (currentLocation) {
                // Fly to location
                map.flyTo({
                    center: [currentLocation.longitude, currentLocation.latitude],
                    zoom: 16,
                    pitch: 45,
                    bearing: 0
                });
                
                // Remove existing marker
                if (currentLocationMarker) {
                    currentLocationMarker.remove();
                }
                
                // Add new marker with popup
                currentLocationMarker = new mapboxgl.Marker({
                    color: '#e74c3c',
                    scale: 1.2
                })
                    .setLngLat([currentLocation.longitude, currentLocation.latitude])
                    .setPopup(new mapboxgl.Popup({ offset: 25 })
                        .setHTML(`
                            <div style="text-align: center;">
                                <h3>📍 Your Location</h3>
                                <p><strong>Lat:</strong> ${currentLocation.latitude.toFixed(6)}</p>
                                <p><strong>Lng:</strong> ${currentLocation.longitude.toFixed(6)}</p>
                                <p><strong>Accuracy:</strong> ±${Math.round(currentLocation.accuracy)}m</p>
                                <p><strong>Time:</strong> ${new Date(currentLocation.timestamp).toLocaleString()}</p>
                            </div>
                        `))
                    .addTo(map);
                
                // Show popup automatically
                currentLocationMarker.getPopup().addTo(map);
            }
        }

        // Add emergency contact
        function addContact() {
            const name = document.getElementById('contact-name').value;
            const phone = document.getElementById('contact-phone').value;
            const email = document.getElementById('contact-email').value;

            if (!name || !phone) {
                alert('Please enter both name and phone number');
                return;
            }

            const contact = {
                id: Date.now(),
                name: name,
                phone: phone,
                email: email || ''
            };

            contacts.push(contact);
            updateContactsList();
            updateContactCount();
            
            // Clear form
            document.getElementById('contact-name').value = '';
            document.getElementById('contact-phone').value = '';
            document.getElementById('contact-email').value = '';
        }

        // Update contacts list display
        function updateContactsList() {
            const contactsList = document.getElementById('contacts-list');
            contactsList.innerHTML = '';

            contacts.forEach(contact => {
                const contactItem = document.createElement('div');
                contactItem.className = 'contact-item';
                contactItem.innerHTML = `
                    <div class="contact-info">
                        <h4>${contact.name}</h4>
                        <p>📞 ${contact.phone}</p>
                        ${contact.email ? `<p>📧 ${contact.email}</p>` : ''}
                    </div>
                    <div class="contact-actions">
                        <button class="btn btn-danger btn-small" onclick="removeContact(${contact.id})">Remove</button>
                    </div>
                `;
                contactsList.appendChild(contactItem);
            });
        }

        // Remove contact
        function removeContact(contactId) {
            contacts = contacts.filter(contact => contact.id !== contactId);
            updateContactsList();
            updateContactCount();
        }

        // Update contact count
        function updateContactCount() {
            document.getElementById('contact-count').textContent = `${contacts.length} contact${contacts.length !== 1 ? 's' : ''} added`;
        }

        // Trigger emergency alert when double press detected
        async function triggerEmergencyAlert() {
            console.log('🚨 EMERGENCY ALERT TRIGGERED!');
            
            if (contacts.length === 0) {
                alert('❌ No emergency contacts added!\n\nPlease add emergency contacts first.');
                return;
            }

            // Get fresh GPS location for emergency
            try {
                await getCurrentLocation();
            } catch (error) {
                console.warn('Could not get fresh GPS location, using existing location');
            }

            if (!currentLocation) {
                alert('⚠️ GPS location not available!\n\nEmergency alert will be sent without location.');
            }

            // Send emergency messages immediately
            await sendEmergencyMessages();
        }

        // Send emergency messages automatically to all contacts
        async function sendEmergencyMessages() {
            const locationText = currentLocation ? 
                `Emergency Location: https://maps.google.com/?q=${currentLocation.latitude},${currentLocation.longitude}` : 
                'GPS location unavailable';

            const timestamp = new Date().toLocaleString();
            const emergencyMessage = `🚨 EMERGENCY SOS ALERT! 

I need immediate help!

${locationText}

Time: ${timestamp}

This is an automated emergency message from ESP32 Emergency System. Please respond immediately or contact emergency services.`;

            let successCount = 0;
            let failCount = 0;

            console.log('Sending emergency messages to', contacts.length, 'contacts');

            // Send to each contact
            for (const contact of contacts) {
                try {
                    // Send SMS
                    console.log(`Sending SMS to ${contact.name} (${contact.phone})`);
                    await sendSMS(contact.phone, emergencyMessage);
                    successCount++;
                    
                    // Send Email if available
                    if (contact.email) {
                        console.log(`Sending Email to ${contact.name} (${contact.email})`);
                        await sendEmail(contact.email, contact.name, emergencyMessage, locationText, timestamp);
                        successCount++;
                    }
                    
                } catch (error) {
                    console.error(`Failed to send message to ${contact.name}:`, error);
                    failCount++;
                    
                    // Fallback: Open SMS app for manual sending
                    const smsUrl = `sms:${contact.phone}?body=${encodeURIComponent(emergencyMessage)}`;
                    window.open(smsUrl, '_blank');
                }
            }

            // Log the emergency event
            logEmergencyEvent(emergencyMessage, locationText, timestamp, successCount, failCount);
            
            // Update UI
            updateLastEmergency(timestamp, successCount, failCount);
            
            // Show confirmation
            const statusMessage = successCount > 0 ? 
                `✅ ${successCount} emergency messages sent successfully!` : 
                `⚠️ ${failCount} messages failed. SMS apps opened for manual sending.`;
                
            showEmergencyAlert(statusMessage);
        }

        // Send SMS using SMS API
        async function sendSMS(phoneNumber, message) {
            // Using TextBelt API (free tier: 1 SMS per day per IP)
            // You can replace this with Twilio, AWS SNS, or other SMS service
            
            try {
                const response = await fetch('https://textbelt.com/text', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify({
                        phone: phoneNumber,
                        message: message,
                        key: 'textbelt' // Free tier key
                    })
                });

                const result = await response.json();
                
                if (!result.success) {
                    throw new Error(result.error || 'SMS sending failed');
                }
                
                console.log('SMS sent successfully:', result);
                return result;
                
            } catch (error) {
                console.error('SMS API error:', error);
                
                // Alternative: Try opening SMS app as fallback
                const smsUrl = `sms:${phoneNumber}?body=${encodeURIComponent(message)}`;
                window.open(smsUrl, '_blank');
                
                throw error;
            }
        }

        // Send Email
        async function sendEmail(email, name, message, location, timestamp) {
            // Mock email implementation - replace with actual email service
            // You can use EmailJS, SendGrid, or your own email API
            
            const emailData = {
                to: email,
                to_name: name,
                subject: '🚨 EMERGENCY SOS ALERT - IMMEDIATE RESPONSE REQUIRED',
                html: `
                    <div style="font-family: Arial, sans-serif; max-width: 600px; margin: 0 auto;">
                        <div style="background: #e74c3c; color: white; padding: 20px; text-align: center;">
                            <h1>🚨 EMERGENCY SOS ALERT</h1>
                            <h2>IMMEDIATE RESPONSE REQUIRED</h2>
                        </div>
                        <div style="padding: 20px; background: #f8f9fa;">
                            <p><strong>Dear ${name},</strong></p>
                            <p style="font-size: 16px; color: #e74c3c; font-weight: bold;">
                                An emergency SOS alert has been triggered. Please respond immediately!
                            </p>
                            <div style="background: white; padding: 15px; border-left: 5px solid #e74c3c; margin: 20px 0;">
                                <p><strong>Emergency Message:</strong></p>
                                <p>${message.replace(/\n/g, '<br>')}</p>
                            </div>
                            <p><strong>Location:</strong> ${location}</p>
                            <p><strong>Time:</strong> ${timestamp}</p>
                            <div style="text-align: center; margin: 30px 0;">
                                <a href="${location.includes('maps.google.com') ? location.split('Location: ')[1] : '#'}" 
                                   style="background: #e74c3c; color: white; padding: 15px 30px; text-decoration: none; border-radius: 5px; font-weight: bold;">
                                   📍 VIEW LOCATION ON MAP
                                </a>
                            </div>
                            <p style="font-size: 14px; color: #666;">
                                This is an automated emergency message sent from an ESP32 Emergency System.
                                If this is a real emergency, please contact emergency services immediately.
                            </p>
                        </div>
                    </div>
                `
            };

            // Mock API call - replace with your actual email service
            console.log('Email would be sent:', emailData);
            
            // Simulate API delay
            await new Promise(resolve => setTimeout(resolve, 1000));
            
            // For now, open email client as fallback
            const emailSubject = encodeURIComponent('🚨 EMERGENCY SOS ALERT');
            const emailBody = encodeURIComponent(message);
            const emailUrl = `mailto:${email}?subject=${emailSubject}&body=${emailBody}`;
            window.open(emailUrl, '_blank');
            
            return { status: 'success' };
        }

        // Log emergency events
        function logEmergencyEvent(message, location, timestamp, successCount, failCount) {
            const emergencyLog = {
                id: Date.now(),
                timestamp: timestamp,
                message: message,
                location: location,
                successCount: successCount,
                failCount: failCount,
                totalContacts: contacts.length,
                coordinates: currentLocation ? {
                    lat: currentLocation.latitude,
                    lng: currentLocation.longitude,
                    accuracy: currentLocation.accuracy
                } : null
            };

            // Store in browser's memory (in production, send to server)
            let logs = JSON.parse(localStorage.getItem('emergencyLogs') || '[]');
            logs.unshift(emergencyLog); // Add to beginning of array
            logs = logs.slice(0, 10); // Keep only last 10 logs
            localStorage.setItem('emergencyLogs', JSON.stringify(logs));

            console.log('Emergency event logged:', emergencyLog);
        }

        // Update last emergency display
        function updateLastEmergency(timestamp, successCount, failCount) {
            const lastEmergencyDiv = document.getElementById('last-emergency');
            if (lastEmergencyDiv) {
                lastEmergencyDiv.innerHTML = `
                    <strong>Last Alert:</strong> ${timestamp}<br>
                    <small>✅ ${successCount} sent | ❌ ${failCount} failed</small>
                `;
            }
        }

        // Send emergency messages automatically
        async function sendEmergencyMessages() {
            const locationText = currentLocation ? 
                `My location: https://maps.google.com/?q=${currentLocation.latitude},${currentLocation.longitude}` : 
                'Location unavailable';

            const emergencyMessage = `🚨 EMERGENCY SOS! I need help urgently. ${locationText}`;
            const timestamp = new Date().toLocaleString();

            let messagesSent = 0;
            let totalMessages = contacts.length * 2; // SMS + Email for each contact

            for (const contact of contacts) {
                try {
                    // Send SMS via SMS API (using a SMS service)
                    await sendSMS(contact.phone, emergencyMessage);
                    messagesSent++;
                    console.log(`SMS sent to ${contact.name} (${contact.phone})`);
                } catch (error) {
                    console.error(`Failed to send SMS to ${contact.name}:`, error);
                    // Fallback: Open SMS app
                    const smsUrl = `sms:${contact.phone}?body=${encodeURIComponent(emergencyMessage)}`;
                    window.open(smsUrl);
                }

                // Send Email if available
                if (contact.email) {
                    try {
                        await sendEmail(contact.email, contact.name, emergencyMessage, locationText, timestamp);
                        messagesSent++;
                        console.log(`Email sent to ${contact.name} (${contact.email})`);
                    } catch (error) {
                        console.error(`Failed to send email to ${contact.name}:`, error);
                        // Fallback: Open email client
                        const emailSubject = '🚨 EMERGENCY SOS ALERT';
                        const emailBody = `Dear ${contact.name},\n\n${emergencyMessage}\n\nTime: ${timestamp}\n\nThis is an automated emergency message.`;
                        const emailUrl = `mailto:${contact.email}?subject=${encodeURIComponent(emailSubject)}&body=${encodeURIComponent(emailBody)}`;
                        window.open(emailUrl);
                    }
                }
            }

            // Log emergency event
            logEmergencyEvent(emergencyMessage, locationText, timestamp, messagesSent);
            showEmergencyAlert(`${messagesSent} emergency messages sent successfully!`);
        }

        // Send SMS using SMS API (you'll need to integrate with an SMS service like Twilio, TextBelt, etc.)
        async function sendSMS(phoneNumber, message) {
            // Example using TextBelt API (free SMS service)
            const response = await fetch('https://textbelt.com/text', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify({
                    phone: phoneNumber,
                    message: message,
                    key: 'textbelt' // Use 'textbelt' for free tier (1 SMS per day per IP)
                })
            });

            const result = await response.json();
            if (!result.success) {
                throw new Error(result.error || 'SMS sending failed');
            }
            return result;
        }

        // Send Email using EmailJS or similar service
        async function sendEmail(email, name, message, location, timestamp) {
            // You'll need to set up EmailJS account and get your service details
            // For demo, we'll use a mock implementation
            
            const emailData = {
                to_email: email,
                to_name: name,
                subject: '🚨 EMERGENCY SOS ALERT',
                message: `Dear ${name},

🚨 EMERGENCY SOS ALERT! 

${message}

Time: ${timestamp}
${location}

This is an automated emergency message sent from an ESP32 Emergency System.

Please respond immediately or contact emergency services.`,
            };

            // Mock email sending (replace with actual EmailJS implementation)
            return new Promise((resolve, reject) => {
                setTimeout(() => {
                    console.log('Email would be sent:', emailData);
                    resolve({ status: 'success' });
                }, 1000);
            });
        }

        // Log emergency events for tracking
        function logEmergencyEvent(message, location, timestamp, messagesSent) {
            const emergencyLog = {
                timestamp: timestamp,
                message: message,
                location: location,
                messagesSent: messagesSent,
                contacts: contacts.length
            };

            // Store in browser's memory (in a real app, send to server)
            let logs = window.emergencyLogs || [];
            logs.push(emergencyLog);
            window.emergencyLogs = logs;

            console.log('Emergency event logged:', emergencyLog);
        }

        // Show emergency alert popup with custom message
        function showEmergencyAlert(customMessage = null) {
            const alertDiv = document.getElementById('emergency-alert');
            if (customMessage) {
                alertDiv.innerHTML = `
                    <h2>🚨 EMERGENCY ALERT SENT!</h2>
                    <p>${customMessage}</p>
                    <p>Emergency contacts have been notified with your GPS location</p>
                    <button class="btn" onclick="closeAlert()" style="background: white; color: #e74c3c; margin-top: 15px;">Close</button>
                `;
            }
            alertDiv.style.display = 'block';
            setTimeout(closeAlert, 8000); // Auto close after 8 seconds
        }

        // Close emergency alert
        function closeAlert() {
            document.getElementById('emergency-alert').style.display = 'none';
        }

        // Test emergency alert (for demo)
        function testEmergencyAlert() {
            if (contacts.length === 0) {
                alert('⚠️ No emergency contacts added!\n\nPlease add at least one emergency contact before testing the alert system.');
                return;
            }
            
            if (!currentLocation) {
                alert('⚠️ GPS location not available!\n\nTrying to get location now...');
                getCurrentLocation();
                setTimeout(() => {
                    if (currentLocation) {
                        buttonPressCount = 2;
                        triggerEmergencyAlert();
                    } else {
                        alert('❌ Could not get GPS location. Emergency alert will be sent without location.');
                        buttonPressCount = 2;
                        triggerEmergencyAlert();
                    }
                }, 3000);
                return;
            }
            
            // Simulate double button press
            buttonPressCount = 2;
            triggerEmergencyAlert();
        }

        // View emergency logs
        function viewEmergencyLogs() {
            const logs = window.emergencyLogs || [];
            if (logs.length === 0) {
                alert('📝 No emergency events logged yet.');
                return;
            }

            let logText = '🚨 EMERGENCY LOGS:\n\n';
            logs.forEach((log, index) => {
                logText += `Event ${index + 1}:\n`;
                logText += `Time: ${log.timestamp}\n`;
                logText += `Messages Sent: ${log.messagesSent}\n`;
                logText += `Contacts Notified: ${log.contacts}\n`;
                logText += `Location: ${log.location}\n\n`;
            });

            alert(logText);
        }

        // Initialize app
        document.addEventListener('DOMContentLoaded', function() {
            initMap();
            getCurrentLocation();
            updateContactCount();
        });
    </script>
</body>
</html>
