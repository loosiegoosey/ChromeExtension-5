## Overview

Overview
The project comprises various Chrome extensions designed to enhance web browsing by providing functionalities such as message passing, content manipulation, audio controls, web scraping, and fetching the word of the day.

##
## Features

Features
- **Message Passing**: Send and receive messages between content scripts and background scripts.
- **Content Manipulation**: Modify webpage content dynamically based on specific conditions.
- **Audio Controls**: Play and pause audio tracks via a Chrome extension popout.
- **Web Scraping**: Execute web scraping commands from a popout script.
- **Word of The Day**: Fetch and display the word of the day from an external API.

##
## Installation Instructions

Installation Instructions
1. **Clone the repository:**
    ```sh
    git clone https://github.com/Nainik2509/ChromeExtension.git
    ```

2. **Load the extension(s) in Chrome:**
    1. Open Chrome and navigate to `chrome://extensions/`.
    2. Enable `Developer mode`.
    3. Click on `Load unpacked`.
    4. Select the individual extension folders (like "Background Message", "Content Script", "Epic Mode", etc.) from the cloned repository to load each extension.

##
## Usage Examples

Usage Examples

### Background Message Extension
1. **Send Message from Content Script to Background:**
    ```javascript
    // contentScript.js
    chrome.runtime.sendMessage({ name: "message" }, (response) => {
        document.querySelector("body").innerHTML = response.text;
    });
    ```

2. **Receive Message in Background Script:**
    ```javascript
    // background.js
    chrome.runtime.onMessage.addListener((msg, sender, response) => {
        if (msg.name == 'message') {
            response({ text: "This is a response..." });
        }
    });
    ```

### Content Manipulation
1. **Modify Content on BBC Sport Pages:**
    ```javascript
    // contentScript.js
    if (window.location.href.includes('bbc.com/sport')) {
        document.querySelector('.gs-c-promo-heading').innerText = 'A new headline';
        document.querySelector('.gs-c-promo-heading').parentElement.querySelector('p').innerText = 'A new block of summary information.';
    }
    ```

### Epic Mode (Audio Control)
1. **Play and Pause Tracks:**
    ```javascript
    // popout.js
    document.querySelector('button.play').addEventListener('click', function() {
        var selTrack = document.querySelector('select').value;
        chrome.runtime.sendMessage({ name: "playTrack", track: selTrack });
    });

    document.querySelector('button.pause').addEventListener('click', function() {
        chrome.runtime.sendMessage({ name: "pauseTrack" });
    });

    // bg.js
    chrome.runtime.onMessage.addListener((msg, sender, response) => {
        var audioEle = document.querySelector('.audio-element');
        if (msg.name == 'playTrack') {
            audioEle.src = `${msg.track}.mp3`;
            audioEle.play();
        } else if (msg.name == 'pauseTrack') {
            audioEle.pause();
        }
    });
    ```

### Web Scraper
1. **Run Web Scraping Commands:**
    ```javascript
    // contentScript.js
    chrome.runtime.onMessage.addListener((msg, sender, response) => {
        if (msg.command == "runCommands") {
            window.ScraperExt = [];
            var scrapeObj = msg.data;
            getNextItem(scrapeObj, 0);
            window.ScraperExt = [];
        }
    });

    function getNextItem(obj, index) {
        // Recursive scraping logic
    }

    // popout.js
    chrome.runtime.onMessage.addListener((msg, sender, response) => {
        if (msg.command == "run-complete") {
            document.querySelector("textarea").value = JSON.stringify(msg.data);
            document.querySelector("textarea").style.display = "block";
            alert("Command has been run");
        }
    });

    function createCommandObject() {
        var commandsArr = [];
        var commands = document.querySelectorAll(".commands-list .command-item");
        // Build commandsObj logic
    }
    ```

### Word of The Day
1. **Fetch and Display Word of The Day:**
    ```javascript
    // script.js
    chrome.runtime.sendMessage({ name: "fetchWords" }, (response) => {
        document.querySelector('h1').innerHTML = response.word;
        document.querySelector('p').innerHTML = response.desc;
    });

    // background.js
    chrome.runtime.onMessage.addListener((msg, sender, response) => {
        if (msg.name == 'fetchWords') {
            const apiKey = 'YOUR_API_KEY';
            const dateStr = new Date().toISOString().slice(0, 10);
            const apiCall = `https://api.wordnik.com/v4/words.json/wordOfTheDay?date=${dateStr}&api_key=${apiKey}`;
            // Fetch and respond with word of the day logic
        }
    });
    ```

##
## Code Summary

Code Summary
- **Background Message:**
  - **background.js**: Listens for a message and sends a response.
  - **contentScript.js**: Sends a message to the background script and updates the content based on the response.
  
- **Content Script:**
  - **contentScript.js**: Modifies BBC Sport webpages by changing headlines and summaries.

- **Epic Mode:**
  - **bg.js**: Handles playing and pausing audio tracks based on messages.
  - **popout.js**: Sends play and pause track messages based on user interaction.

- **Web Scrape:**
  - **contentScript.js**: Executes web scraping commands.
  - **popout.js**: Displays the results of scraping commands and constructs commands to be sent.

- **Word of The Day:**
  - **background.js**: Fetches the word of the day from an external API.
  - **script.js**: Sends fetch request and displays the word of the day.

##
## License

License
This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.