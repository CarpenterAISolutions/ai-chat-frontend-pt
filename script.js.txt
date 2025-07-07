document.addEventListener('DOMContentLoaded', () => {
    // --- 1. GET REFERENCES TO HTML ELEMENTS ---
    const chatBox = document.getElementById('chat-box');
    const chatForm = document.getElementById('chat-form');
    const userInput = document.getElementById('user-input');
    const loadingSpinner = document.getElementById('loading-spinner');

    // --- 2. SET THE BACKEND API URL ---
    // IMPORTANT: When you deploy your backend, change this to your live URL
    const BACKEND_URL = 'http://127.0.0.1:8000/chat';

    // --- 3. HANDLE FORM SUBMISSION ---
    chatForm.addEventListener('submit', async (event) => {
        event.preventDefault(); // Prevents the page from reloading

        const userMessage = userInput.value.trim();
        if (!userMessage) return; // Don't send empty messages

        // Display user's message immediately and clear the input
        addMessage(userMessage, 'user');
        userInput.value = '';

        // Show the thinking indicator
        loadingSpinner.classList.remove('hidden');

        try {
            // --- 4. SEND MESSAGE TO BACKEND ---
            const response = await fetch(BACKEND_URL, {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ query: userMessage })
            });

            if (!response.ok) {
                throw new Error('Network response was not ok.');
            }

            const data = await response.json();
            
            // --- 5. DISPLAY AI'S RESPONSE ---
            addMessage(data.answer, 'ai');

        } catch (error) {
            console.error('Error:', error);
            addMessage('Sorry, something went wrong. Please try again.', 'ai');
        } finally {
            // Hide the thinking indicator
            loadingSpinner.classList.add('hidden');
        }
    });

    // --- 6. HELPER FUNCTION TO ADD MESSAGES TO THE UI ---
    function addMessage(text, sender) {
        const messageElement = document.createElement('div');
        messageElement.classList.add('message', `${sender}-message`);
        
        const paragraph = document.createElement('p');
        paragraph.textContent = text;
        messageElement.appendChild(paragraph);

        chatBox.appendChild(messageElement);

        // Scroll to the latest message
        chatBox.scrollTop = chatBox.scrollHeight;
    }
});