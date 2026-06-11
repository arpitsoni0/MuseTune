# MuseTune Design Document

| Field | Detail |
|-------|--------|
| Project | MuseTune: AI Songwriting System |
| Author | Arpit Soni (a1897431) |
| Course | ICT Master Capstone Project 2 |
| University | University of Adelaide |
| Supervisor | Dr. Hussain Ahmed |
| Version | 1.0 |
| Last Updated | June 2026 |

---

## 1. Project Overview

MuseTune is a system that turns journal entries into songs. The user writes how they feel. Then the system makes a song with chords, lyrics, and audio. The full process takes less than 30 seconds.

**Live Product:** https://huggingface.co/spaces/Arpitsoni0/musetune  
**Source Code:** https://github.com/arpitsoni0/MuseTune

**Best Result:** The best combination is ChatGPT with our ARIA algorithm. It got a score of 0.518 out of 90 experiments. This is better than all other methods we tested.

---

## 2. System Architecture

MuseTune has five stages. Each stage does one job and passes data to the next stage.

### 2.1 Pipeline Diagram
┌─────────────────────────────────────────────────────────────┐
│ USER JOURNAL ENTRY │
└──────────────────────────┬──────────────────────────────────┘
▼
┌─────────────────────────────────────────────────────────────┐
│ STAGE 1: EMOTION DETECTION │
│ RoBERTa model finds 27 different emotions │
└──────────────────────────┬──────────────────────────────────┘
▼
┌─────────────────────────────────────────────────────────────┐
│ STAGE 2: ARIA CHORD RETRIEVAL │
│ Our algorithm searches 3,263 real songs │
└──────────────────────────┬──────────────────────────────────┘
▼
┌─────────────────────────────────────────────────────────────┐
│ STAGE 3: LYRIC GENERATION │
│ ChatGPT writes 18 lines of song lyrics │
└──────────────────────────┬──────────────────────────────────┘
▼
┌─────────────────────────────────────────────────────────────┐
│ STAGE 4: AUDIO SYNTHESIS │
│ Guitar, bass, drums, pad, and vocals are added │
└──────────────────────────┬──────────────────────────────────┘
▼
┌─────────────────────────────────────────────────────────────┐
│ STAGE 5: QUALITY EVALUATION │
│ Six metrics score the final song │
└──────────────────────────┬──────────────────────────────────┘
▼
┌─────────────────────────────────────────────────────────────┐
│ FINAL SONG + LYRICS + SCORECARD │
└─────────────────────────────────────────────────────────────┘

### 2.2 Technology Stack
| Layer | Technology |
|-------|-----------|
| Frontend | Gradio 4.x |
| Emotion Detection | RoBERTa [6] fine-tuned on GoEmotions [1] |
| Chord Database | HookTheory corpus [9] (3,263 songs) |
| Lyric Generator | OpenAI gpt-4o-mini |
| Audio Engine | Karplus-Strong synthesis, Edge TTS Jenny voice |
| Evaluation | music21 [2], mir_eval [3], GPT-2 [7], NLTK METEOR [5] |
| Deployment | Hugging Face Spaces (CPU tier) |
| Version Control | Git / GitHub |
---
## 3. Core Workflows
### 3.1 Live Demo Workflow
This is how a user gets a song:
1. User opens the Gradio website and writes a journal entry
2. The system runs RoBERTa [6] to find the main emotion
3. ARIA searches the chord database and returns the best chord progression
4. ChatGPT gets the emotion and chords, then writes 18 lines of lyrics
5. The audio engine makes a full song with vocals
6. Six metrics score the song
7. The user sees the song, lyrics, and a radar chart of scores
**Average time:** 28 seconds from start to end.
### 3.2 Research Evaluation Workflow
This is how we tested the system:
1. We made 10 journal entries, one for each target emotion
2. Each entry was tested with all 9 combinations (3 chord methods x 3 LLMs)
3. Every output was scored on all 6 quality metrics
4. Results were saved in a CSV file called `musetune_full_results.csv`
5. Six figures were made to show the results clearly
6. Statistics confirmed which combination won
**Total experiments:** 90  
**Cost:** Less than $1 in API fees
---
## 4. Key Design Decisions
This section explains why we made the choices we made.
### 4.1 Why RoBERTa for Emotion Detection?
RoBERTa [6] is trained on the GoEmotions dataset [1]. It can find 27 different emotions. Normal sentiment models only say positive or negative. That is not enough for music. Music needs to know the difference between sadness and grief, or joy and excitement. So RoBERTa is better for this job.
### 4.2 Why HookTheory as the Source Corpus?
HookTheory [9] has three things that other datasets do not have together:
- Real chord progressions made by music fans
- Spotify audio features for each song (valence, energy, mode)
- Artist and song names for attribution
These emotion labels are what make ARIA work. Without them, we cannot match emotions to chords.
### 4.3 Why a 3x3 Test Design?
The 3x3 design helps us check two things at the same time. The chord method and the language model. By testing all 9 combinations with 10 emotions, we get 90 results. This is enough to do proper statistics. But not so many that the API costs get too high.
### 4.4 Why These Six Specific Metrics?
Each metric comes from a published research paper. This means every score we report is backed by real science.
| Metric | Reference |
|--------|--------|
| Harmonic Validity | [2] |
| Chord Similarity | [3] |
| Tonal Tension | [4] |
| Fluency | [7] |
| METEOR | [5] |
| Emotional Alignment | [1] |
### 4.5 Why Karplus-Strong Synthesis for Guitar?
Karplus-Strong is an old algorithm from 1983 that makes guitar sounds. It is good because it runs fast on cheap computers. The free Hugging Face server has limited power, so heavy synthesis would be too slow. Karplus-Strong gives realistic guitar with very low CPU usage.
### 4.6 Why ChatGPT and ARIA as the Default Pipeline?
We did not just guess. We tested 9 combinations across 90 experiments. ChatGPT with ARIA got the best score of 0.518. ChatGPT is good at writing natural English and rich vocabulary. ARIA is good at picking chords that match the emotion. Together they work the best.
### 4.7 Why Cloud APIs Instead of Local Models?
The free Hugging Face server cannot run big language models without being very slow. So we use cloud APIs from OpenAI, Google, and Groq. This makes the system fast and free for users. But it does mean user data goes to those companies. We talk about this in Section 5.
---
## 5. Assumptions and Limits
### 5.1 What We Assumed
- Users will write in English
- Songs will sound like Anglo-American pop music
- The cloud APIs (OpenAI, Google, Groq) will stay available
- Users are okay with waiting about 30 seconds
### 5.2 What the System Cannot Do
- The Hugging Face free server has only 16 GB RAM and no GPU
- Free APIs sometimes hit rate limits or have outages
- Audio is computer-made, not studio quality
- The emotion model only knows English text
### 5.3 What is Not Included
- The user cannot sing in real time
- No multi-track editing
- No commercial music license
- No mobile app yet
---
## 6. External Libraries
We use these third-party libraries. All credits are in `requirements.txt` and the README.
| Library | What it Does | License |
|---------|---------|---------|
| transformers | Loads RoBERTa [6] and GPT-2 [7] | Apache 2.0 |
| openai | Talks to ChatGPT | MIT |
| google-generativeai | Talks to Gemini | Apache 2.0 |
| groq | Talks to LLaMA | MIT |
| music21 [2] | Parses chords | BSD |
| mir_eval [3] | Calculates chord similarity | MIT |
| nltk | Calculates METEOR [5] score | Apache 2.0 |
| pydub | Mixes audio together | MIT |
| edge-tts | Makes vocal voice | MIT |
| gradio | Makes the website | Apache 2.0 |
| pandas, numpy | Handles data | BSD |
| matplotlib, seaborn | Makes charts | PSF, BSD |
---
## 7. Testing Strategy
The full test results are in a separate file called `TEST_CASES.md`. Here is a quick summary.
| Test Category | Number of Tests | Pass Rate |
|---------------|-------|-----------|
| Emotion Detection | 10 | 100% |
| Chord Retrieval | 9 | 100% |
| Lyric Generation | 3 | 100% |
| Audio Synthesis | 4 | 100% |
| End-to-End Pipeline | 5 | 100% |
| Edge Cases | 4 | 75% (1 known limit) |
| API Resilience | 3 | 100% |
| Performance | 4 | 100% |
| **TOTAL** | **42** | **97.6%** |
The one test that failed was for non-English input. This is a known limit, not a bug. It is listed in Section 5.2 and in future work.
---
## 8. Deployment and Operations
### 8.1 Production Setup
- **Platform:** Hugging Face Spaces (free CPU tier)
- **Auto-deploy:** When we push to GitHub, the website updates
- **Uptime:** Best effort, no guarantee on free tier
- **Cold start:** About 60 seconds after being idle
### 8.2 How to Reproduce the System
Anyone can run this system in three steps:
1. Clone the GitHub repository
2. Install all libraries from `requirements.txt`
3. Set three secret keys:
   - `GEMINI_KEY` from https://aistudio.google.com/apikey
   - `OPENAI_KEY` from https://platform.openai.com/api-keys
   - `GROQ_KEY` from https://console.groq.com/keys
Then run the Colab notebook cell by cell. Or run `python app.py` to start the website.
### 8.3 Monitoring
- API errors are printed to the log
- If Gemini fails, the system tries gemini-flash-latest instead
- Audio files use unique timestamps so the player does not show old audio
---
## 9. Ethics and Risk Assessment
We found three real-world risks and planned safeguards for each.
| Risk | Planned Safeguard |
|------|-------------------|
| Mental health misuse | A planned crisis word detector will redirect users to Lifeline before audio is made |
| Copyright concerns | Chord progressions are not protected by copyright under US Music Modernization Act 2018. We also credit source artists. |
| Data privacy | We use no-logging API headers. A future local-only mode will keep all data on the user device. |
We also did a bias check. The emotion model [1][6] is trained on Reddit which is mostly Western English. The chord database [9] is mostly Anglo-American pop. Future work will add other languages and global music styles.
---
## 10. Future Work
| Improvement | Status |
|-------------|--------|
| Load ChoCo corpus [8] (4,000+ more songs) | Code is ready but not loaded yet |
| Local-only LLaMA mode (no cloud) | Planned for version 2.0 |
| Multi-language emotion detection | Research only |
| Crisis safeguard implementation | Designed but not coded |
| Mobile app | Just an idea |
| User feedback for ARIA learning | Just an idea |
---
## 11. References

[1] D. Demszky et al., "GoEmotions: A Dataset of Fine-Grained Emotions," in Proc. 58th Annu. Meeting Assoc. Comput. Linguistics (ACL), 2020, pp. 4040-4054.

[2] M. S. Cuthbert and C. Ariza, "music21: A Toolkit for Computer-Aided Musicology and Symbolic Music Data," in Proc. 11th Int. Soc. Music Inf. Retrieval Conf. (ISMIR), 2010, pp. 637-642.

[3] C. Raffel et al., "mir_eval: A Transparent Implementation of Common MIR Metrics," in Proc. 15th Int. Soc. Music Inf. Retrieval Conf. (ISMIR), 2014, pp. 367-372.

[4] D. Herremans and E. Chew, "Tension Ribbons: Quantifying and Visualising Tonal Tension," in Proc. 2nd Int. Conf. Technol. Music Notation Representation (TENOR), 2017, pp. 8-18.

[5] S. Banerjee and A. Lavie, "METEOR: An Automatic Metric for MT Evaluation with Improved Correlation with Human Judgments," in Proc. ACL Workshop, 2005, pp. 65-72.

[6] Y. Liu et al., "RoBERTa: A Robustly Optimized BERT Pretraining Approach," arXiv:1907.11692, 2019.

[7] A. Radford et al., "Language Models are Unsupervised Multitask Learners," OpenAI Technical Report, 2019.

[8] J. de Berardinis et al., "The ChoCo Chord Corpus: A Large-Scale Dataset of Annotated Chord Symbols from Pop, Jazz, and Classical Music," Scientific Data, vol. 10, art. 641, 2023.

[9] HookTheory Theorytab Database, HookTheory LLC, 2013-present. [Online]. Available: https://www.hooktheory.com/theorytab
