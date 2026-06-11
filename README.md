# MuseTune 🎵

> Turn your feelings into music. Just write how you feel, and MuseTune creates a complete song for you.

[![Live Demo](https://img.shields.io/badge/🤗-Try%20it%20Live-yellow)](https://huggingface.co/spaces/Arpitsoni0/musetune)
[![Python](https://img.shields.io/badge/Python-3.10+-blue)]()
[![License](https://img.shields.io/badge/License-MIT-green)]()

---

## What is MuseTune?

MuseTune is an AI system that turns your journal entries into complete original songs.

You write a few sentences about how you feel. MuseTune reads your mood, picks music that fits, writes lyrics for you, and plays the whole thing back as a real song with guitar, drums, bass, and vocals.

The whole process takes less than 30 seconds.

---

## Try It Live

You can try MuseTune in your browser right now:

**https://huggingface.co/spaces/Arpitsoni0/musetune**

No signup needed. No download needed. Just type a feeling and press Generate.

---

## How It Works

MuseTune uses a 5-stage pipeline:

1. **Emotion Detection.** A RoBERTa AI model reads your text and figures out your mood. It can recognize 27 different emotions like joy, sadness, anger, love, fear, and more.

2. **ARIA Chord Retrieval.** Our custom algorithm called ARIA searches through thousands of real popular songs to find chord progressions that match your mood.

3. **Lyric Generation.** A large language model (ChatGPT) writes 18 lines of original lyrics that match your emotion and the chord progression.

4. **Audio Synthesis.** A music engine plays the song using guitar, bass, drums, and pad sounds. A neural text-to-speech model adds the vocals.

5. **Quality Scoring.** Six different metrics check the song against academic research standards.

---

## Key Result

We ran 90 experiments to test which combination works best.

The winner is **ChatGPT paired with our ARIA algorithm**, scoring **0.518** on average. This beats every other combination we tested.

| Rank | Combination | Score |
|------|-------------|-------|
| 1 | ChatGPT × ARIA | 0.518 |
| 2 | LLaMA × ARIA | 0.483 |
| 3 | ChatGPT × Theory-Based | 0.477 |

All ARIA-based combinations rank in the top 4. All LLM-only baselines rank in the bottom 4. This proves that good chord retrieval matters more than which language model you use.

---

## What Makes ARIA Special

Most AI music systems just ask a language model to write chords. ARIA is different.

ARIA looks at a curated database of 3,263 real popular songs from HookTheory. Each song has emotional features like valence (happiness) and energy (intensity) from Spotify. When you give ARIA an emotion, it finds the chord progressions that real songwriters use for that exact feeling.

The result is music that feels authentic, not generic.

---

## Six Quality Metrics

Every song MuseTune makes is scored on six things, each backed by a published research paper.

1. **Harmonic Validity.** Do the chords sound musical and correct?
2. **Chord Similarity.** Do they match the style of real popular music?
3. **Tonal Tension.** Does the song feel emotionally right?
4. **Fluency.** Are the lyrics natural and easy to read?
5. **METEOR Score.** Do the lyrics use rich, varied words?
6. **Emotional Alignment.** Do the lyrics truly capture the target mood?

---

## Project Structure

```
MuseTune/
├── README.md                Project overview (you are here)
├── LICENSE                  MIT license
├── .gitignore              Files Git should skip
└── Musetune2.ipynb         Full research notebook
```

The Hugging Face Space has the live demo code separately.

---

## How to Run It Yourself

### Option 1: Just Try the Live Demo

Go to https://huggingface.co/spaces/Arpitsoni0/musetune. It is free and works in any browser.

### Option 2: Run the Notebook

You will need a Google Colab account (free) and three API keys.

1. Open `Musetune2.ipynb` in Google Colab
2. Add these three keys to Colab Secrets (the key icon in the left sidebar):
   * `GEMINI_KEY` from https://aistudio.google.com/apikey
   * `OPENAI_KEY` from https://platform.openai.com/api-keys
   * `GROQ_KEY` from https://console.groq.com/keys
3. Run all the cells from top to bottom

---

## Research Foundation

MuseTune is built on five published academic papers and two open datasets.

| Paper | Used For |
|-------|----------|
| Demszky et al., ACL 2020 | GoEmotions taxonomy (emotion classes) |
| Cuthbert and Ariza, ISMIR 2010 | music21 library (chord parsing) |
| Raffel et al., ISMIR 2014 | mir_eval toolkit (chord similarity) |
| Herremans and Chew, TENOR 2017 | Spiral Array (tonal tension) |
| Banerjee and Lavie, ACL 2005 | METEOR score (lyric quality) |
| HookTheory | Crowd-sourced chord database |
| ChoCo (de Berardinis et al., 2023) | Annotated chord corpus |

Every metric and design choice is grounded in published research. Nothing was invented just for this project.

---

## Limitations

MuseTune is a research prototype, not a finished product. Some honest limits:

* The chord database currently has 3,263 songs, mostly Anglo-American pop. Other styles like jazz, classical, and non-Western music are not represented well.
* Emotion detection works best for English text. Other languages are not tested.
* The audio synthesis sounds good but is not studio quality. A real producer would still be better.
* All journal entries are sent to cloud APIs (OpenAI, Google, Groq). Privacy-conscious users should wait for the upcoming local-only mode.

---

## What is Next

Planned improvements include:

* Adding the ChoCo chord corpus (the code is ready, just needs to be loaded)
* A local mode that runs entirely on your computer with no cloud APIs
* Multi-language emotion detection
* User feedback loop so the system learns what you like
* A polished mobile-friendly interface

---

## Author

**Arpit Soni**
Student ID: a1897431
ICT Master Capstone Project 2
Adelaide University

Supervisor: Dr. Hussain Ahmed

---

## License

This project is released under the MIT License. You are free to use, modify, and share the code, even commercially. Just keep the copyright notice.

See the `LICENSE` file for the full text.

---

## Acknowledgements

Thank you to:

* Dr. Hussain Ahmed for supervision and feedback
* The HookTheory team for the open chord database
* The ChoCo authors for releasing their corpus to the public
* The open-source community behind RoBERTa, music21, mir_eval, and Gradio
* The University of Adelaide for the opportunity to build this project

---

## Questions?

If you find a bug or have a suggestion, please open an Issue on GitHub. I welcome feedback from anyone.
