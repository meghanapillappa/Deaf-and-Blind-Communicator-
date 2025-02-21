import tkinter as tk
import pyttsx3
import speech_recognition as sr
import cv2
import threading
from PIL import Image, ImageTk
import os

def speak_text(text):
    engine = pyttsx3.init()
    engine.say(text)
    engine.runAndWait()

def detect_speech_with_opencv(output_widget):
    cap = cv2.VideoCapture(0)
    face_cascade = cv2.CascadeClassifier(cv2.data.haarcascades + "haarcascade_frontalface_default.xml")
    while True:
        ret, frame = cap.read()
        if not ret:
            break
        gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
        faces = face_cascade.detectMultiScale(gray, 1.3, 5)
        if len(faces) > 0:
            recognize_speech(output_widget)
            break
    cap.release()
    cv2.destroyAllWindows()

def recognize_speech(output_widget):
    recognizer = sr.Recognizer()
    with sr.Microphone() as source:
        recognizer.adjust_for_ambient_noise(source, duration=1)  # Adjusts for background noise
        output_label.config(text="Listening...", font=("Arial", 12, "bold"), fg="red")
        try:
            audio = recognizer.listen(source, timeout=5)  # Timeout added
            text = recognizer.recognize_google(audio)
            output_label.config(text=f"Blind User Said: {text}", fg="green")
            output_widget.insert(tk.END, f"Blind: {text}\n")
        except sr.UnknownValueError:
            output_label.config(text="Could not understand audio", fg="orange")
        except sr.RequestError:
            output_label.config(text="Could not request results", fg="red")
        except sr.WaitTimeoutError:
            output_label.config(text="Listening timed out", fg="red")

def send_text():
    text = text_input.get()
    text_output.insert(tk.END, f"Deaf/Mute: {text}\n")
    speak_text(text)
    text_input.delete(0, tk.END)
    display_sign_language(text, sign_output)

def display_sign_language(text, output_widget):
    output_widget.delete("1.0", tk.END)
    for letter in text.upper():
        image_path = f"sign_language/{letter}.png"
        if os.path.exists(image_path):
            img = Image.open(image_path)
            img = img.resize((50, 50), Image.ANTIALIAS)
            img = ImageTk.PhotoImage(img)
            label = tk.Label(output_widget, image=img)
            label.image = img  # Keep reference
            label.pack(side=tk.LEFT)

def open_conversation():
    conversation_window = tk.Toplevel(root)
    conversation_window.title("Conversation Box")
    conversation_window.configure(bg="#D1C4E9")
    
    welcome_message = ("Breaking barriers, building connections!\n"
                       "Every voice deserves to be heard!\n"
                       "Together, we communicate!\n"
                       "Understanding starts with listening!\n"
                       "Words have power – use them wisely!\n")
    
    tk.Label(conversation_window, text=welcome_message, bg="#D1C4E9", fg="blue", wraplength=400, font=("Arial", 12, "bold")).pack()
    
    conversation_window.after(1000, lambda: speak_text(welcome_message))
    
    tk.Label(conversation_window, text="Deaf/Mute User: Type below and press Send", bg="#D1C4E9", fg="purple", font=("Arial", 10, "bold")).pack()
    conversation_input = tk.Entry(conversation_window, width=50)
    conversation_input.pack()
    tk.Button(conversation_window, text="Send", bg="#64B5F6", fg="white", font=("Arial", 10, "bold"), command=lambda: send_conversation_text(conversation_input, conversation_output)).pack()
    
    global output_label
    output_label = tk.Label(conversation_window, text="Blind User: Speak into the microphone", bg="#D1C4E9", fg="black", font=("Arial", 10, "bold"))
    output_label.pack()
    tk.Button(conversation_window, text="Listen", bg="#FF8A65", fg="white", font=("Arial", 10, "bold"), command=lambda: threading.Thread(target=detect_speech_with_opencv, args=(conversation_output,)).start()).pack()
    
    tk.Label(conversation_window, text="Conversation:", bg="#D1C4E9", fg="dark green", font=("Arial", 12, "bold")).pack()
    conversation_output = tk.Text(conversation_window, height=10, width=50, bg="white", fg="black", font=("Arial", 10))
    conversation_output.pack()
    
    tk.Button(conversation_window, text="Reset Conversation", bg="#F06292", fg="white", font=("Arial", 10, "bold"), command=lambda: reset_conversation(conversation_output)).pack()

def send_conversation_text(entry_widget, output_widget):
    text = entry_widget.get()
    output_widget.insert(tk.END, f"Deaf/Mute: {text}\n")
    speak_text(text)
    entry_widget.delete(0, tk.END)
    display_sign_language(text, sign_output)

def reset_conversation(output_widget):
    output_widget.delete("1.0", tk.END)

root = tk.Tk()
root.title("Communication System")
root.geometry("420x600")
root.configure(bg="#B3E5FC")

welcome_message = "Welcome to the communication system. Deaf or mute users can type, and blind users can speak.\nBeing different doesn’t mean being disconnected—welcome to a world where everyone is heard!"

tk.Label(root, text=welcome_message, bg="#B3E5FC", fg="blue", wraplength=400, font=("Arial", 12, "bold")).pack()

image_path = "picture2.jpeg"
img = Image.open(image_path)
img = img.resize((400, 200), Image.LANCZOS)
img = ImageTk.PhotoImage(img)
tk.Label(root, image=img, bg="#B3E5FC").pack()
root.image = img  # Keep a reference to avoid garbage collection

root.after(1000, lambda: speak_text(welcome_message))

sign_output = tk.Frame(root, bg="#B3E5FC")
sign_output.pack()

tk.Button(root, text="Start Conversation", bg="#7E57C2", fg="white", font=("Arial", 12, "bold"), command=open_conversation).pack(pady=20)

root.mainloop()
