from fastapi import FastAPI
import yt_dlp
import uuid
import os

app = FastAPI()

DOWNLOADS = "downloads"
os.makedirs(DOWNLOADS, exist_ok=True)

@app.get("/")
def home():
    return {"status": "API WORKING"}

@app.get("/download")
def download(url: str):
    file_id = str(uuid.uuid4())
    output = f"{DOWNLOADS}/{file_id}.%(ext)s"

    ydl_opts = {
        "outtmpl": output,
        "format": "best",
        "quiet": True
    }

    with yt_dlp.YoutubeDL(ydl_opts) as ydl:
        info = ydl.extract_info(url, download=True)
        filename = ydl.prepare_filename(info)

    return {"file": filename}
