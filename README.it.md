# Trascrittore Audio WhatsApp

Trascrive in testo i vocali di WhatsApp direttamente sul tuo PC, **offline**, usando [OpenAI Whisper](https://github.com/openai/whisper). L'audio non esce dal tuo computer.

🇬🇧 English version: [README.md](README.md)

## Requisiti

- Windows (le istruzioni usano PowerShell; su macOS/Linux i comandi sono analoghi)
- [Python 3.9 – 3.13](https://www.python.org/downloads/)
- [FFmpeg](https://ffmpeg.org/) (Whisper lo usa per leggere i file audio)
- Circa 1–2 GB di spazio libero per PyTorch e il modello

## Installazione

### 1. Clona il progetto

```powershell
git clone https://github.com/youssouf994/simply-STT-whisper-for-whatsapp.git
cd simply-STT-whisper-for-whatsapp
```

### 2. Installa FFmpeg

```powershell
winget install Gyan.FFmpeg
```

**Chiudi e riapri il terminale** (il PATH si aggiorna solo nelle nuove sessioni), poi verifica:

```powershell
ffmpeg -version
```

Alternative: `choco install ffmpeg`, oppure installazione manuale da [gyan.dev](https://www.gyan.dev/ffmpeg/builds/) aggiungendo la cartella `bin` al PATH.

### 3. Crea e attiva l'ambiente virtuale

```powershell
python -m venv venv
Set-ExecutionPolicy -Scope Process -ExecutionPolicy RemoteSigned
.\venv\Scripts\Activate.ps1
```

Vedrai `(venv)` all'inizio della riga del terminale. Se PowerShell blocca lo script, il comando `Set-ExecutionPolicy` qui sopra vale solo per la sessione corrente e risolve il problema.

### 4. Installa le dipendenze

```powershell
pip install -r requirements.txt
```

## Utilizzo

### 1. Scarica il vocale da WhatsApp

Su WhatsApp Web/Desktop: menu del messaggio vocale (freccia a destra) → **Scarica**. Otterrai un file `.ogg` o `.opus`.

Metti i file nella cartella `audio/` del progetto (o usa un percorso qualsiasi).

### 2. Trascrivi

Con il venv attivo:

```powershell
whisper audio\1.ogg --language it --model small
```

Il testo appare nel terminale e vengono creati nella cartella corrente i file `1.txt`, `1.srt`, `1.vtt`, `1.tsv` e `1.json`.

Alla prima esecuzione il modello viene scaricato automaticamente (`small` ≈ 461 MB).

### Solo il testo, in una cartella a scelta

```powershell
whisper audio\1.ogg --language it --model small --output_format txt --output_dir trascrizioni
```

### Più file in una volta

```powershell
whisper audio\1.ogg audio\2.ogg audio\3.ogg --language it --model small --output_format txt
```

### File con spazi nel nome o in un'altra cartella

Usa le virgolette:

```powershell
whisper "C:\Users\tuo-utente\Downloads\WhatsApp Ptt 2026-11-01 at 10.30.15.ogg" --language it --model small
```

## Scegliere il modello

| Modello  | Velocità | Precisione | Note                             |
|----------|----------|------------|----------------------------------|
| `tiny`   | ★★★★★    | ★          | Solo per prove rapide            |
| `base`   | ★★★★     | ★★         |                                  |
| `small`  | ★★★      | ★★★        | Buon compromesso per l'italiano  |
| `medium` | ★★       | ★★★★       | Più preciso, più lento           |
| `large`  | ★        | ★★★★★      | Massima qualità, serve molta RAM |

Cambia il modello con `--model medium` (o altro).

## Risoluzione dei problemi

| Problema | Soluzione |
|----------|-----------|
| `ffmpeg` / `whisper` non riconosciuto | Riapri il terminale. Per `whisper` riattiva il venv: `.\venv\Scripts\Activate.ps1` |
| `Error opening input file ... No such file or directory` | Il file non esiste nel percorso indicato: controlla il nome reale (`dir audio`) o usa il percorso completo |
| `FP16 is not supported on CPU; using FP32 instead` | Solo un avviso, innocuo: su CPU Whisper usa FP32 |
| Esecuzione script bloccata in PowerShell | `Set-ExecutionPolicy -Scope Process -ExecutionPolicy RemoteSigned` |
| Errori di installazione con Python troppo recente | Usa Python 3.11 o 3.12 |
| Trascrizione imprecisa | Prova `--model medium` e specifica sempre `--language it` |

## Note

- Tutto avviene in locale: ideale per messaggi riservati.
- Su CPU i modelli grandi sono più lenti della durata dell'audio; con una GPU NVIDIA (PyTorch con CUDA) è molto più veloce.
