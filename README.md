# Transcribe
## Transcribe audio in any language to English using Whisper

# Run on Google Colab

Click the badge below to open this project in Google Colab:

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1HdNUo4pOo-vXQL3Lxg38l6R9uNuMT1F1?usp=sharing)


### Python Code Example
```python
pip install transformers torchaudio
```
Upload audio file in colab

```python
from google.colab import files

# Upload the file
uploaded = files.upload()

# Display the uploaded file(s)
for filename in uploaded.keys():
    print(f"Uploaded file: {filename}")
    file_path = filename
```
Transcribe using whisper to any language needed
# Run this cell and wait for all chunks to be transcribed. The final output will be shown after all chunks are done. 

## Whisper Large v2 Supported Languages and Codes

Below is the full list of languages supported by Whisper Large v2 with their corresponding code parameters:

| Language              | Code  | Language            | Code  |
|-----------------------|-------|---------------------|-------|
| Afrikaans             | `af`  | Albanian            | `sq`  |
| Amharic               | `am`  | Arabic              | `ar`  |
| Armenian              | `hy`  | Assamese            | `as`  |
| Azerbaijani           | `az`  | Bashkir             | `ba`  |
| Basque                | `eu`  | Belarusian          | `be`  |
| Bengali               | `bn`  | Bosnian             | `bs`  |
| Bulgarian             | `bg`  | Burmese             | `my`  |
| Catalan               | `ca`  | Chinese (Simplified)| `zh`  |
| Chinese (Traditional) | `zh-tw`| Croatian           | `hr`  |
| Czech                 | `cs`  | Danish              | `da`  |
| Dutch                 | `nl`  | English             | `en`  |
| Esperanto             | `eo`  | Estonian            | `et`  |
| Finnish               | `fi`  | French              | `fr`  |
| Galician              | `gl`  | Georgian            | `ka`  |
| German                | `de`  | Greek               | `el`  |
| Gujarati              | `gu`  | Hausa               | `ha`  |
| Hebrew                | `he`  | Hindi               | `hi`  |
| Hungarian             | `hu`  | Icelandic           | `is`  |
| Indonesian            | `id`  | Italian             | `it`  |
| Japanese              | `ja`  | Javanese            | `jv`  |
| Kannada               | `kn`  | Kazakh              | `kk`  |
| Khmer                 | `km`  | Korean              | `ko`  |
| Lao                   | `lo`  | Latvian             | `lv`  |
| Lithuanian            | `lt`  | Macedonian          | `mk`  |
| Malagasy              | `mg`  | Malay               | `ms`  |
| Malayalam             | `ml`  | Maltese             | `mt`  |
| Maori                 | `mi`  | Marathi             | `mr`  |
| Mongolian             | `mn`  | Nepali              | `ne`  |
| Norwegian             | `no`  | Oriya (Odia)        | `or`  |
| Pashto                | `ps`  | Persian (Farsi)     | `fa`  |
| Polish                | `pl`  | Portuguese          | `pt`  |
| Punjabi               | `pa`  | Romanian            | `ro`  |
| Russian               | `ru`  | Serbian             | `sr`  |
| Sinhala               | `si`  | Slovak              | `sk`  |
| Slovenian             | `sl`  | Somali              | `so`  |
| Spanish               | `es`  | Sundanese           | `su`  |
| Swahili               | `sw`  | Swedish             | `sv`  |
| Tagalog               | `tl`  | Tamil               | `ta`  |
| Tatar                 | `tt`  | Telugu              | `te`  |
| Thai                  | `th`  | Turkish             | `tr`  |
| Ukrainian             | `uk`  | Urdu                | `ur`  |
| Uzbek                 | `uz`  | Vietnamese          | `vi`  |
| Welsh                 | `cy`  | Yiddish             | `yi`  |
| Yoruba                | `yo`  | Zulu                | `zu`  |

You can use the corresponding code for each language in your Whisper configuration to set the desired transcription language.

Here’s how you can modify the code to replace the language in a specific line using GitHub Markdown:

```markdown
# How to Modify the Language in Code

Below is the Python code snippet to change the language in `forced_decoder_ids`:

```python
# Example: Setting the decoder prompt for a specific language and task
forced_decoder_ids = processor.get_decoder_prompt_ids(language="ta", task="translate")
```

### Instructions to Change the Language:
1. Locate the line in your code:
   ```python
   forced_decoder_ids = processor.get_decoder_prompt_ids(language="ta", task="translate")
   ```
2. Replace the `"ta"` (Tamil) in the `language` parameter with the desired language code. For example, to set the language to Spanish (`es`):
   ```python
   forced_decoder_ids = processor.get_decoder_prompt_ids(language="es", task="translate")
   ```


3. Run the cell to use the updated language.

```python
import torch
from transformers import WhisperProcessor, WhisperForConditionalGeneration
import torchaudio

# Load Whisper processor and large-v2 model
processor = WhisperProcessor.from_pretrained("openai/whisper-large-v2")
model = WhisperForConditionalGeneration.from_pretrained("openai/whisper-large-v2")

# Ensure the model is on the appropriate device
device = "cuda" if torch.cuda.is_available() else "cpu"
model.to(device)

# Function to preprocess audio file
def preprocess_audio(file_path, sampling_rate=16000):
    # Load the audio file
    audio, sr = torchaudio.load(file_path)
    # Resample to 16kHz if necessary
    if sr != sampling_rate:
        resampler = torchaudio.transforms.Resample(orig_freq=sr, new_freq=sampling_rate)
        audio = resampler(audio)
    return audio.squeeze(0)  # Remove channel dimension if stereo

# Function to split long audio into chunks
def split_audio(audio, chunk_duration, sampling_rate):
    num_samples_per_chunk = chunk_duration * sampling_rate
    return [audio[i:i + num_samples_per_chunk] for i in range(0, len(audio), num_samples_per_chunk)]

 # Replace with the actual file path

# Preprocess the audio
audio_tensor = preprocess_audio(file_path)

# Split the audio into smaller chunks (30 seconds each)
chunk_duration = 30  # Duration of each chunk in seconds
chunks = split_audio(audio_tensor, chunk_duration, sampling_rate=16000)

# Transcribe each chunk and concatenate results
transcriptions = []
for i, chunk in enumerate(chunks):
    input_features = processor(chunk, sampling_rate=16000, return_tensors="pt").input_features.to(device)
    forced_decoder_ids = processor.get_decoder_prompt_ids(language="ta", task="translate") #change language here
    generated_ids = model.generate(
        input_features,
        forced_decoder_ids=forced_decoder_ids,
        max_new_tokens=444,  # Allow enough tokens for longer transcription
    )
    transcription = processor.batch_decode(generated_ids, skip_special_tokens=True)[0]
    transcriptions.append(transcription)
    print(f"Chunk {i + 1}/{len(chunks)} transcribed.")

# Combine all chunks into the final transcription
full_transcription = " ".join(transcriptions)
print("\nFull Transcription:\n", full_transcription)


