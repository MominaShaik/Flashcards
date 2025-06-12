# Flashcards
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flashcard App</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        /* Custom styles for the Inter font and general body */
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f0f2f5; /* Light grey background for the whole page */
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh; /* Ensure it takes full viewport height */
            margin: 0;
            padding: 1rem; /* Add some padding around the content */
            box-sizing: border-box; /* Include padding in element's total width and height */
            transition: background-color 0.3s ease; /* Smooth transition for theme change */
        }

        /* Dark mode styles */
        body.dark-mode {
            background-color: #1a202c; /* Dark background for the whole page */
            color: #e2e8f0; /* Light text color */
        }

        /* Adjustments for the main container to center content on larger screens */
        .main-container {
            max-width: 600px; /* Max width for the app on larger screens */
            width: 100%; /* Take full width on smaller screens */
            display: flex;
            flex-direction: column;
            gap: 1.5rem; /* Space between sections */
            transition: background-color 0.3s ease; /* Smooth transition for theme change */
        }

        body.dark-mode .main-container {
            background-color: #2d3748; /* Darker background for main container */
        }

        /* Flashcard specific styling */
        .flashcard {
            min-height: 300px; /* Minimum height for the flashcard */
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            text-align: center;
            position: relative; /* Needed for absolute positioning of reveal icon */
            cursor: pointer; /* Indicate it's interactive */
            transition: transform 0.6s ease-in-out, background-color 0.3s ease, border-color 0.3s ease; /* Smooth flip and theme transitions */
            transform-style: preserve-3d; /* For 3D flip effect */
        }

        .flashcard.flipped {
            transform: rotateY(180deg); /* Flip effect */
        }

        .flashcard-content {
            position: absolute;
            width: 100%;
            height: 100%;
            backface-visibility: hidden; /* Hide the back of the content div */
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            padding: 1.5rem;
            box-sizing: border-box;
            border-radius: 0.75rem; /* Match parent border-radius */
            transition: background-color 0.3s ease, color 0.3s ease; /* Smooth transition for theme change */
        }

        .flashcard-front {
            z-index: 2; /* Ensure front is on top when not flipped */
            background-color: #1a202c; /* Dark blue background for question */
            color: white;
        }

        body.dark-mode .flashcard-front {
            background-color: #4a5568; /* Darker blue for flashcard in dark mode */
            color: #e2e8f0;
        }

        .flashcard-back {
            transform: rotateY(180deg); /* Initially rotated for back */
            z-index: 1; /* Ensure back is behind front when not flipped */
            background-color: #1a202c; /* Dark blue background for answer */
            color: white;
        }

        body.dark-mode .flashcard-back {
            background-color: #4a5568; /* Darker blue for flashcard in dark mode */
            color: #e2e8f0;
        }

        /* Icon styling */
        .icon-button {
            background: none;
            border: none;
            padding: 0.5rem;
            cursor: pointer;
            color: white;
            display: flex;
            align-items: center;
            justify-content: center;
            border-radius: 9999px; /* Full rounded for circular buttons */
            transition: background-color 0.2s ease-in-out;
        }
        .icon-button:hover {
            background-color: rgba(255, 255, 255, 0.2); /* Slight hover effect */
        }
        .icon-button svg {
            width: 24px;
            height: 24px;
            fill: currentColor;
        }

        /* Specific styling for the small flashcard icon in the header */
        .small-flashcard-icon-container {
            display: flex;
            align-items: center;
            background-color: rgba(255, 255, 255, 0.1);
            padding: 0.5rem 0.75rem; /* Adjust padding for text */
            border-radius: 9999px;
            gap: 0.5rem; /* Space between icon and text */
        }

        /* Styling for navigation arrows */
        .nav-arrow {
            background-color: #3f51b5; /* A shade of blue for arrows */
            color: white;
            padding: 0.75rem;
            border-radius: 9999px;
            cursor: pointer;
            transition: background-color 0.2s ease-in-out;
        }
        .nav-arrow:hover {
            background-color: #303f9f; /* Darker blue on hover */
        }
        .nav-arrow svg {
            width: 24px;
            height: 24px;
        }

        body.dark-mode .nav-arrow {
            background-color: #6366f1; /* Lighter blue for arrows in dark mode */
        }
        body.dark-mode .nav-arrow:hover {
            background-color: #4f46e5; /* Darker blue on hover in dark mode */
        }

        body.dark-mode #card-progress {
            color: #cbd5e0; /* Light grey for progress text in dark mode */
        }

        /* Settings Modal Styles */
        .settings-modal-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.7); /* Semi-transparent black overlay */
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 1000; /* Ensure it's on top of everything */
            opacity: 0;
            visibility: hidden;
            transition: opacity 0.3s ease, visibility 0.3s ease;
        }

        .settings-modal-overlay.show {
            opacity: 1;
            visibility: visible;
        }

        .settings-modal-content {
            background-color: white;
            padding: 2rem;
            border-radius: 1rem;
            box-shadow: 0 4px 20px rgba(0, 0, 0, 0.2);
            width: 90%;
            max-width: 400px;
            position: relative;
            transform: translateY(20px); /* Initial slight offset for animation */
            transition: transform 0.3s ease;
        }

        .settings-modal-overlay.show .settings-modal-content {
            transform: translateY(0); /* Slide into place */
        }

        body.dark-mode .settings-modal-content {
            background-color: #2d3748; /* Dark mode background for modal */
            color: #e2e8f0;
        }

        .settings-modal-content .close-button {
            position: absolute;
            top: 1rem;
            right: 1rem;
            background: none;
            border: none;
            font-size: 1.5rem;
            cursor: pointer;
            color: #666;
            transition: color 0.2s ease;
        }

        body.dark-mode .settings-modal-content .close-button {
            color: #cbd5e0;
        }

        .settings-modal-content .close-button:hover {
            color: #333;
        }

        body.dark-mode .settings-modal-content .close-button:hover {
            color: #fff;
        }

        .settings-modal-content h2 {
            font-size: 1.75rem;
            font-weight: 700;
            margin-bottom: 1.5rem;
            text-align: center;
            color: #333;
        }

        body.dark-mode .settings-modal-content h2 {
            color: #e2e8f0;
        }

        .settings-option {
            display: flex;
            flex-direction: column; /* Stack buttons vertically */
            gap: 0.75rem; /* Space between buttons */
            padding: 1rem 0;
            border-bottom: 1px solid #eee;
        }

        body.dark-mode .settings-option {
            border-bottom-color: #4a5568;
        }

        .settings-option:last-child {
            border-bottom: none;
        }

        .settings-option label {
            font-size: 1.1rem;
            font-weight: 600;
            margin-bottom: 0.5rem; /* Space between label and buttons */
        }

        .settings-option button {
            background-color: #3f51b5;
            color: white;
            padding: 0.75rem 1.25rem;
            border-radius: 0.5rem;
            font-weight: 600;
            cursor: pointer;
            transition: background-color 0.2s ease;
            width: 100%; /* Make buttons full width */
            text-align: center;
        }

        .settings-option button:hover {
            background-color: #303f9f;
        }

        body.dark-mode .settings-option button {
            background-color: #6366f1;
        }
        body.dark-mode .settings-option button:hover {
            background-color: #4f46e5;
        }

        /* Mini flashcards container styles (now always hidden) */
        #mini-flashcards-container {
            /* This container will always be hidden */
            display: none;
        }
    </style>
</head>
<body class="bg-gray-100 flex items-center justify-center min-h-screen p-4">

    <div class="main-container bg-white shadow-lg rounded-2xl overflow-hidden">
        <div class="bg-gradient-to-r from-teal-400 to-blue-500 p-6 flex flex-col items-center justify-center rounded-t-2xl relative">
            <div class="absolute top-4 left-4 small-flashcard-icon-container">
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" class="w-6 h-6 text-white">
                    <path fill="currentColor" d="M20 2H4c-1.1 0-2 .9-2 2v16c0 1.1.9 2 2 2h16c1.1 0 2-.9 2-2V4c0-1.1-.9-2-2-2zm-1 16H5V6h14v12zm-2-9H7v2h10V9zm0 4H7v2h10v-2z"/>
                </svg>
                <span class="text-white text-sm font-medium">My Flashcards</span>
            </div>

            <div class="absolute top-4 right-4 flex space-x-2">
                <button id="sound-button" class="icon-button" aria-label="Sound">
                    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24">
                        <path d="M3 10v4c0 .55.45 1 1 1h3l3.29 3.29c.63.63 1.71.18 1.71-.71V6.41c0-.89-1.08-1.34-1.71-.71L7 9H4c-.55 0-1 .45-1 1zm13.5 2c0-1.77-1.02-3.29-2.5-4.03v8.05c1.48-.73 2.5-2.25 2.5-4.02zM14 3.23v2.06c2.89.86 5 3.54 5 6.71s-2.11 5.85-5 6.71v2.06c4.01-.98 7-4.74 7-8.77s-2.99-7.79-7-8.77z"/>
                    </svg>
                </button>
                <button id="settings-button" class="icon-button" aria-label="Settings">
                    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24">
                        <path d="M19.43 12.98c.04-.32.07-.64.07-.98s-.03-.66-.07-.98l2.11-1.65c.19-.15.24-.42.12-.64l-2-3.46c-.12-.22-.39-.3-.61-.22l-2.49 1c-.52-.4-1.09-.74-1.71-.98L14.82 2.8c-.08-.23-.3-.38-.54-.38h-4c-.24 0-.46.15-.55.38L9.25 5.35c-.62.24-1.19.58-1.71.98l-2.49-1c-.22-.09-.49 0-.61.22l-2 3.46c-.12.22-.07.49.12.64l2.11 1.65c-.04.32-.07.64-.07.98s.03.66.07.98l-2.11 1.65c-.19.15-.24.42-.12.64l2 3.46c.12.22.39.3.61.22l2.49-1c.52.4 1.09.74 1.71.98l.37 2.53c.08.23.3.38.55.38h4c.24 0 .46-.15.55-.38l.37-2.53c.62-.24 1.19-.58 1.71-.98l2.49 1c.22.09.49 0 .61-.22l2-3.46c.12-.22.07-.49-.12-.64l-2.11-1.65zM12 15.5c-1.93 0-3.5-1.57-3.5-3.5s1.57-3.5 3.5-3.5 3.5 1.57 3.5 3.5-1.57 3.5-3.5 3.5z"/>
                    </svg>
                </button>
                </div>

            <div class="w-36 h-36 flex items-center justify-center rounded-full shadow-lg bg-white bg-opacity-20">
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" class="w-24 h-24 text-white">
                    <path fill="currentColor" d="M20 2H4c-1.1 0-2 .9-2 2v16c0 1.1.9 2 2 2h16c1.1 0 2-.9 2-2V4c0-1.1-.9-2-2-2zm-1 16H5V6h14v12zm-2-9H7v2h10V9zm0 4H7v2h10v-2z"/>
                </svg>
            </div>
        </div>

        <div id="flashcard" class="flashcard rounded-xl shadow-md mx-4 border-2 border-white relative">
            <div id="flashcard-front" class="flashcard-content flashcard-front">
                <p id="question-text" class="text-2xl font-semibold px-4">Loading question...</p>
                <button id="reveal-button" class="absolute bottom-4 right-4 text-white p-2 rounded-full bg-blue-700 hover:bg-blue-600 transition-colors" aria-label="Reveal Answer">
                    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" class="w-6 h-6">
                        <path fill="currentColor" d="M12 4.5c-4.72 0-8.9 3.14-10 7.5 1.1 4.36 5.28 7.5 10 7.5s8.9-3.14 10-7.5c-1.1-4.36-5.28-7.5-10-7.5zm0 13c-2.48 0-4.5-2.02-4.5-4.5s2.02-4.5 4.5-4.5 4.5 2.02 4.5 4.5-2.02 4.5-4.5 4.5zm0-7c-1.38 0-2.5 1.12-2.5 2.5s1.12 2.5 2.5 2.5 2.5-1.12 2.5-2.5-1.12-2.5-2.5-2.5z"/>
                    </svg>
                </button>
            </div>

            <div id="flashcard-back" class="flashcard-content flashcard-back">
                <p id="answer-text" class="text-2xl font-semibold px-4">Loading answer...</p>
                <button id="flip-back-button" class="absolute bottom-4 right-4 text-white p-2 rounded-full bg-blue-700 hover:bg-blue-600 transition-colors" aria-label="Flip back to question">
                    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" class="w-6 h-6">
                        <path fill="currentColor" d="M12 8l-6 6 1.41 1.41L12 10.83l4.59 4.58L18 14z"/>
                    </svg>
                </button>
            </div>
        </div>

        <div id="mini-flashcards-container" class="hidden">
            </div>

        <div class="flex justify-center items-center p-4">
            <button id="prev-button" class="nav-arrow mr-4" aria-label="Previous Card">
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor">
                    <path d="M15.41 7.41L14 6l-6 6 6 6 1.41-1.41L10.83 12z"/>
                </svg>
            </button>
            <span id="card-progress" class="text-lg font-medium text-gray-700">0/0</span>
            <button id="next-button" class="nav-arrow ml-4" aria-label="Next Card">
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor">
                    <path d="M8.59 16.59L13.17 12 8.59 7.41 10 6l6 6-6 6z"/>
                </svg>
            </button>
        </div>
    </div>

    <div id="settings-modal" class="settings-modal-overlay">
        <div class="settings-modal-content">
            <button id="close-settings-button" class="close-button" aria-label="Close Settings">&times;</button>
            <h2>Settings</h2>
            <div class="settings-option">
                <label>App Theme</label>
                <button id="light-mode-option" class="py-2 px-4 rounded-md">Light Mode</button>
                <button id="dark-mode-option" class="py-2 px-4 rounded-md">Dark Mode</button>
                <button id="default-mode-option" class="py-2 px-4 rounded-md">Default Mode</button>
            </div>
            </div>
    </div>

    <script>
        // Define your flashcard data
        const flashcards = [
            {
                question: "What is Design Thinking?",
                answer: "Design Thinking is a human-centered approach to innovation that focuses on understanding user needs, generating creative ideas, and developing solutions to problems."
            },
            {
                question: "What are the 3 main stages of Design Thinking?",
                answer: "The 3 main stages are: Empathize (understand the user's needs), Ideate (generate and explore ideas), and Prototype (create and test solutions)."
            },
            {
                question: "What is the purpose of User Research in Design Thinking?",
                answer: "User research helps understand the user's needs, desires, and challenges to inform design decisions and create a more effective and user-friendly product or service."
            },
            {
                question: "What is the difference between Design Thinking and Innovation?",
                answer: "Design Thinking is a process for problem-solving, while Innovation is the result of Design Thinking, leading to new and improved products, services, or processes."
            },
            {
                question: "What is Prototyping and why is it important?",
                answer: "Prototyping is the creation of a physical or digital representation of a product or service to test and refine the design. It's important because it allows for early feedback, iterations, and improvement before full production."
            }
        ];

        // Get references to DOM elements
        const flashcardElement = document.getElementById('flashcard');
        const flashcardFront = document.getElementById('flashcard-front');
        const flashcardBack = document.getElementById('flashcard-back');
        const questionText = document.getElementById('question-text');
        const answerText = document.getElementById('answer-text');
        const revealButton = document.getElementById('reveal-button'); // Eye icon on front
        const flipBackButton = document.getElementById('flip-back-button'); // Up arrow on back
        const prevButton = document.getElementById('prev-button');
        const nextButton = document.getElementById('next-button');
        const cardProgress = document.getElementById('card-progress');
        const soundButton = document.getElementById('sound-button'); // Get reference to the sound button
        const settingsButton = document.getElementById('settings-button'); // Get reference to the settings button
        // Removed minimizeButton reference

        // Settings Modal Elements
        const settingsModal = document.getElementById('settings-modal');
        const closeSettingsButton = document.getElementById('close-settings-button');
        const lightModeOption = document.getElementById('light-mode-option');
        const darkModeOption = document.getElementById('dark-mode-option');
        const defaultModeOption = document.getElementById('default-mode-option');

        // Removed state variables related to minimize/maximize
        let currentCardIndex = 0;
        let isFlipped = false; // To track if the card is showing the answer

        // Get reference to the mini flashcards container (will remain hidden)
        const miniFlashcardsContainer = document.getElementById('mini-flashcards-container');


        // SpeechSynthesisUtterance instance for text-to-speech
        let utterance = new SpeechSynthesisUtterance();
        utterance.lang = 'en-US'; // Set language for better pronunciation

        /**
         * Displays the current flashcard based on currentCardIndex.
         * Updates the question, answer, and progress indicator.
         */
        function displayCard() {
            const card = flashcards[currentCardIndex];
            questionText.textContent = card.question;
            answerText.textContent = card.answer;
            cardProgress.textContent = `${currentCardIndex + 1}/${flashcards.length}`;

            // Reset flip state when changing cards
            if (isFlipped) {
                flashcardElement.classList.remove('flipped');
                isFlipped = false;
            }
            // Stop any ongoing speech when changing cards
            speechSynthesis.cancel();

            // Ensure main flashcard is visible when displayCard is called
            flashcardElement.classList.remove('hidden');
            // Ensure navigation buttons are visible in full card view
            prevButton.classList.remove('hidden');
            nextButton.classList.remove('hidden');
            cardProgress.classList.remove('hidden');
        }

        /**
         * Toggles the visibility of the answer by flipping the flashcard.
         */
        function toggleAnswerVisibility() {
            isFlipped = !isFlipped;
            if (isFlipped) {
                flashcardElement.classList.add('flipped');
            } else {
                flashcardElement.classList.remove('flipped');
            }
            // Stop any ongoing speech when flipping the card
            speechSynthesis.cancel();
        }

        /**
         * Navigates to the previous flashcard.
         * Prevents going below the first card.
         */
        function showPreviousCard() {
            if (currentCardIndex > 0) {
                currentCardIndex--;
                displayCard();
            }
        }

        /**
         * Navigates to the next flashcard.
         * Prevents going beyond the last card.
         */
        function showNextCard() {
            if (currentCardIndex < flashcards.length - 1) {
                currentCardIndex++;
                displayCard();
            }
        }

        /**
         * Reads the text content of the currently displayed side of the flashcard.
         */
        function readFlashcardContent() {
            // If speech is already ongoing, stop it
            if (speechSynthesis.speaking) {
                speechSynthesis.cancel();
                return; // Exit function after stopping
            }

            let textToRead = '';
            if (isFlipped) {
                // If flipped, read the answer
                textToRead = answerText.textContent;
            } else {
                // If not flipped, read the question
                textToRead = questionText.textContent;
            }

            if (textToRead) {
                utterance.text = textToRead;
                speechSynthesis.speak(utterance);
            }
        }

        /**
         * Sets the application theme based on the provided mode.
         * @param {string} mode - 'light', 'dark', or 'default'.
         */
        function setTheme(mode) {
            const body = document.body;
            if (mode === 'dark') {
                body.classList.add('dark-mode');
                localStorage.setItem('theme', 'dark');
            } else if (mode === 'light') {
                body.classList.remove('dark-mode');
                localStorage.setItem('theme', 'light');
            } else if (mode === 'default') {
                localStorage.removeItem('theme'); // Remove stored preference
                // Re-evaluate based on system preference immediately
                if (window.matchMedia('(prefers-color-scheme: dark)').matches) {
                    body.classList.add('dark-mode');
                } else {
                    body.classList.remove('dark-mode');
                }
            }
        }

        /**
         * Initializes theme based on localStorage or system preference.
         */
        function initializeTheme() {
            const savedTheme = localStorage.getItem('theme');
            const prefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches;
            const body = document.body;

            if (savedTheme) {
                // If a theme is saved, use it
                setTheme(savedTheme);
            } else {
                // If no theme is saved, use system preference as default
                if (prefersDark) {
                    body.classList.add('dark-mode');
                } else {
                    body.classList.remove('dark-mode');
                }
            }
        }

        /**
         * Shows the settings modal.
         */
        function showSettingsModal() {
            settingsModal.classList.add('show');
        }

        /**
         * Hides the settings modal.
         */
        function hideSettingsModal() {
            settingsModal.classList.remove('show');
        }

        // Removed toggleMinimizeView and renderMiniFlashcards functions

        // Add event listeners
        revealButton.addEventListener('click', (event) => {
            event.stopPropagation();
            toggleAnswerVisibility();
        });

        flipBackButton.addEventListener('click', (event) => {
            event.stopPropagation();
            toggleAnswerVisibility();
        });

        flashcardElement.addEventListener('click', toggleAnswerVisibility);
        prevButton.addEventListener('click', showPreviousCard);
        nextButton.addEventListener('click', showNextCard);
        soundButton.addEventListener('click', readFlashcardContent);

        // Removed event listener for the minimize button

        // Event listeners for Settings Modal
        settingsButton.addEventListener('click', showSettingsModal);
        closeSettingsButton.addEventListener('click', hideSettingsModal);

        lightModeOption.addEventListener('click', () => {
            setTheme('light');
            hideSettingsModal(); // Close modal after selection
        });
        darkModeOption.addEventListener('click', () => {
            setTheme('dark');
            hideSettingsModal(); // Close modal after selection
        });
        defaultModeOption.addEventListener('click', () => {
            setTheme('default');
            hideSettingsModal(); // Close modal after selection
        });

        // Listen for changes in system color scheme preference
        window.matchMedia('(prefers-color-scheme: dark)').addEventListener('change', (event) => {
            // Only re-apply if the user is in 'default' mode
            if (!localStorage.getItem('theme')) {
                if (event.matches) {
                    document.body.classList.add('dark-mode');
                } else {
                    document.body.classList.remove('dark-mode');
                }
            }
        });


        // Initialize the first card and theme when the page loads
        document.addEventListener('DOMContentLoaded', () => {
            initializeTheme(); // Initialize theme
            displayCard(); // Then display the card
            // miniFlashcardsContainer.classList.add('hidden'); // This line is no longer strictly necessary as the container is permanently hidden in HTML/CSS
        });
    </script>
</body>
</html>
