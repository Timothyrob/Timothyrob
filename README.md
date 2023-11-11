import speech_recognition as sr
import os
import datetime
import requests

def recognize_speech():
    recognizer = sr.Recognizer()

    with sr.Microphone() as source:
        print("Say something...")
        recognizer.adjust_for_ambient_noise(source)
        audio = recognizer.listen(source)

    try:
        print("Recognizing...")
        query = recognizer.recognize_google(audio).lower()
        print(f"You said: {query}")
        return query
    except sr.UnknownValueError:
        print("Sorry, I didn't catch that. Could you please repeat?")
        return None
    except sr.RequestError as e:
        print(f"Could not request results from Google Speech Recognition service; {e}")
        return None

def get_weather():
    # Replace 'YOUR_API_KEY' with your actual OpenWeatherMap API key
    api_key = 'YOUR_API_KEY'
    city = 'YourCity'
    base_url = f'http://api.openweathermap.org/data/2.5/weather?q={city}&appid={api_key}'
    
    try:
        response = requests.get(base_url)
        data = response.json()
        temperature = data['main']['temp']
        description = data['weather'][0]['description']
        return f"The weather in {city} is {description} with a temperature of {temperature} Kelvin."
    except Exception as e:
        return f"Error fetching weather data: {e}"

def remind():
    now = datetime.datetime.now()
    return f"It's {now.strftime('%H:%M')}. Don't forget to complete your tasks!"

def main():
    print("Voice-Controlled Assistant")

    while True:
        command = recognize_speech()

        if command:
            if 'weather' in command:
                print(get_weather())
            elif 'reminder' in command:
                print(remind())
            elif 'exit' in command:
                print("Exiting the assistant.")
                break
            else:
                print("Command not recognized. Please try again.")

if __name__ == "__main__":
    main()
