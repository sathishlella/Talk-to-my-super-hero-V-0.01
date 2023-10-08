# Talk-to-my-super-hero-V-0.01
Overview
"Talk to My Super Hero" is a Python project that allows you to perform speech-to-text, text-to-speech, and audio-video mixing operations. With this project, you can communicate with your favorite superheroes using speech recognition and audio manipulation.

Features
Speech to Text: Convert your spoken words into text using speech recognition.
Text to Speech: Convert your written text into spoken words and save them as audio files.
Audio-Video Mixing: Mix audio (speech) with video, allowing you to have a conversation with your chosen superhero.
Getting Started
Prerequisites
Python 3.x
Required Python libraries (install using pip): pyttsx3, speech_recognition, pydub, moviepy, and tkinter (for the GUI).


____________________________________________________________________________________________________________________________________
#Code

import tkinter as tk
from tkinter import ttk  # Import ttk for themed widgets
from tkinter import messagebox
import pyttsx3
import speech_recognition as sr
from pydub import AudioSegment
import moviepy.editor as mp
import cv2

# Function to perform speech-to-text
def speech_to_text():
    # Initialize the text-to-speech engine
    engine = pyttsx3.init()

    # Initialize the speech recognizer
    recognizer = sr.Recognizer()

    try:
        # Use the microphone as the audio source
        with sr.Microphone() as source:
            print("Say something:")
            
            # Listen for audio input
            audio = recognizer.listen(source)

        # Recognize speech using Google Web Speech API
        text = recognizer.recognize_google(audio)

        # Save the recognized text to an audio file
        audio_export_path = "input_audio.mp3"  # Adjust this as needed
        engine.save_to_file(text, audio_export_path)
        engine.runAndWait()

        return {'text': text, 'audio_file': audio_export_path}

    except sr.UnknownValueError:
        return {'text': "Sorry, I couldn't understand your speech."}
    except sr.RequestError:
        return {'text': "I'm having trouble with my speech recognition service."}

# Function to perform text-to-speech
def text_to_speech():
    text = text_entry.get("1.0", tk.END)
    output_file = "input_audio.mp3"
    engine = pyttsx3.init()
    engine.save_to_file(text, output_file)
    engine.runAndWait()
    messagebox.showinfo("Info", "Text converted to speech and saved as input_audio.mp3")




"""

# Function to play video using OpenCV
def play_video(video_path):
    video = cv2.VideoCapture(video_path)
    while True:
        ret, frame = video.read()
        if not ret:
            break
        cv2.imshow('Video', frame)
        if cv2.waitKey(1) & 0xFF == ord('q'):
            break
    video.release()
    cv2.destroyAllWindows()
"""


import cv2
import numpy as np
#ffpyplayer for playing audio
from ffpyplayer.player import MediaPlayer
video_path="output_video.mp4"
def PlayVideo(video_path):
    video=cv2.VideoCapture(video_path)
    player = MediaPlayer(video_path)
    while True:
        grabbed, frame=video.read()
        audio_frame, val = player.get_frame()
        if not grabbed:
            print("End of video")
            break
        if cv2.waitKey(28) & 0xFF == ord("q"):
            break
        cv2.imshow("Video", frame)
        if val != 'eof' and audio_frame is not None:
            #audio
            img, t = audio_frame
    video.release()
    cv2.destroyAllWindows()
#PlayVideo(video_path)







# Function to mix audio and video
def mix_audio_video():
    selected_hero = hero_var.get()
    
    if selected_hero == "Tom Cruise":
        video_file = "tom1.mp4"
    elif selected_hero == "Keanu Reeves":
        video_file = "Keanu_Reeves.mp4"
    elif selected_hero == "Morgan":
        video_file = "Morgan.mp4"
    elif selected_hero == "Willsmith":
        video_file = "Willsmith.mp4"
    else:
        messagebox.showerror("Error", "Invalid hero selection")
        return
    
    audio_file = "input_audio.mp3"
    output_file = "output_video.mp4"
    
    video_clip = mp.VideoFileClip(video_file)
    audio_clip = mp.AudioFileClip(audio_file)
    
    if audio_clip.duration < video_clip.duration:
        video_clip = video_clip.subclip(0, audio_clip.duration)
    
    video_clip = video_clip.set_audio(audio_clip)
    video_clip.write_videofile(output_file, codec="libx264")
    
    messagebox.showinfo("Info", f"Video with mixed audio saved as {output_file}")
    
    # Play the output video using OpenCV
    PlayVideo(output_file)




from moviepy.editor import VideoFileClip
def play():
    # Define the path to your video file
    video_file_path = 'output_video.mp4'  # Replace with the actual path to your video file
    
    # Load the video clip
    video_clip = VideoFileClip(video_file_path)
    
    # Play the video with audio
    video_clip.preview()
    
    # Close the video player when you're done
    video_clip.close()


# Create the main window
root = tk.Tk()
root.title("Talk to My Super Hero")

# Set window dimensions
window_width = 800  # Increase the window size for a better interface
window_height = 600
screen_width = root.winfo_screenwidth()
screen_height = root.winfo_screenheight()
x = (screen_width - window_width) // 2
y = (screen_height - window_height) // 2

root.geometry(f"{window_width}x{window_height}+{x}+{y}")

# Create and place widgets with improved design
title_label = ttk.Label(root, text="Talk to Your Super Hero", font=("Helvetica", 24, "bold"))
title_label.pack(pady=20)

speech_button = ttk.Button(root, text="Speak", command=speech_to_text)
speech_button.pack(pady=10)

text_entry_label = ttk.Label(root, text="Enter text:", font=("Helvetica", 16))
text_entry_label.pack()
text_entry = tk.Text(root, height=5, width=60, font=("Helvetica", 14))
text_entry.pack()

text_to_speech_button = ttk.Button(root, text="Save Text", command=text_to_speech)
text_to_speech_button.pack(pady=10)

hero_var = tk.StringVar()
hero_var.set("Select Your Favorite Hero")

hero_label = ttk.Label(root, text="Choose Your Hero:", font=("Helvetica", 16))
hero_label.pack()

hero_options = ["Tom Cruise", "Keanu Reeves", "Morgan","Willsmith"]
hero_menu = ttk.Combobox(root, textvariable=hero_var, values=hero_options, font=("Helvetica", 14))
hero_menu.pack()

mix_button = ttk.Button(root, text="Submit", command= mix_audio_video, style="AccentButton.TButton", padding=10)
mix_button.pack(pady=20)

# Create a custom style for the "Submit" button to make it more eye-catching
style = ttk.Style()
style.configure("AccentButton.TButton", foreground="gray", background="black", font=("Helvetica", 16))

root.mainloop()


