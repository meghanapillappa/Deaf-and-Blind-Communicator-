This program is a communication system for deaf/mute and blind users, built using Python Tkinter for the interface, speech recognition for blind user input, text-to-speech (pyttsx3) for audio output, and sign language images for visual aid.

🔹 Features:

Blind user: Detected via webcam (OpenCV face detection), then speaks into the microphone. Speech is recognized and displayed as text.

Deaf/mute user: Types messages in a text box. The system speaks the text aloud and displays the corresponding sign language images.

Conversation box: Interactive window where both users can communicate in real-time with speech, text, and sign language.

Reset option: Clears conversation history.

User-friendly UI: Includes images, styled labels, and motivational messages.



Tools Used:

Python – Core programming language for the entire project

Tkinter – For creating the graphical user interface (GUI)

OpenCV (cv2) – For webcam access and face detection (to identify blind user)

SpeechRecognition – To convert spoken input into text

pyttsx3 – For converting text into speech (audio output)

Pillow (PIL) – For displaying and managing sign language images

Custom Sign Language Image Dataset – For visual representation of typed text

VS Code  – For development and testing environment



👉 In short, it’s an assistive communication tool that bridges the gap between deaf/mute and blind individuals by converting speech ↔ text ↔ sign language in an accessible way.
