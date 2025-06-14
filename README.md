<!DOCTYPE html>
<html lang="en">
<head>
    <!-- FOUC Prevention - Add this FIRST in head -->
    <style>
        html { 
            background-color: #000;
        }
        body {
            visibility: hidden;
            opacity: 0;
            margin: 0;
            padding: 0;
            height: 100%;
            overflow: hidden;
            background: #000;
            font-family: 'Times New Roman', serif;
        }
        body.loaded {
            visibility: visible;
            opacity: 1;
            transition: opacity 0.5s ease;
        }
    </style>
    
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <title>dread.services</title>
    <style>
        .youtube-container {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 145%;
            opacity: 0;
            transition: opacity 1s ease-in-out;
            z-index: 1;
            pointer-events: none;
        }
        .youtube-container.playing {
            opacity: 1;
        }
        .youtube-iframe {
            width: 100%;
            height: 100%;
            border: none;
            transform: translateY(-15%);
        }
        .overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: #000;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 2;
        }
        .overlay.hidden {
            opacity: 0;
            pointer-events: none;
            transition: opacity 1s ease-in-out;
        }
        .title-text {
            color: #ffffff;
            font-size: min(5vw, 36px);
            text-transform: uppercase;
            letter-spacing: 4px;
            margin-bottom: 20px;
            text-shadow: 0 0 8px rgba(255, 255, 255, 0.5);
        }
        .subtitle-text {
            color: #FFFF00;
            font-size: min(3vw, 24px);
            letter-spacing: 2px;
            margin-bottom: 40px;
            text-shadow: 0 0 8px rgba(255, 255, 0, 0.7);
        }
        .enter-button {
            padding: 20px 40px;
            font-size: min(5vw, 28px);
            font-weight: bold;
            color: #ffffff;
            background: linear-gradient(145deg, #1a1a1a, #333333);
            border: 2px solid #444;
            border-top: 2px solid #666;
            border-bottom: 2px solid #222;
            border-radius: 0;
            text-transform: uppercase;
            letter-spacing: 4px;
            cursor: pointer;
            position: relative;
            overflow: hidden;
            box-shadow: 0 0 20px rgba(255, 0, 0, 0.7);
            transition: all 0.3s ease;
            text-shadow: 0 0 8px rgba(255, 255, 255, 0.5);
            min-width: 250px;
            text-align: center;
        }
        .enter-button:hover {
            box-shadow: 0 0 30px rgba(255, 0, 0, 0.9);
            transform: scale(1.05);
            text-shadow: 0 0 12px rgba(255, 255, 255, 0.8);
        }
        .click-animation {
            position: absolute;
            width: 100px;
            height: 100px;
            background: radial-gradient(circle, 
                rgba(255, 0, 0, 0.8) 0%, 
                rgba(255, 0, 0, 0) 70%);
            border-radius: 50%;
            transform: scale(0);
            opacity: 0;
            pointer-events: none;
            mix-blend-mode: screen;
        }
        .animate {
            animation: ripple 1s ease-out;
        }
        @keyframes ripple {
            to {
                transform: scale(3);
                opacity: 0;
            }
        }
        .login-button {
            position: fixed;
            bottom: 50px;
            right: 20px;
            padding: 10px 20px;
            font-size: 16px;
            font-weight: bold;
            color: #ffffff;
            background: linear-gradient(145deg, #1a1a1a, #333333);
            border: 2px solid #444;
            border-top: 2px solid #666;
            border-bottom: 2px solid #222;
            border-radius: 0;
            text-transform: uppercase;
            letter-spacing: 2px;
            cursor: pointer;
            z-index: 1002; /* Updated from 100 to 1002 */
            box-shadow: 0 0 10px rgba(255, 0, 0, 0.5);
            transition: all 0.3s ease;
            text-shadow: 0 0 5px rgba(255, 255, 255, 0.5);
            opacity: 0;
            transition: opacity 0.5s ease;
        }
        .login-button.visible {
            opacity: 1;
        }
        .login-button:hover {
            box-shadow: 0 0 15px rgba(255, 0, 0, 0.8);
            transform: scale(1.05);
        }
        .logout-button {
            position: fixed;
            bottom: 50px;
            right: 20px;
            padding: 10px 20px;
            font-size: 16px;
            font-weight: bold;
            color: #ffffff;
            background: linear-gradient(145deg, #1a1a1a, #333333);
            border: 2px solid #444;
            border-top: 2px solid #666;
            border-bottom: 2px solid #222;
            border-radius: 0;
            text-transform: uppercase;
            letter-spacing: 2px;
            cursor: pointer;
            z-index: 1002; /* Updated from 100 to 1002 */
            box-shadow: 0 0 10px rgba(255, 0, 0, 0.5);
            transition: all 0.3s ease;
            text-shadow: 0 0 5px rgba(255, 255, 255, 0.5);
            opacity: 0;
            transition: opacity 0.5s ease;
        }
        .logout-button.visible {
            opacity: 1;
        }
        .logout-button:hover {
            box-shadow: 0 0 15px rgba(255, 0, 0, 0.8);
            transform: scale(1.05);
        }
        .volume-container {
            position: fixed;
            bottom: 50px;
            left: 20px;
            display: flex;
            align-items: center;
            z-index: 100;
            opacity: 0;
            transition: opacity 0.5s ease;
        }
        .volume-container.visible {
            opacity: 1;
        }
        .volume-slider {
            -webkit-appearance: none;
            width: 120px;
            height: 8px;
            background: linear-gradient(145deg, #1a1a1a, #333333);
            border: 2px solid #444;
            border-top: 2px solid #666;
            border-bottom: 2px solid #222;
            outline: none;
            margin-left: 10px;
            box-shadow: 0 0 10px rgba(255, 0, 0, 0.5);
        }
        .volume-slider::-webkit-slider-thumb {
            -webkit-appearance: none;
            appearance: none;
            width: 18px;
            height: 18px;
            background: #ffffff;
            cursor: pointer;
            border: 2px solid #444;
            box-shadow: 0 0 5px rgba(255, 0, 0, 0.7);
        }
        .volume-slider::-moz-range-thumb {
            width: 18px;
            height: 18px;
            background: #ffffff;
            cursor: pointer;
            border: 2px solid #444;
            box-shadow: 0 0 5px rgba(255, 0, 0, 0.7);
        }
        .volume-icon {
            color: #ffffff;
            font-size: 24px;
            text-shadow: 0 0 5px rgba(255, 0, 0, 0.7);
        }
        .login-page {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: #000;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 1000;
            opacity: 0;
            pointer-events: none;
            transition: opacity 0.5s ease;
        }
        .login-page.visible {
            opacity: 1;
            pointer-events: all;
        }
        .login-title {
            color: #ffffff;
            font-size: min(5vw, 36px);
            text-transform: uppercase;
            letter-spacing: 4px;
            margin-bottom: 40px;
            text-shadow: 0 0 8px rgba(255, 255, 255, 0.5);
        }
        .input-container {
            margin-bottom: 20px;
            text-align: center;
        }
        .input-label {
            display: block;
            color: #FFFF00;
            font-size: min(3vw, 18px);
            margin-bottom: 8px;
            text-shadow: 0 0 8px rgba(255, 255, 0, 0.7);
        }
        .input-field {
            padding: 15px 25px;
            font-size: min(4vw, 20px);
            color: #ffffff;
            background: linear-gradient(145deg, #1a1a1a, #333333);
            border: 2px solid #444;
            border-top: 2px solid #666;
            border-bottom: 2px solid #222;
            border-radius: 0;
            text-transform: uppercase;
            letter-spacing: 2px;
            min-width: 250px;
            text-align: center;
            box-shadow: 0 0 10px rgba(255, 0, 0, 0.5);
            transition: all 0.3s ease;
        }
        .input-field:focus {
            outline: none;
            box-shadow: 0 0 15px rgba(255, 0, 0, 0.8);
        }
        .login-submit {
            margin-top: 30px;
            padding: 15px 30px;
            font-size: min(4vw, 20px);
            font-weight: bold;
            color: #ffffff;
            background: linear-gradient(145deg, #1a1a1a, #333333);
            border: 2px solid #444;
            border-top: 2px solid #666;
            border-bottom: 2px solid #222;
            border-radius: 0;
            text-transform: uppercase;
            letter-spacing: 2px;
            cursor: pointer;
            box-shadow: 0 0 10px rgba(255, 0, 0, 0.5);
            transition: all 0.3s ease;
        }
        .login-submit:hover {
            box-shadow: 0 0 20px rgba(255, 0, 0, 0.9);
            transform: scale(1.05);
        }
        .login-message {
            margin-top: 20px;
            color: #FF0000;
            font-size: min(3vw, 16px);
            text-shadow: 0 0 8px rgba(255, 0, 0, 0.7);
            height: 20px;
        }
        .close-login {
            position: absolute;
            top: 20px;
            right: 20px;
            color: #ffffff;
            font-size: 24px;
            cursor: pointer;
            text-shadow: 0 0 8px rgba(255, 0, 0, 0.7);
        }
        .logged-in-page {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: #000;
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 1000;
            color: #ffffff;
            font-size: min(10vw, 72px);
            text-transform: uppercase;
            letter-spacing: 8px;
            text-shadow: 0 0 20px rgba(255, 0, 0, 0.9);
            opacity: 0;
            pointer-events: none;
            transition: opacity 1s ease;
        }
        .logged-in-page.visible {
            opacity: 1;
            pointer-events: all;
        }
        .test-page {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: #000;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 1001;
            opacity: 0;
            pointer-events: none;
            transition: opacity 1s ease;
        }
        .test-page.visible {
            opacity: 1;
            pointer-events: all;
        }
        .test-button {
            padding: 20px 40px;
            font-size: min(5vw, 28px);
            font-weight: bold;
            color: #ffffff;
            background: linear-gradient(145deg, #1a1a1a, #333333);
            border: 2px solid #444;
            border-top: 2px solid #666;
            border-bottom: 2px solid #222;
            border-radius: 0;
            text-transform: uppercase;
            letter-spacing: 4px;
            cursor: pointer;
            position: relative;
            overflow: hidden;
            box-shadow: 0 0 20px rgba(0, 255, 0, 0.7);
            transition: all 0.3s ease;
            text-shadow: 0 0 8px rgba(255, 255, 255, 0.5);
            min-width: 250px;
            text-align: center;
            margin-top: 40px;
        }
        .test-button:hover {
            box-shadow: 0 0 30px rgba(0, 255, 0, 0.9);
            transform: scale(1.05);
            text-shadow: 0 0 12px rgba(255, 255, 255, 0.8);
        }
    </style>
</head>
<body>
    <!-- Preloader (hidden by default, shown only if needed) -->
    <div id="preloader" style="position:fixed;top:0;left:0;width:100%;height:100%;background:#000;z-index:9999;display:none;"></div>

    <div id="youtubeContainer" class="youtube-container"></div>

    <audio id="backgroundMusic"></audio>

    <div class="overlay" id="overlay">
        <div class="title-text">dread.services</div>
        <div class="subtitle-text">Work In Progress</div>
        <button class="enter-button" id="enterButton">CLICK TO ENTER</button>
    </div>

    <div class="volume-container" id="volumeContainer">
        <div class="volume-icon">🔊</div>
        <input type="range" min="0" max="1" step="0.01" value="0.5" class="volume-slider" id="volumeSlider">
    </div>

    <button class="login-button" id="loginButton">LOGIN</button>
    <button class="logout-button" id="logoutButton">LOGOUT</button>
    
    <div class="login-page" id="loginPage">
        <span class="close-login" id="closeLogin">&times;</span>
        <div class="login-title">LOGIN</div>
        
        <div class="input-container">
            <label class="input-label" for="username">ENTER USERNAME</label>
            <input type="text" id="username" class="input-field">
        </div>
        
        <div class="input-container">
            <label class="input-label" for="password">ENTER PASSWORD</label>
            <input type="password" id="password" class="input-field">
        </div>
        
        <button class="login-submit" id="loginSubmit">LOGIN</button>
        <div class="login-message" id="loginMessage"></div>
    </div>

    <div class="logged-in-page" id="loggedInPage">
        LOGGED IN
    </div>

    <div class="test-page" id="testPage">
        <div class="title-text">TEST PAGE</div>
        <button class="test-button" id="testButton">TEST</button>
    </div>

    <script>
        // Secure credential storage
        const SECURE = {
            user: '1',
            pass: '1',
            token: 'dread_' + btoa('r00do:admin').split('').reverse().join('') + '_' 
                 + Math.random().toString(36).substring(2, 15)
        };

        // List of songs in the music directory
        const songs = [
            'Scars.mp3',
            'Rip.mp3',
            'Zero.mp3',
            'Plata.mp3',
            'Peach.mp3',
            'Leave.mp3',
            'Peace.mp3',
            'Foreal.mp3',
            'Prove.mp3',
            'Newer.mp3',
            'Hair.mp3'
        ];

        // YouTube iframe reference
        let youtubeIframe = null;

        // Function to shuffle array
        function shuffleArray(array) {
            for (let i = array.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [array[i], array[j]] = [array[j], array[i]];
            }
            return array;
        }

        // Function to play shuffled songs
        function playShuffledSongs() {
            const music = document.getElementById('backgroundMusic');
            let shuffledSongs = shuffleArray([...songs]); // Create a copy and shuffle it
            
            let currentSongIndex = 0;
            
            function playNextSong() {
                if (currentSongIndex >= shuffledSongs.length) {
                    // If we've played all songs, reshuffle and start over
                    shuffledSongs = shuffleArray([...songs]);
                    currentSongIndex = 0;
                }
                
                const song = shuffledSongs[currentSongIndex];
                music.src = `music/${song}`;
                music.play();
                
                currentSongIndex++;
            }
            
            // Set up event listener for when a song ends
            music.addEventListener('ended', playNextSong);
            
            // Start playing the first song
            playNextSong();
        }

        // Function to fade out music
        function fadeOutMusic(duration = 2000) {
            const music = document.getElementById('backgroundMusic');
            const startVolume = music.volume;
            const fadeInterval = 50;
            const steps = duration / fadeInterval;
            const stepSize = startVolume / steps;
            
            const fadeAudio = setInterval(() => {
                if (music.volume > stepSize) {
                    music.volume -= stepSize;
                } else {
                    music.volume = 0;
                    clearInterval(fadeAudio);
                    music.pause();
                }
            }, fadeInterval);
        }

        // Function to pause all media
        function pauseAllMedia() {
            fadeOutMusic();
            
            if (youtubeIframe) {
                youtubeIframe.contentWindow.postMessage('{"event":"command","func":"pauseVideo","args":""}', '*');
            }
        }

        // Function to resume all media
        function resumeAllMedia() {
            const music = document.getElementById('backgroundMusic');
            music.volume = document.getElementById('volumeSlider').value;
            music.play();
            
            if (youtubeIframe) {
                youtubeIframe.contentWindow.postMessage('{"event":"command","func":"playVideo","args":""}', '*');
            }
        }

        // Function to reset to initial state
        function resetToInitialState() {
            // Hide all pages except overlay
            document.getElementById('loginPage').classList.remove('visible');
            document.getElementById('loggedInPage').classList.remove('visible');
            document.getElementById('testPage').classList.remove('visible');
            
            // Show overlay and enter button
            document.getElementById('overlay').classList.remove('hidden');
            document.getElementById('enterButton').style.display = 'block';
            
            // Hide login/logout buttons and volume control
            document.getElementById('loginButton').style.display = 'none';
            document.getElementById('loginButton').classList.remove('visible');
            document.getElementById('logoutButton').style.display = 'none';
            document.getElementById('logoutButton').classList.remove('visible');
            document.getElementById('volumeContainer').style.display = 'none';
            document.getElementById('volumeContainer').classList.remove('visible');
            
            // Clear auth
            clearAuth();
            
            // Pause all media
            pauseAllMedia();
            
            // Remove YouTube iframe
            document.getElementById('youtubeContainer').innerHTML = '';
            document.getElementById('youtubeContainer').classList.remove('playing');
            youtubeIframe = null;
        }

        // Authentication functions
        function setAuth() {
            const now = new Date();
            now.setTime(now.getTime() + (60 * 60 * 1000)); // 1 hour expiration
            document.cookie = `dread_auth=${SECURE.token}; expires=${now.toUTCString()}; path=/; SameSite=Strict`;
            localStorage.setItem('dread_auth', SECURE.token);
        }

        function checkAuth() {
            const cookieAuth = document.cookie.split(';').some(item => 
                item.trim().startsWith('dread_auth=' + SECURE.token));
            const storageAuth = localStorage.getItem('dread_auth') === SECURE.token;
            return cookieAuth || storageAuth;
        }

        function clearAuth() {
            document.cookie = 'dread_auth=; expires=Thu, 01 Jan 1970 00:00:00 GMT; path=/';
            localStorage.removeItem('dread_auth');
        }

        // DOM Ready
        document.addEventListener("DOMContentLoaded", () => {
            // Add 'loaded' class to fade in the page
            document.body.classList.add('loaded');
            
            // Hide login/logout buttons initially
            document.getElementById('loginButton').style.display = 'none';
            document.getElementById('logoutButton').style.display = 'none';
            document.getElementById('volumeContainer').style.display = 'none';
            
            // If already logged in, show logged in page
            if (checkAuth()) {
                document.getElementById('loggedInPage').classList.add('visible');
                document.getElementById('overlay').style.display = 'none';
                // Start video and music if already logged in
                loadYouTubeVideo();
                playShuffledSongs();
                // Show logout button and volume control immediately if already logged in
                document.getElementById('logoutButton').style.display = 'block';
                document.getElementById('logoutButton').classList.add('visible');
                volumeContainer.style.display = 'flex';
                volumeContainer.classList.add('visible');
                
                // After 3 seconds, show the test page and fade out music
                setTimeout(() => {
                    document.getElementById('loggedInPage').classList.remove('visible');
                    document.getElementById('testPage').classList.add('visible');
                    fadeOutMusic();
                }, 3000);
            }
        });

        // Element references
        const youtubeContainer = document.getElementById('youtubeContainer');
        const music = document.getElementById('backgroundMusic');
        const overlay = document.getElementById('overlay');
        const enterButton = document.getElementById('enterButton');
        const loginButton = document.getElementById('loginButton');
        const logoutButton = document.getElementById('logoutButton');
        const loginPage = document.getElementById('loginPage');
        const closeLogin = document.getElementById('closeLogin');
        const loginSubmit = document.getElementById('loginSubmit');
        const usernameInput = document.getElementById('username');
        const passwordInput = document.getElementById('password');
        const loginMessage = document.getElementById('loginMessage');
        const loggedInPage = document.getElementById('loggedInPage');
        const volumeContainer = document.getElementById('volumeContainer');
        const volumeSlider = document.getElementById('volumeSlider');
        const testPage = document.getElementById('testPage');
        const testButton = document.getElementById('testButton');

        // Volume control
        volumeSlider.addEventListener('input', (e) => {
            music.volume = e.target.value;
        });

        // Set initial volume
        music.volume = volumeSlider.value;

        // YouTube video loader function
        function loadYouTubeVideo() {
            youtubeContainer.innerHTML = `
                <iframe class="youtube-iframe" 
                    src="https://www.youtube.com/embed/sKu8Zg7Hplc?autoplay=1&controls=0&disablekb=1&fs=0&loop=1&modestbranding=1&playsinline=1&rel=0&showinfo=0&mute=1&iv_load_policy=3&cc_load_policy=0&playlist=sKu8Zg7Hplc&enablejsapi=1" 
                    frameborder="0" 
                    allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
                    allowfullscreen>
                </iframe>`;
            
            // Store reference to iframe
            youtubeIframe = youtubeContainer.querySelector('.youtube-iframe');
            
            // Add slight delay before showing to ensure it's loaded
            setTimeout(() => {
                youtubeContainer.classList.add('playing');
            }, 300);
        }

        // Enter button click
        enterButton.addEventListener('click', (e) => {
            // Click animation
            const ripple = document.createElement('div');
            ripple.classList.add('click-animation');
            const rect = enterButton.getBoundingClientRect();
            ripple.style.left = (e.clientX - rect.left - 50) + 'px';
            ripple.style.top = (e.clientY - rect.top - 50) + 'px';
            enterButton.appendChild(ripple);
            
            setTimeout(() => {
                ripple.classList.add('animate');
            }, 10);
            
            setTimeout(() => {
                ripple.remove();
            }, 1000);
            
            // Load YouTube video and play music immediately
            loadYouTubeVideo();
            playShuffledSongs();
            
            // Hide the enter button and show the login button and volume control
            enterButton.style.display = 'none';
            loginButton.style.display = 'block';
            volumeContainer.style.display = 'flex';
            setTimeout(() => {
                loginButton.classList.add('visible');
                volumeContainer.classList.add('visible');
            }, 500);
            
            // Optionally fade out the overlay (but keep it in DOM for potential login)
            overlay.classList.add('hidden');
        });

        // Login button click
        loginButton.addEventListener('click', () => {
            pauseAllMedia();
            loginPage.classList.add('visible');
        });
        
        // Close login page
        closeLogin.addEventListener('click', () => {
            loginPage.classList.remove('visible');
            loginMessage.textContent = '';
            resumeAllMedia();
        });
        
        // Login submit
        loginSubmit.addEventListener('click', () => {
            const username = usernameInput.value.trim();
            const password = passwordInput.value.trim();
            
            if (!username || !password) {
                loginMessage.textContent = 'Please enter both username and password';
                return;
            }
            
            if (username === SECURE.user && password === SECURE.pass) {
                loginMessage.textContent = 'Login successful!';
                loginMessage.style.color = '#00FF00';
                setAuth();
                setTimeout(() => {
                    loginPage.classList.remove('visible');
                    loginButton.classList.remove('visible');
                    
                    // Show logged in page after a delay
                    setTimeout(() => {
                        loggedInPage.classList.add('visible');
                        
                        // After 3 seconds, show the test page and fade out music
                        setTimeout(() => {
                            loggedInPage.classList.remove('visible');
                            testPage.classList.add('visible');
                            fadeOutMusic();
                            
                            // Show logout button
                            logoutButton.style.display = 'block';
                            setTimeout(() => {
                                logoutButton.classList.add('visible');
                            }, 100);
                        }, 3000);
                    }, 1000);
                    
                    usernameInput.value = '';
                    passwordInput.value = '';
                }, 1000);
            } else {
                loginMessage.textContent = 'Invalid credentials';
                loginMessage.style.color = '#FF0000';
            }
        });
        
        // Logout button click
        logoutButton.addEventListener('click', () => {
            resetToInitialState();
        });
        
        // Enter key for password field
        passwordInput.addEventListener('keypress', (e) => {
            if (e.key === 'Enter') {
                loginSubmit.click();
            }
        });

        // Test button click
        testButton.addEventListener('click', () => {
            alert('Test button clicked!');
        });

        // Responsive font sizing
        window.addEventListener('resize', () => {
            const width = window.innerWidth;
            enterButton.style.fontSize = `${Math.min(width * 0.05, 28)}px`;
        });

        window.dispatchEvent(new Event('resize'));
        
        // Anti-tampering measures
        document.addEventListener('contextmenu', e => e.preventDefault());
        
        document.addEventListener('keydown', e => {
            // Block F12, Ctrl+Shift+I, Ctrl+Shift+J, Ctrl+Shift+C
            if (e.key === 'F12' || 
                (e.ctrlKey && e.shiftKey && ['I','J','C'].includes(e.key))) {
                e.preventDefault();
                alert('Access restricted');
                return false;
            }
            
            // Block Ctrl+U (View Source)
            if (e.ctrlKey && e.key === 'u') {
                e.preventDefault();
                return false;
            }
        });

        // Extra protection against console access
        setInterval(() => {
            if (window.console && console.log) {
                console.log = function() {};
                console.warn = function() {};
                console.error = function() {};
            }
        }, 1000);
    </script>
</body>
</html>
