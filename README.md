# YouTube-Video-Analyzer-
Analysis 
import os
from pytube import YouTube
import whisper
from transformers import pipeline

def baixar_audio(video_url, filename="audio.mp4"):
    yt = YouTube(video_url)
    audio_stream = yt.streams.filter(only_audio=True).first()
    audio_stream.download(filename=filename)
    return filename

def transcrever_audio(audio_file):
    model = whisper.load_model("base")  # Use "small" ou "medium" para mais precisão
    resultado = model.transcribe(audio_file)
    return resultado['text']

def resumir_texto(texto):
    summarizer = pipeline("summarization", model="facebook/bart-large-cnn")
    partes = [texto[i:i+1000] for i in range(0, len(texto), 1000)]
    resumo = ''
    for parte in partes:
        resumo += summarizer(parte, max_length=130, min_length=30, do_sample=False)[0]['summary_text'] + ' '
    return resumo.strip()

def analisar_video_youtube(url):
    print("Baixando áudio...")
    audio_file = baixar_audio(url)

    print("Transcrevendo vídeo...")
    texto = transcrever_audio(audio_file)

    print("Resumindo conteúdo...")
    resumo = resumir_texto(texto)

    os.remove(audio_file)  # Limpeza
    return resumo

# Exemplo de uso:
url_do_video = "https://www.youtube.com/watch?v=ID_DO_VIDEO"
resumo = analisar_video_youtube(url_do_video)
print("Resumo do vídeo:\n", resumo)
