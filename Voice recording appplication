from tkinter import *
from tkinter import messagebox
import sounddevice as sound
from scipy.io.wavfile import write
import threading
import time
 
 
class VoiceRecorder:
   def __init__(self, master):
       self.master = master
       master.title("Voice Recorder - The Pycodes")
       master.geometry("600x500")
       master.configure(bg="black")
       master.resizable(False, False)
 
 
       self.recording = None
       self.freq = 44100
       self.stop_recording_flag = False  # Variable to signal the countdown to stop
       self.countdown_thread = None  # Variable to store the countdown thread
 
 
       self.label = Label(master, text="Voice Recorder", font="arial 30 bold", bg="black", fg="red")
       self.label.pack()
 
 
       self.duration_label = Label(master, text="Enter recording time in seconds:", font="arial 15", bg="black", fg="red")
       self.duration_label.pack()
 
 
       self.duration_var = StringVar()
       self.entry = Entry(master, textvariable=self.duration_var, font="arial 30", width=15)
       self.entry.pack(pady=10)
 
 
       self.start_button = Button(master, font="arial 20", text="Start Recording", bg="green", fg="white", bd=0, command=self.start_recording)
       self.start_button.pack(pady=10)
 
 
       self.stop_button = Button(master, font="arial 20", text="Stop Recording", bg="red", fg="white", bd=0, command=self.stop_recording, state=DISABLED)
       self.stop_button.pack(pady=10)
 
 
       self.save_button = Button(master, font="arial 20", text="Save Recording", bg="blue", fg="white", bd=0, command=self.save_recording, state=DISABLED)
       self.save_button.pack(pady=10)
 
 
       self.counter_label = Label(master, text="", font="arial 40", width=4, background="black", fg="white")
       self.counter_label.pack()
 
 
   def start_recording(self):
       try:
           recording_time = int(self.duration_var.get())
       except ValueError:
           messagebox.showerror("Error", "Please enter a valid duration.")
           return
 
 
       self.recording = sound.rec(recording_time * self.freq, samplerate=self.freq, channels=2)
       self.stop_recording_flag = False  # Reset the flag
       self.countdown_thread = threading.Thread(target=self.update_counter, args=(recording_time,))
       self.countdown_thread.start()
 
 
       self.start_button.config(state=DISABLED)
       self.stop_button.config(state=NORMAL)
       self.save_button.config(state=DISABLED)
 
 
   def stop_recording(self):
       sound.stop()
       self.stop_recording_flag = True  # Signal the countdown to stop
 
 
       # Wait for the countdown thread to complete before proceeding
       threading.Thread(target=self.wait_for_countdown).start()
 
 
   def wait_for_countdown(self):
       # This function is used to join the countdown thread
       self.countdown_thread.join()
 
 
       # Update button states after the countdown thread completes
       self.master.after(0, self.update_button_states)
 
 
       # Trim the recorded data based on the actual recording time
       if self.recording is not None:
           self.recording = self.recording[:self.get_actual_recorded_frames()]
 
 
       # Save the recording automatically when the countdown finishes
       if not self.stop_recording_flag:
           self.master.after(0, self.save_recording)
 
 
   def update_button_states(self):
       # Update button states after the countdown thread completes
       self.start_button.config(state=NORMAL)
       self.stop_button.config(state=DISABLED)
       self.save_button.config(state=NORMAL)
 
 
   def update_counter(self, recording_time):
       while recording_time > 0 and not self.stop_recording_flag:
           self.master.update()
           time.sleep(1)
           recording_time -= 1
 
 
           if recording_time == 0:
               messagebox.showinfo("Time Countdown", "Time's up")
               self.master.after(0, self.save_recording)  # Save the recording automatically when the countdown finishes
           self.counter_label.config(text=str(recording_time))
 
 
       sound.wait()
 
 
   def get_actual_recorded_frames(self):
       # Calculate the actual number of frames recorded based on the time elapsed
       if self.recording is not None:
           elapsed_time = int(self.duration_var.get()) - int(self.counter_label.cget("text"))
           actual_frames = int(elapsed_time * self.freq)
           return min(actual_frames, len(self.recording))
       return 0
 
 
   def save_recording(self):
       try:
           write("voice-recording.wav", self.freq, self.recording)
           messagebox.showinfo("Save", "Recording saved successfully!")
       except Exception as e:
           messagebox.showerror("Error", f"An error occurred while saving the recording: {e}")
 
 
if __name__ == "__main__":
   root = Tk()
   app = VoiceRecorder(root)
   root.mainloop()
