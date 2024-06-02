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

train_ner_model.py:

            import spacy
            from spacy.training import Example
            import csv
            
            # This line creates a blank spaCy NLP pipeline for the English language
            nlp = spacy.blank("en") 
            
            # Defines the function to load training data from a CSV file and prepare it for training the NER (Named Entity Recognition) model
            def load_training_data(file_path):
                training_data = [] 
                with open(file_path, "r", newline="", encoding="utf-8") as csvfile: 
                    csvreader = csv.DictReader(csvfile) 
                    for row in csvreader: # Starts a loop to iterate over each row
                        text = row["Product Name"] 
                        entities = [] # Initializes an empty list to store the entities extracted from the rows
                        entities.append((0, len(row["Product Name"]), "PRODUCT_NAME")) 
                        entities.append((len(row["Product Name"]) + 1, len(row["Product Name"]) + len(row["Brand"]), "BRAND"))  
                        entities.append((len(row["Product Name"]) + len(row["Brand"]) + 1, len(row["Product Name"]) + len(row["Brand"]) + len(row["Category"]), "CATEGORY"))  
                        entities.append((len(row["Product Name"]) + len(row["Brand"]) + len(row["Category"]) + 1, len(row["Product Name"]) + len(row["Brand"]) + len(row["Category"]) + len(row["Price"]), "PRICE")) 
                        entities.append((len(row["Product Name"]) + len(row["Brand"]) + len(row["Category"]) + len(row["Price"]) + 1, len(row["Product Name"]) + len(row["Brand"]) + len(row["Category"]) + len(row["Price"]) + len(row["Rating"]), "RATING"))  
                        entities.append((len(row["Product Name"]) + len(row["Brand"]) + len(row["Category"]) + len(row["Price"]) + len(row["Rating"]) + 1, len(row["Product Name"]) + len(row["Brand"]) + len(row["Category"]) + len(row["Price"]) + len(row["Rating"]) + len(row["Color"]), "COLOR"))
                        entities.append((len(row["Product Name"]) + len(row["Brand"]) + len(row["Category"]) + len(row["Price"]) + len(row["Rating"]) + len(row["Color"]) + 1, len(row["Product Name"]) + len(row["Brand"]) + len(row["Category"]) + len(row["Price"]) + len(row["Rating"]) + len(row["Color"]) + len(row["Size"]), "SIZE"))  
                        training_data.append((text, {"entities": entities}))
                return training_data
            
            # Load the training data from your CSV file
            file_path = "file_path_here"
            training_data = load_training_data(file_path)
            
            # Process the training data and update the NER model
            for text, annotations in training_data:
                  doc = nlp.make_doc(text)
                  example = Example.from_dict(doc, annotations)
                  nlp.update([example], losses={})
            
            # Save the trained NER model
            nlp.to_disk("file_path_here")

app.py:

      from flask import Flask, render_template, request
      import spacy
      import requests
      import re
      
      # Initialize Flask app
      app = Flask(__name__)
      
      # Load the previously trained spaCy model for named entity recognition (NER)
      custom_nlp = spacy.load("file_path_here")
      
      # Define the headers for the API request
      headers = {
          "X-RapidAPI-Key": "addb8aa98cmsh7dfe2f3bcb35e26p11a374jsn236fe1174741",
          "X-RapidAPI-Host": "amazon-product-data6.p.rapidapi.com"
      }
      
      # Function to process the query and fetch products from the API
      def process_query(keyword, url, min_price=None, max_price=None, only_best_sellers=False, only_high_rated=False):
          querystring = {
              "keyword": keyword,
              "page": "1",
              "country": "US",
              "sort_by": "feature"
          }
      
          try:
              response = requests.get(url, headers=headers, params=querystring)
      
              if response.status_code == 200:
                  try:
                      information = response.json()
                      if 'data' in information:
                          products = information['data']
                          if products:
                              best_sellers = []
                              others = []
                              high_rated = []
      
                              for product in products:
                                  bestseller = product.get('is_best_seller', 'N/A')
                                  highrated = product.get('stars', 'N/A')
                                  highrated_value = float(highrated) if highrated is not None else 0
                                  ratingcount = product.get('rating_count', 'N/A')
                                  ratingcount_value = float(ratingcount) if ratingcount is not None else 0
                                  price_str = product.get('price', 'N/A')
                                  if price_str and price_str != 'N/A':
                                      try:
                                          price_value = float(price_str.replace('$', '').replace(',', ''))
                                          if min_price is not None and price_value < min_price:
                                              continue
                                          if max_price is not None and price_value > max_price:
                                              continue
                                      except ValueError:
                                          continue
                                  else:
                                      continue
      
                                  if bestseller:
                                      best_sellers.append((price_value, product))
                                  elif highrated_value >= 4.4 and ratingcount_value >= 15:
                                      high_rated.append((price_value, product))
                                  else:
                                      others.append((price_value, product))
      
                              best_sellers.sort(key=lambda x: x[0])
                              others.sort(key=lambda x: x[0])
                              high_rated.sort(key=lambda x: x[0])
      
                              if only_best_sellers:
                                  relevant_products = [product for _, product in best_sellers[:5]]
                              elif only_high_rated:
                                  relevant_products = [product for _, product in high_rated[:5]]
                              else:
                                  relevant_products = [product for _, product in others[:5]]
      
                              if relevant_products:
                                  response_text = f"Here are some relevant products related to your question: '{keyword}':\n\n"
                                  for product in relevant_products:
                                      title = product.get('title', 'N/A')
                                      price = product.get('price', 'N/A')
                                      url = product.get('url', 'N/A')
                                      stars = product.get('stars', 'N/A')
                                      rating_count = product.get('rating_count', 'N/A')
                                      is_best_seller = product.get('is_best_seller', 'N/A')
                                      response_text += (
                                          f"- {title}\n"
                                          f"  Price: {price}\n"
                                          f"  Rated: {stars} stars by {rating_count} customers\n"
                                          f"  Best seller: {is_best_seller}\n"
                                          f"  See more information here: {url}\n\n"
                                      )
                                  return response_text.strip()
                              else:
                                  if only_best_sellers:
                                      return f"Sorry, I couldn't find any best-seller products related to '{keyword}'. Please specify your query more clearly."
                                  elif only_high_rated:
                                      return f"Sorry, I couldn't find any high-rated products related to '{keyword}'. Please specify your query more clearly."
                                  else:
                                      return f"Sorry, I couldn't find any products within the price range of ${min_price} to ${max_price} related to '{keyword}'."
                          else:
                              return f"Sorry, I couldn't find any products related to '{keyword}'."
                      else:
                          return "No 'data' key found in the API response."
                  except Exception as e:
                      return f"Error processing API response: {e}"
              else:
                  return f"Error fetching data from the API: {response.text}"
          except requests.exceptions.RequestException as e:
              return f"Error: Unable to connect to the API server. {e}"
      
      
      # Define the chatbot function that analyzes the user's statement, constructs a query, and calls process_query() function
      def chatbot(statement, url):
          product_intents = ["show", "recommend", "find", "searching", "looking for", "buy"]
          best_seller_keywords = ["best seller", "top seller", "popular", "bestseller", "best-seller", "best-selling", "bestselling", "best selling", "best"]
          high_rated_keywords = ["good", "top", "top-rated", "highly", "highest rated", "toprated", "top rated", "highest-rated", "loved", "rated 4 stars", "stars", "highest", "rated"]
          product_keywords = ["shoe", "shoes", "tennis shoes", "high heel shoes", "high heels", "women's shoes", "kids shoes", ""]
          size_keywords = ["size"]
          statement = custom_nlp(statement.lower())
      
          if any(intent in statement.text for intent in product_intents) and any(keyword in statement.text for keyword in product_keywords):
              query = ""
              prices = []
              is_size = False
              only_best_sellers = False
              only_high_rated = False
              for token in statement:
                  if token.text in size_keywords:
                      is_size = True
                      query += f"{token.text} "
                  elif token.like_num:
                      if is_size:
                          query += f"{token.text} "
                          is_size = False
                      else:
                          prices.append(float(token.text))
                          query += f"{token.text} "
                  else:
                      query += f"{token.text} "
      
              if any(keyword in statement.text for keyword in best_seller_keywords):
                  only_best_sellers = True
              elif any(keyword in statement.text for keyword in high_rated_keywords):
                  only_high_rated = True
      
              if query:
                  min_price, max_price = None, None
                  if len(prices) == 1:
                      max_price = prices[0]
                  elif len(prices) == 2:
                      min_price, max_price = min(prices), max(prices)
                  product_information = process_query(query.strip(), url, min_price, max_price, only_best_sellers, only_high_rated)
                  if product_information is not None:
                      return product_information
                  else:
                      return "Something went wrong."
              else:
                  return "Please specify your query more clearly."
          else:
              return "This question is not related to products. Please ask a product-related question."
      
      @app.route("/", methods=["GET", "POST"])
      def index():
          if request.method == "POST":
              user_query = request.form["query"]
              url = "https://amazon-product-data6.p.rapidapi.com/product-by-text"
              if re.search(r"\b(bye|goodbye|exit)\b", user_query, re.IGNORECASE):
                  bot_response = "Goodbye! Have a good day."
              elif re.search(r"\b(thank you|thanks|thankyou)\b", user_query, re.IGNORECASE):
                  bot_response = "Is there anything else I can help you with today?"
              elif re.search(r"\b(no|no, thank you|no, thanks)\b", user_query, re.IGNORECASE):
                  bot_response = "Okay, goodbye! Have a great day."
              else:
                  bot_response = chatbot(user_query, url)
              return render_template("index.html", user_query=user_query, bot_response=bot_response)
          return render_template("index.html")
      
      if __name__ == "__main__":
          app.run(debug=True)


index.html:

      <!DOCTYPE html>
      <html lang="en">
      <head>
          <meta charset="UTF-8">
          <title>Amazon Product Chatbot</title>
          <style>
              body {
                  font-family: Georgia, 'Times New Roman', Times, serif;
                  margin: 0;
                  padding: 0;
                  background-color: #eef5db;
              }
              .container {
                  width: 80%;
                  margin: auto;
                  overflow: hidden;
              }
              
              #main {
                  background: #b8d8d8;
                  color: #4f6367;
                  padding: 20px;
                  margin-top: 50px;
              }
              input[type="text"] {
                  padding: 10px;
                  margin: 5px;
                  width: 30%;
              }
      
              input[type="submit"] {
                  padding: 10px;
                  margin: 5px;
              }
      
              #chat-box {
                  height: 300px;
                  overflow-y: scroll;
                  border: 1px solid #ccc;
                  padding: 10px;
                  margin-bottom: 20px;
                  background: #f9f9f9;
              }
              .user-query, .bot-response {
                  margin-bottom: 10px;
              }
              .user-query {
                  color: #fe5f55;
              }
              .bot-response {
                  color: #678586;
              }
          </style>
      </head>
      <body>
      <div class="container">
          <div id="main">
              <h1>Amazon Product Chatbot</h1>
              <div id="chat-box">
                  {% if user_query and bot_response %}
                  <div class="user-query">You: {{ user_query }}</div>
                  <div class="bot-response">Chelsea: {{ bot_response }}</div>
                  {% else %}
                  <p>Hello! My name is Chelsea. What would you like to find on Amazon?</p>
                  </p>
                  {% endif %}
              </div>
              <form method="POST" action="/">
                  <input type="text" name="query" placeholder="Ask me something about products...">
                  <input type="submit" value="Send">
              </form>
          </div>
      </div>
      </body>
      </html>



