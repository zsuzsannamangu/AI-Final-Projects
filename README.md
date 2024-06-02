# Chatbot projects

I was given the task to create two different chatbots. The first one was a rule-based chatbot, and the second one was an AI-based chatbot. I used Python and Visual Studio Code to write my code, and Flask for the GUI.

## Rule-based chatbot

The rule-based chatbot is for a restaurant's website. I created a predefined set of rules to dictate the bot's responses based on specific keywords. For some intents, such as opening hours and address, I created multiple responses that the bot would choose from according to specific inputs by the user. As for greetings, I created multiple responses that the bot would randomly pick from. When the user says "thank you", the bot responds with "Is there anything else I can help you with?". If the answer is no, the bot will say goodbye, if the answer is yes or another question is entered, the program keeps going. I did add some minimal styling and content here for the restaurant's website, but my focus was on the functionality of the chatbot.

app.py:

      import re
      import random
      from flask import Flask, render_template, request, jsonify #We are using Flask to build our restaurant's webpage
      
      app = Flask(__name__) #Create a Flask application instance
      
      #Define intents and corresponding patterns
      intents = {
          "greeting": r"^(hi|hello|hey).*$",
          "address": r"(address|location|located|street)\??",
          "opening_hours": r"(hours|close|open|hours of operation|schedule)\??",
          "special_diets": r"(vegan|vegetarian|gluten free|nut free|soy free|dietery restrictions|special diet)\??",
          "sourcing": r"(source|sourcing|locally|organic|organically|grow)\??",
          "reservation": r"(reservations|reservation|walk-ins|walk-in|walk in|walk ins)\??",
          "delivery": r"(delivery|deliver|delivery fee)\??",
          "online_order": r"(order online|online|website|phone number|order|place an order|status)\??",
          "kids_menu": r"(kids|kid)\??",
          "accessibility": r"(ada|accessibility|wheelchair|accessible|disability|disabilities|special needs)\??",
          "more_help": r"(help|person|human|call|phone|someone|assistance|assist)\??",
          "thank_you": r"\b(thank\s*you|thanks|thank\s*oyu|thnak\s*you|thank\s*u|thank\s*yuo|thx|ty)[!.?]*$"
      }
      
      #Define responses for each intent
      responses = {
          "greeting": ["How can I help you today?", "What can I do for you?", "How can I assist you?"],
          "address": {
              "address": "Our address is 123 A Street, Portland, Oregon.",
              "located": "We are located at 123 A Street, Portland, Oregon.",
              "location": "We are located at 123 A Street, Portland, Oregon.",
              "street": "123 A Street, Portland, Oregon."
              },
          "opening_hours": {
              "open": "We are open every day from 11am to 10pm.",
              "close": "We close at 10pm every day.",
              "hours of operation": "Our hours of operation is 11am to 10pm every day.",
              "hours": "Our hours are 11am to 10pm Monday through Sunday."},
          "special_diets": ["Our restaurant is 100% vegan. We offer many gluten free and nut free options. Soy free options are very limited."],
          "sourcing": ["Everything on our all plant-based menu is farm-to-table direct &/or organically grown."],
          "reservation": ["Call us to reserve a table for parties of 5 or more only, walk-ins available to parties of all sizes. Our number is 123.456.7890."],
          "delivery": ["We've partnered with the bicycle delivery service Cascadian Couriers Collective that delivers to a 1.5 mile radius from our restaurant. The delivery fee is $5."],
          "online_order": ["To place an order, go to ourrestaurant.com or call us at 123.456.7890. If you would like to check on your order status, please call us!"],
          "kids_menu": ["We offer a kids' noodle bowl and a kids' rice bowl for kids 12 and under for $8 each."],
          "accessibility": ["Our restrooms and dining spaces are wheelchair accessible."],
          "more_help": ["It looks like you might want to talk to someone from our team to answer this question, please call us at 123.456.7890!"],
          "thank_you": ["No problem. Anything else I can help you with today?"]
      }

      #This function matches user input to intents based on the defined patterns
      def get_intent(user_input): .
          for intent, pattern in intents.items():
              if re.search(pattern, user_input.lower()):
                  return intent
          return None

      #This function selects a response based on the matched intent and user input
      def get_response(intent, user_input):
          if intent in responses:
              if isinstance(responses[intent], list):
                  return random.choice(responses[intent])
              elif isinstance(responses[intent], dict):
                  for keyword, response in responses[intent].items():
                      if keyword in user_input.lower():
                          return response
          return "I'm sorry, I didn't understand that."

      #This decorator tells Flask to call the index() function when the root URL('/') of the web application is accessed
      @app.route('/')
      def index():
          return render_template('index.html')
      @app.route('/chat', methods=['POST'])

      #This function handles incoming messages from the client and generates responses
      def chat(): 
          user_input = request.json.get('message')
          intent = get_intent(user_input)
          goodbye_pattern = re.compile(r'\b(bye|goodbye|exit|by|bey|goodby|goodbyes|good-bye|good-by|byes|good-byes|good-bys)[!.?]*$', re.IGNORECASE)
          if re.search(goodbye_pattern, user_input):
              return jsonify(response="Goodbye! Have a great day.")
          if intent:
              response = get_response(intent, user_input)
              if intent == "thank_you":
                  return jsonify(response=response, prompt_reply=True)
              return jsonify(response=response)
          else:
              return jsonify(response="I'm sorry, I didn't understand that.")

      #This line starts the Flask development server, with debug mode enabled
      if __name__ == '__main__':
          app.run(debug=True)

index.html:

      <!DOCTYPE html>
      <html lang="en">
      <head>
          <meta charset="UTF-8">
          <meta name="viewport" content="width=device-width, initial-scale=1.0">
          <title>Restaurant Website</title>
          <style>
              body {
                  font-family:'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
                  background-color: white;
                  margin: 0;
                  padding: 0;
                  font-size: 18px;
              }
              header {
                  background-color: #F5DAD2;
                  color: #1c1d1c;
                  padding: 1rem;
                  text-align: center;
              }
              .content {
                  padding: 2rem;
                  text-align: center;
                  margin-left: 10%;
                  margin-right: 10%;
              }
      
              .no-border-table {
                  width: 20%;
                  border-collapse: collapse;
              }
      
              .no-border-table td {
                  padding: 10px;
                  border: none;
                  text-align: left;
              }
      
              .chat-container {
                  position: fixed;
                  bottom: 100px;
                  right: 20px;
                  width: 400px;
                  background-color: white;
                  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
                  border-radius: 8px;
                  overflow: hidden;
                  display: none;
              }
              .chat-header, .chat-footer {
                  padding: 16px;
                  background-color: #F5DAD2;
                  color: #1c1d1c;
              }
              .chat-messages {
                  padding: 16px;
                  height: 300px;
                  overflow-y: auto;
              }
              .message {
                  margin-bottom: 10px;
              }
              .user-message {
                  text-align: right;
              }
              .bot-message {
                  text-align: left;
              }
              input[type="text"] {
                  width: calc(100% - 32px);
                  padding: 8px;
                  margin: 0;
                  border: none;
                  border-radius: 4px;
              }
              .chatbot-icon {
                  position: fixed;
                  bottom: 20px;
                  right: 20px;
                  width: 60px;
                  height: 60px;
                  background-color: #F5DAD2;
                  border-radius: 50%;
                  display: flex;
                  justify-content: center;
                  align-items: center;
                  cursor: pointer;
                  color: white;
                  font-size: 24px;
              }
          </style>
      </head>
      <body>
          <header>
              <h1>WELCOME TO OUR RESTAURANT</h1>
              <p>Enjoy our delicious vegan cuisine!</p>
          </header>
          <div class="content">
              <h2>OUR MENU</h2>
              <p>Explore a variety of vegan dishes, made with organically and locally sourced ingredients!</p>
              <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit, 
                  sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. 
                  Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut.
                  Sed ut perspiciatis unde omnis iste natus error sit voluptatem accusantium doloremque laudantium.</p>
              <table class="no-border-table">
                  <tr>
                      <td>Nemo enim ipsam</td>
                      <td>$12</td>
                  </tr>
                  <tr>
                      <td>Tempor incididunt</td>
                      <td>$15</td>
                  </tr>
                  <tr>
                      <td>Laboris nisi</td>
                      <td>$13</td>
                  </tr>
                  <tr>
                      <td>Tut aliquid</td>
                      <td>$12</td>
                  </tr>
                  <tr>
                      <td>Vex ea commodi</td>
                      <td>$14</td>
                  </tr>
                  <tr>
                      <td>Consequatur</td>
                      <td>$18</td>
                  </tr>
                  <tr>
                      <td>Quis autem</td>
                      <td>$11</td>
                  </tr>
                  <tr>
                      <td>Vel eum iure</td>
                      <td>$17</td>
                  </tr>
                  <tr>
                      <td>Sed quia</td>
                      <td>$14</td>
                  </tr>
              </table>
              </div>
          </div>
          <div class="chat-container" id="chat-container">
              <div class="chat-header">
                  <h3>Chat with us!</h3>
              </div>
              <div class="chat-messages" id="chat-messages">
                  <div class="message bot-message">Marta: Hello! My name is Marta. I'm your virtual assistant.</div>
              </div>
              <div class="chat-footer">
                  <input type="text" id="user-input" placeholder="Type your message here..." onkeypress="handleKeyPress(event)">
              </div>
          </div>
          <div class="chatbot-icon" onclick="toggleChat()">
              💬
          </div>
          <script>
              let awaitingReply = false; //This variable is used to track whether the chatbot is expecting a specific reply from the user, we start by setting it to false
      
              function handleKeyPress(event) { //Detects when the 'Enter' key is pressed and trigger the sendMessage function
                  if (event.key === 'Enter') {
                      sendMessage();
                  }
              }
      
              function toggleChat() { //Shows or hides the chat container when the chatbot icon is clicked
                  const chatContainer = document.getElementById('chat-container');
                  chatContainer.style.display = chatContainer.style.display === 'none' ? 'block' : 'none';
              }
      
              function sendMessage() { //This function handles sending the user's message to the server.
                  const userInput = document.getElementById('user-input').value; //Gets the value from the input field.
                  if (userInput.trim() === '') return; //Returns if the input is empty.
      
                  addMessage('user-message', 'You: ' + userInput); //Adds the user's message to the chat history.
                  document.getElementById('user-input').value = ''; // Clears the input field.
      
                  if (awaitingReply) {
                      handleReply(userInput); //If the bot is awaiting a reply, it calls handleReply.
                  } else {
                      fetchMessage(userInput); //Otherwise, it fetches a new message from the server.
                  }
              }
      
              function fetchMessage(userInput) { //This function sends the user's message to the server and handles the response
                  fetch('/chat', { //Sends a POST request to the /chat endpoint with the user's message.
                      method: 'POST',
                      headers: {
                          'Content-Type': 'application/json'
                      },
                      body: JSON.stringify({ message: userInput })
                  })
                  .then(response => response.json()) //Parses the JSON response from the server.
                  .then(data => { //Handles the parsed JSON data
                      addMessage('bot-message', 'Marta: ' + data.response); //Adds the bot's response to the chat history.
                      if (data.prompt_reply) {
                          awaitingReply = true; //Sets awaitingReply to true if the bot expects a reply.
                      } else if (data.end_conversation) {
                          setTimeout(() => addMessage('bot-message', 'Marta: Goodbye! Have a great day.'), 500); //Adds a goodbye message after a delay if the conversation is ending.
                      }
                  })
                  .catch(error => {
                      console.error('Error:', error); //Logs any errors that occur.
                  });
              }
      
              function handleReply(userInput) { //This function handles specific replies when the bot is expecting a response.
                  if (["no", "no, thank you", "no, thanks"].includes(userInput.toLowerCase())) {
                      addMessage('bot-message', 'Marta: Okay, goodbye! Have a great day.');
                      awaitingReply = false; //Resets awaitingReply to false.
                  } else {
                      awaitingReply = false;
                      fetchMessage(userInput); //If the user's response is not a negative response, it fetches a new message from the server.
                  }
              }
      
              function addMessage(type, text) { //This function adds a message to the chat history.
                  const messageContainer = document.createElement('div'); //Creates a new div element.
                  messageContainer.classList.add('message', type); //Adds the appropriate classes to the message container.
                  messageContainer.textContent = text; //Sets the text content of the message.
      
                  const chatMessages = document.getElementById('chat-messages'); //Gets the chat messages container.
                  chatMessages.appendChild(messageContainer); //Appends the new message to the chat messages container.
                  chatMessages.scrollTop = chatMessages.scrollHeight; //Scrolls to the bottom of the chat messages container.
              }
      
              //Initializes chat visibility to hidden
              document.getElementById('chat-container').style.display = 'none';
          </script>
      </body>
      </html>
      
## AI-based chatbot

Rule-based chatbots are limited in their ability to respond to variations in user input that do not match predefined patterns. My goal was to create a more flexible chatbot by using artificial intelligence, particularly natural language processing and machine learning. 

My AI-based chatbot is for answering Amazon product related questions. Users can ask specific product recommendations based on price, user ratings or bestsellers. I integrated my chatbot with an Amazon product API to fetch real-time information and provide dynamic responses. I trained my model using a fashion product dataset and spaCy's training module.

      




