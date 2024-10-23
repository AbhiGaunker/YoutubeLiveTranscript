import streamlink
import subprocess
import speech_recognition as sr
import threading
import queue
import time
import os
from datetime import datetime
import numpy as np
import wave
import pyaudio

# Constants
CHUNK_DURATION = 10  # Duration in seconds for each audio chunk
SAMPLE_RATE = 16000
CHANNELS = 1
CHUNK_SIZE = 1024

class AudioTranscriber:
    def __init__(self):
        self.recognizer = sr.Recognizer()
        self.audio_queue = queue.Queue()
        self.text_queue = queue.Queue()
        self.is_running = False
        self.output_file = f"transcriptions/transcript_{datetime.now().strftime('%Y%m%d_%H%M%S')}.txt"
        os.makedirs("transcriptions", exist_ok=True)

        # Initialize output file
        with open(self.output_file, 'w', encoding='utf-8') as f:
            f.write(f"=== Transcription started at {datetime.now()} ===\n\n")

    def get_stream_url(self, youtube_url):
        """Get the audio stream URL from YouTube"""
        try:
            streams = streamlink.streams(youtube_url)
            if 'audio_only' in streams:
                return streams['audio_only'].url
            elif 'worst' in streams:
                return streams['worst'].url
            else:
                return streams['best'].url
        except Exception as e:
            print(f"Error getting stream URL: {e}")
            return None

    def save_audio_chunk(self, audio_data, filename):
        """Save audio data to WAV file"""
        with wave.open(filename, 'wb') as wf:
            wf.setnchannels(CHANNELS)
            wf.setsampwidth(2)  # 2 bytes per sample
            wf.setframerate(SAMPLE_RATE)
            wf.writeframes(audio_data)

    def transcribe_audio(self, audio_file):
        """Transcribe audio file using Google Speech Recognition"""
        try:
            with sr.AudioFile(audio_file) as source:
                audio = self.recognizer.record(source)
                text = self.recognizer.recognize_google(audio)
                return text
        except sr.UnknownValueError:
            return None
        except Exception as e:
            print(f"Transcription error: {e}")
            return None

    def process_audio_chunks(self):
        """Process audio chunks from the queue"""
        while self.is_running:
            try:
                audio_data = self.audio_queue.get(timeout=1)
                temp_filename = f"temp_audio_{time.time()}.wav"

                # Save audio chunk
                self.save_audio_chunk(audio_data, temp_filename)

                # Transcribe
                text = self.transcribe_audio(temp_filename)

                # Clean up
                os.remove(temp_filename)

                if text:
                    timestamp = datetime.now().strftime('%H:%M:%S')
                    full_text = f"[{timestamp}] {text}"
                    print(full_text)

                    # Save to file
                    with open(self.output_file, 'a', encoding='utf-8') as f:
                        f.write(full_text + '\n')

            except queue.Empty:
                continue
            except Exception as e:
                print(f"Error processing audio: {e}")

    def stream_audio(self, youtube_url):
        """Stream audio from YouTube URL"""
        stream_url = self.get_stream_url(youtube_url)
        if not stream_url:
            print("Failed to get stream URL")
            return

        command = [
            'ffmpeg',
            '-i', stream_url,
            '-acodec', 'pcm_s16le',
            '-ar', str(SAMPLE_RATE),
            '-ac', str(CHANNELS),
            '-f', 'wav',
            '-'
        ]

        try:
            process = subprocess.Popen(
                command,
                stdout=subprocess.PIPE,
                stderr=subprocess.DEVNULL
            )

            audio_buffer = bytearray()
            samples_per_chunk = SAMPLE_RATE * CHUNK_DURATION

            while self.is_running:
                data = process.stdout.read(CHUNK_SIZE)
                if not data:
                    break

                audio_buffer.extend(data)

                # Check if we have enough samples for our chunk duration
                if len(audio_buffer) >= samples_per_chunk * 2:  # * 2 because 16-bit samples
                    self.audio_queue.put(bytes(audio_buffer[:samples_per_chunk * 2]))
                    audio_buffer = audio_buffer[samples_per_chunk * 2:]

        except Exception as e:
            print(f"Streaming error: {e}")
        finally:
            process.kill()

    def start(self, youtube_url):
        """Start the transcription process"""
        self.is_running = True

        # Start processing thread
        processing_thread = threading.Thread(
            target=self.process_audio_chunks
        )
        processing_thread.start()

        # Start streaming thread
        streaming_thread = threading.Thread(
            target=self.stream_audio,
            args=(youtube_url,)
        )
        streaming_thread.start()

        print(f"Transcription started. Saving to: {self.output_file}")
        print("Press Ctrl+C to stop...")

        try:
            while True:
                time.sleep(0.1)
        except KeyboardInterrupt:
            print("\nStopping transcription...")
            self.is_running = False
            processing_thread.join()
            streaming_thread.join()
            print(f"Transcription saved to: {self.output_file}")

if __name__ == "__main__":
    # Replace with your YouTube live stream URL
    YOUTUBE_URL = "https://www.youtube.com/watch?v=gEeXjQ0WCIQ"

    transcriber = AudioTranscriber()
    transcriber.start(YOUTUBE_URL)