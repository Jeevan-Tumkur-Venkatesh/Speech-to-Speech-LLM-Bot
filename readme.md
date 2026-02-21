Import Required Libraries
import os import io import base64 from gtts import gTTS import streamlit as st from groq import Groq import speech_recognition as sr os, io, base64: For handling file paths, in-memory audio processing, and encoding audio. gTTS: Converts text into speech (Google Text-to-Speech). Streamlit: Provides the interactive web app interface. Groq: Interface with the LLM (language model) for generating responses. SpeechRecognition: For recognizing speech from microphone input.

in addition to this install pip install pipwin pipwin install pyaudio

Initialize the Groq LLM Client
client = Groq(api_key="your_api_key_here") Connects to the Groq LLM API using your API key to send and receive messages.

Configure Streamlit
st.set_page_config(page_title="Speech-to-Speech LLM Bot", layout="centered") Sets up the Streamlit page with a title and layout.

Title and Description
st.title("Speech-to-Speech LLM Bot") st.write("Speak into your microphone to interact with the LLM and receive a spoken response!") Adds the title and instructions for the app.

Start Recording Button
if st.button("Start Recording"): Creates a button labeled "Start Recording." When clicked, the app begins listening to the microphone.

Speech Recognition Setup
recognizer = sr.Recognizer() Initializes a Recognizer instance from the SpeechRecognition library to process audio input.

Capture Audio from Microphone
with sr.Microphone() as source: st.info("Recording... Please wait.") audio_data = recognizer.listen(source, timeout=5) Uses Microphone() to capture live audio input. Listens to the user for 5 seconds (or until they stop speaking).

Convert Speech to Text
user_text = recognizer.recognize_google(audio_data) st.subheader("Recognized Speech:") st.write(user_text) Converts the captured audio to text using Google’s Speech Recognition API. Displays the recognized text to the user.

Send Text to the LLM
response = client.chat.completions.create( model="mixtral-8x7b-32768", messages=[ {"role": "user", "content": user_text} ] ) bot_text = response.choices[0].message.content Sends the recognized text (user_text) to the LLM model. Receives the response text (bot_text) from the model.

Display LLM Response
st.subheader("LLM Response:") st.write(bot_text) Displays the LLM-generated response text in the app.

Convert Response Text to Speech
tts = gTTS(bot_text, lang='en') audio_fp = io.BytesIO() tts.write_to_fp(audio_fp) audio_fp.seek(0) Converts the LLM response text (bot_text) into speech using gTTS. Saves the audio to an in-memory file (audio_fp).

Save Response Audio
with open("response.mp3", "wb") as audio_file: audio_file.write(audio_fp.read()) Writes the audio data to a temporary MP3 file (response.mp3).

Play the Audio Response
audio_bytes = open("response.mp3", "rb").read() st.audio(audio_bytes, format="audio/mp3") Reads the saved MP3 file and uses st.audio() to play the audio in the app.

Handle Exceptions
except sr.WaitTimeoutError: st.error("No speech detected. Please try again.") except sr.UnknownValueError: st.error("Sorry, could not recognize your speech. Please try again.") except Exception as e: st.error(f"An error occurred: {e}") Handles potential errors: WaitTimeoutError: No speech detected within the timeout period. UnknownValueError: Speech was not understood. Generic Exception: Any other unexpected issues.

Footer
st.write("---") st.caption("Powered by Groq LLM, gTTS, and SpeechRecognition.") Adds a footer with attribution. Workflow Summary User Interaction: User clicks Start Recording and speaks into their microphone. Speech Processing: Audio is captured and converted to text using Google Speech Recognition. LLM Interaction: The recognized text is sent to the Groq LLM, which generates a response. Response Conversion: The LLM response is converted to speech using gTTS. Audio Playback: The response is played back to the user in the app. Use Case

This application is useful for:

Creating real-time conversational agents. Voice-controlled assistants. Accessibility tools for the visually impaired.

