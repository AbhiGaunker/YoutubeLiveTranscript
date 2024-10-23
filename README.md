# YouTube Live Stream Transcriber

A Python tool that provides real-time transcription of YouTube live streams. This tool supports both Google Speech Recognition and Whisper for transcription, with options to save transcripts to files and display them in real-time.

## Features

- Real-time audio streaming from YouTube live streams
- Support for multiple transcription services (Google Speech Recognition and Whisper)
- Automatic saving of transcripts with timestamps
- Low-latency transcription (10-15 seconds delay)
- Console output for real-time monitoring
- Error handling and recovery
- Organized transcript storage

## Prerequisites

- Python 3.8 or higher
- FFmpeg installed on your system
- Required Python packages (see `requirements.txt`)

## Installation

1. Clone the repository:
```bash
git clone https://github.com/yourusername/youtube-live-transcriber.git
cd youtube-live-transcriber
```

2. Create and activate a virtual environment (recommended):
```bash
# Windows
python -m venv venv
venv\Scripts\activate

# Linux/MacOS
python3 -m venv venv
source venv/bin/activate
```

3. Install the required packages:
```bash
pip install -r requirements.txt
```

4. Install FFmpeg:
- **Windows**: Download from [FFmpeg website](https://ffmpeg.org/download.html)
- **Linux**: `sudo apt-get install ffmpeg`
- **MacOS**: `brew install ffmpeg`

## Usage

1. Basic usage:
```python
python transcriber.py
```

2. Modify the YouTube URL in the script or pass it as an argument:
```python
python transcriber.py --url "https://www.youtube.com/watch?v=your_video_id"
```

3. Choose transcription method:
```python
python transcriber.py --method whisper  # or google (default)
```

## Configuration

You can modify the following constants in the script:
- `CHUNK_DURATION`: Duration of audio chunks (default: 10 seconds)
- `SAMPLE_RATE`: Audio sample rate (default: 16000)
- `CHANNELS`: Number of audio channels (default: 1)

## Project Structure

```
youtube-live-transcriber/
├── transcriber.py           # Main script
├── requirements.txt         # Python dependencies
├── README.md               # This file
├── .gitignore             # Git ignore file
└── transcriptions/         # Output directory for transcripts
    └── transcript_*.txt    # Generated transcript files
```



## Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## Common Issues and Solutions

1. **FFmpeg not found**
   - Ensure FFmpeg is installed and added to your system PATH
   - Check installation with `ffmpeg -version`

2. **PyAudio installation issues**
   - Windows: Use pip to install the appropriate wheel
   - Linux: Install portaudio development package first:
     ```bash
     sudo apt-get install python3-pyaudio
     ```

3. **Stream connection errors**
   - Check your internet connection
   - Verify the YouTube URL is valid and the stream is live
   - Try increasing the chunk duration

