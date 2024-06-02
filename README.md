# Chatbot projects

I was given the task to create two different chatbots. The first one was a rule-based chatbot, and the second one was an AI-based chatbot. I used Python and Visual Studio Code to write my code, and Flask for the GUI.

## Rule-based chatbot

The rule-based chatbot is for a restaurant's website. I created a predefined set of rules to dictate the bot's responses based on specific keywords. For some intents, such as opening hours and address, I created multiple responses that the bot would choose from according to specific inputs by the user. As for greetings, I created multiple responses that the bot would randomly pick from. When the user says "thank you", the bot responds with "Is there anything else I can help you with?". If the answer is no, the bot will say goodbye, if the answer is yes or another question is entered, the program keeps going. I did add some minimal styling and content here for the restaurant's website, but my focus was on the functionality of the chatbot.

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
      
## AI-based chatbot

Rule-based chatbots are limited in their ability to respond to variations in user input that do not match predefined patterns. My goal was to create a more flexible chatbot by using artificial intelligence, particularly natural language processing and machine learning. 

My AI-based chatbot is for answering Amazon product related questions. Users can ask specific product recommendations based on price, user ratings or bestsellers. I integrated my chatbot with an Amazon product API to fetch real-time information and provide dynamic responses. I trained my model using a fashion product dataset and spaCy's training module.





