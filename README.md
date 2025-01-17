from gtts import gTTS
import os
import subprocess
import platform
import speech_recognition as sr

# Function to play audio file based on OS
def play_audio(filename):
    if platform.system() == "Windows":
        os.system(f'start {filename}')
    elif platform.system() == "Linux":
        subprocess.call(["xdg-open", filename])
    elif platform.system() == "Darwin":  # macOS
        subprocess.call(["open", filename])

# Text-to-Speech Function
def text_to_speech(text, lang):
    try:
        tts = gTTS(text=text, lang=lang, slow=False)
        filename = "output.mp3"
        tts.save(filename)
        play_audio(filename)
    except Exception as e:
        print(f"Error in Text-to-Speech: {e}")

# Speech-to-Text Function
def speech_to_text(lang):
    recognizer = sr.Recognizer()
    
    with sr.Microphone() as source:
        # Adjust the recognizer sensitivity to ambient noise
        recognizer.adjust_for_ambient_noise(source)
        
        print(f"Listening... Speak in {lang}")
        try:
            audio = recognizer.listen(source, timeout=5)  # Added timeout to avoid hanging
            print("Processing...")
            text = recognizer.recognize_google(audio, language=lang)
            print(f"Recognized text: {text}")
            return text
        except sr.UnknownValueError:
            print("Sorry, could not understand the audio.")
        except sr.RequestError as e:
            print(f"Request error: {e}")
        except Exception as ex:
            print(f"Unexpected error: {ex}")


# Menu for Indian Languages
def indian_language_menu():
    lang_codes = {
        "Hindi": "hi",
        "Punjabi": "pa",
        "Bengali": "bn",
        "Gujarati": "gu",
        "Tamil": "ta",
        "Telugu": "te",
        "Malayalam": "ml",
        "Kannada": "kn",
        "Marathi": "mr",
        "Urdu": "ur"
    }

    print("\nAvailable Languages:")
    for idx, (lang, code) in enumerate(lang_codes.items(), start=1):
        print(f"{idx}. {lang}")

    choice = int(input("Choose a language by number: "))
    lang_name = list(lang_codes.keys())[choice - 1]
    lang_code = lang_codes[lang_name]
    return lang_name, lang_code

# Main Menu
def main():
    while True:
        print("\nMenu:")
        print("1. Text-to-Speech")
        print("2. Speech-to-Text")
        print("3. Exit")
        choice = input("Enter your choice: ")

        if choice == '1':
            lang_name, lang_code = indian_language_menu()
            text = input(f"Enter text to convert to speech in {lang_name}: ")
            text_to_speech(text, lang=lang_code)
        elif choice == '2':
            lang_name, lang_code = indian_language_menu()
            speech_to_text(lang=f"{lang_code}-IN")
        elif choice == '3':
            print("Exiting program.")
            break
        else:
            print("Invalid choice. Please try again.")

if _name_ == "_main_":
    main()
