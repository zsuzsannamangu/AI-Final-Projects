# Chatbot projects

I was given the task to create two different chatbots. The first one was a rule-based chatbot, and the second one was an AI-based chatbot. I used Python and Visual Studio Code to write my code, and Flask for the GUI.

## Rule-based chatbot

The rule-based chatbot is for a restaurant's website. I created a predefined set of rules to dictate the bot's responses based on specific keywords. For some intents, such as opening hours and address, I created multiple responses that the bot would choose from according to specific inputs by the user. As for greetings, I created multiple responses that the bot would randomly pick from. When the user says "thank you", the bot responds with "Is there anything else I can help you with?". If the answer is no, the bot will say goodbye, if the answer is yes or another question is entered, the program keeps going. I did add some minimal styling and content here for the restaurant's website, but my focus was on the functionality of the chatbot.

      import re #The re module allows you to create patterns and search, match, and manipulate strings based on these patterns.
      import random
      from flask import Flask, render_template, request, jsonify #We are using Flask to build our restaurant's webpage
          #The render_template function allows you to generate HTML from template files in the templates directory
          #The request object contains all data sent by the client in an HTTP request. Here it's used to get JSON data from a POST request
          #The jsonify function is used to convert Python data structures into JSON format. The JSON format is easy for humans to read and write, and easy for machines to parse and generate.
      
      app = Flask(__name__) #Create a Flask application instance
      
      #Define intents and corresponding patterns
      intents = {
          "greeting": r"^(hi|hello|hey).*$", #The r prefix denotes a raw string, so backlashes will be treated as literal characters
                                           #^ signifies that the pattern should match at the beginning of a line. $ signifies that the pattern should match at the end of a line.
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
          
      def get_intent(user_input): #This function matches user input to intents based on the defined patterns.
          for intent, pattern in intents.items(): #The intents dictionary defines patterns for various intents.
              if re.search(pattern, user_input.lower()): #Using the 're' modul, we search for patterns in the user input which is now all lowercase
                  return intent
          return None
      
      def get_response(intent, user_input): #This function selects a response based on the matched intent and user input
          if intent in responses: #The responses dictionary contains responses for each intent,
                                   
              if isinstance(responses[intent], list):  #The responses dictionary contains multiple responses for some intents
                  return random.choice(responses[intent]) #The bot will randomnly choose the answer to intents with multiple responses
              elif isinstance(responses[intent], dict):
                  for keyword, response in responses[intent].items(): #The responses dictionary contains keyword-response pairs for the "opening_hours" and "address" intent.
                      if keyword in user_input.lower(): #The answer will be determined by the keyword used by the user
                          return response
          return "I'm sorry, I didn't understand that."
      
      @app.route('/') #This decorator tells Flask to call the index() function when the root URL('/') of the web application is accessed
      def index(): #index() renders the main HTML page for the web application
          return render_template('index.html') #Render an HTML template called index.html, Flask looks for this file in the templates directory
      
      @app.route('/chat', methods=['POST'])  #This decorator defines a route for the URL '/chat' that only accepts POST requests
      def chat(): #This function handles incoming messages from the client and generates responses
          user_input = request.json.get('message') #Retrieve the user input by extracting the message field from the JSON payload of the incoming POST request
          intent = get_intent(user_input) #Analyze user input and determine the intent based on predefined patterns
          goodbye_pattern = re.compile(r'\b(bye|goodbye|exit|by|bey|goodby|goodbyes|good-bye|good-by|byes|good-byes|good-bys)[!.?]*$', re.IGNORECASE)
          if re.search(goodbye_pattern, user_input):
              return jsonify(response="Goodbye! Have a great day.")
          if intent: #If an intent was identified
              response = get_response(intent, user_input) #Generate response based on identified intent
              if intent == "thank_you": #If user says thank you
                  return jsonify(response=response, prompt_reply=True) #Return a JSON response with the generated response and an additional flag (prompt_reply=True) to indicate that the chatbot should prompt the user for further input
              return jsonify(response=response) #If intent is not thank you, the function returns a JSON response with the generated response
          else: #If no intent was identified
              return jsonify(response="I'm sorry, I didn't understand that.") #Return this JSON response
      
      if __name__ == '__main__':
          app.run(debug=True) #Start the Flask development server, with debug mode enabled
      
## AI-based chatbot

Rule-based chatbots are limited in their ability to respond to variations in user input that do not match predefined patterns. My goal was to create a more flexible chatbot by using artificial intelligence, particularly natural language processing and machine learning. 

My AI-based chatbot is for answering Amazon product related questions. Users can ask specific product recommendations based on price, user ratings or bestsellers. I integrated my chatbot with an Amazon product API to fetch real-time information and provide dynamic responses. I trained my model using a fashion product dataset and spaCy's training module.





