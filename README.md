# 🎵 MuseTune
> Transform your feelings into music using AI

![Python](https://img.shields.io/badge/Python-3.x-blue)
![HuggingFace](https://img.shields.io/badge/HuggingFace-Transformers-yellow)
![License](https://img.shields.io/badge/License-MIT-green)

## 🧠 What is MuseTune?
MuseTune is an end-to-end AI pipeline that transforms personal journal entries into complete songs. Write how you feel   MuseTune detects your emotion, finds matching chord progressions, and generates lyrics using Large Language Models.

## 🔄 How It Works
1. 📝 **Input**    User writes a journal entry
2. 🎭 **Emotion Detection**    RoBERTa transformer classifies emotion (28 classes)
3. 🎼 **Chord Retrieval**    Queries 7,200+ song database for matching progressions
4. ✍️ **Lyric Generation**    Multiple LLMs generate chord-conditioned lyrics
5. 📊 **Evaluation**    6 automated metrics score the output

## 🛠️ Tech Stack
- **Emotion Detection:** RoBERTa (GoEmotions, 28 classes)
- **LLMs:** Google Gemini 2.5 Flash, OpenAI ChatGPT, Meta LLaMA
- **Music Analysis:** Music21, mir_eval, Spiral Array Model
- **Data:** HookTheory + ChoCo (7,200+ songs)
- **Evaluation:** METEOR, GPT-2 Perplexity, MIREX, Tonal Tension
- **Interface:** Gradio
- **Libraries:** HuggingFace Transformers, Pandas

## 📏 Evaluation Metrics
| Metric | Type | Paper |
|--------|------|-------|
| Harmonic Validity | Chord | Music21 |
| Chord Similarity | Chord | MIREX (Raffel 2014) |
| Tonal Tension | Chord | Herremans & Chew 2017 |
| Fluency | Lyric | GPT-2 Perplexity |
| Quality | Lyric | METEOR (2005) |
| Emotional Alignment | Lyric | RoBERTa loop-back |

## 🔬 Research Design
3×3 experimental matrix comparing:
- **3 LLMs:** Gemini · ChatGPT · LLaMA
- **3 chord methods:** Database query · LLM-selected · Rule-based

## 👨‍💻 Author
**Arpit Soni**    Final Year CS Student, Adelaide University
[LinkedIn](https://www.linkedin.com/in/arpitsoni00007)
