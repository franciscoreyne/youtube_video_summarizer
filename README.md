# **YouTube Video Summarizer (Python)**

This script extracts subtitles from a YouTube video and summarizes them locally using a pre-trained model from Hugging Face (BART). The summary is capped at 4000 characters.

<p align="center">
  
<img src="Summarizer_Youtube3.png" alt="Youtube Summarizer" width="400"/>

</p>


This version of the code will work locally to extract subtitles from YouTube videos, summarize them using a model from Hugging Face, and return a summary.

### **Dependencies**

Before running the code, you need to install the following dependencies:

```bash
pip install youtube-transcript-api transformers torch
```

### **Instructions**

1. **Clone the repository** or download the script.
2. **Install dependencies** using the command above.
3. **Run the script** with the URL of the YouTube video you want to summarize.

---

### **Code:**

```python
from youtube_transcript_api import YouTubeTranscriptApi
from transformers import pipeline

# Load the Hugging Face summarization model
summarizer = pipeline("summarization", model="facebook/bart-large-cnn")

def get_video_subtitles(video_id):
    """
    Fetches subtitles for a YouTube video using the YouTube Transcript API.
    :param video_id: The ID of the YouTube video.
    :return: Full transcript text from the subtitles.
    """
    try:
        # Get the transcript (subtitles) for the video by its ID
        transcript = YouTubeTranscriptApi.get_transcript(video_id)
        
        # Join all subtitle parts into a single text block
        full_text = " ".join([entry['text'] for entry in transcript])
        
        return full_text
    except Exception as e:
        print(f"Error fetching subtitles: {e}")
        return None

def summarize_text(text, max_length=4000):
    """
    Summarizes the input text using a pre-trained Hugging Face model.
    :param text: The full text to be summarized.
    :param max_length: The maximum number of characters in the summary (default 4000).
    :return: The summarized text.
    """
    # Ensure the text does not exceed the max length
    text = text[:max_length]
    
    # Generate the summary using the Hugging Face summarizer model
    summary = summarizer(text, max_length=400, min_length=50, do_sample=False)
    
    return summary[0]['summary_text']

def summarize_youtube_video(video_url, max_length=4000):
    """
    Extracts subtitles from a YouTube video and summarizes them.
    :param video_url: The URL of the YouTube video.
    :param max_length: The maximum number of characters in the summary (default 4000).
    :return: The summarized text of the video.
    """
    # Extract the video ID from the URL
    video_id = video_url.split("v=")[-1]
    
    # Fetch subtitles for the video
    subtitles = get_video_subtitles(video_id)
    
    if subtitles:
        # Summarize the subtitles using Hugging Face
        summary = summarize_text(subtitles, max_length)
        return summary
    else:
        return "Could not fetch subtitles for the video."

# Example usage:
if __name__ == "__main__":
    video_url = "https://www.youtube.com/watch?v=dQw4w9WgXcQ"  # Replace with your YouTube video URL
    summary = summarize_youtube_video(video_url)
    print("Video Summary:\n", summary)
```

---

### **Explanation of the Code:**

1. **Get Subtitles from YouTube:**
   The `get_video_subtitles` function fetches the subtitles for a given YouTube video using the `youtube_transcript_api` library. The video ID is extracted from the URL of the video.

2. **Summarize the Subtitles:**
   The `summarize_text` function uses the **Hugging Face Transformers library** with a pre-trained model (`facebook/bart-large-cnn`) to summarize the subtitles. The `max_length` parameter ensures the text is within a reasonable limit, and it generates a concise summary of the video.

3. **Main Function:**
   The `summarize_youtube_video` function ties everything together. It accepts the video URL, extracts subtitles, and returns the summarized text. If the subtitles can't be fetched, it returns an error message.

### **How to Use:**

1. **Clone or download the repository** containing this script.
2. **Install dependencies**:

    ```bash
    pip install youtube-transcript-api transformers torch
    ```

3. **Run the script**:

    ```bash
    python youtube_video_summarizer.py
    ```

    Replace the `video_url` with any YouTube video URL you want to summarize. The script will print the summary.

### **Example Output:**

```text
Video Summary:
 This is a detailed explanation of the topic discussed in the video. The video covers the following key points: (List of main points)...
```

---

### **Optimization and Improvements:**

- **Error Handling**: The code already handles basic errors, such as when subtitles cannot be fetched.
- **Performance**: Hugging Face models like `facebook/bart-large-cnn` are optimized for text summarization tasks, but if you deal with longer subtitles, you might need to split the text into smaller chunks and summarize them sequentially.
  
  If you want to further optimize for larger videos, you can split the subtitles into chunks (e.g., 500-1000 characters) and summarize each chunk separately.

### **Notes:**

- **Subtitles Availability**: Not all YouTube videos have subtitles available. If the video doesn't have them, the script will return an error message.
- **Model Speed**: The summarization model can be slow for very long texts. Using a GPU (if available) will significantly speed up the process.

---
