# MuseTune Design Document

| Field | Detail |
|-------|--------|
| Project | MuseTune: AI-Driven Songwriting using Sentiment-Based Chord Mapping with a Hybrid LLM and Music Database Retrieval Framework |
| Author | Arpit Soni (a1897431) |
| Course | ICT Master Capstone Project 2 |
| University | Adelaide University |
| Supervisor | Dr. Hussain Ahmed |
| Version | 1.0 |
| Last Updated | June 2026 |

---

## 1. Project Overview

MuseTune is a fully automated end-to-end pipeline that converts free-text journal entries into 4-line songs accompanied by matching guitar chords and AI-synthesized audio. The system addresses three critical limitations of current AI songwriting tools: fragmented pipelines that treat lyrics and chords as independent outputs, the music theory barrier that prevents novices from translating emotions into harmonically appropriate progressions, and weak evaluation rigor across existing systems [1], [2].

**Live Product:** https://huggingface.co/spaces/Arpitsoni0/musetune  
**Source Code:** https://github.com/arpitsoni0/MuseTune

The system combines five modular components: a RoBERTa classifier fine-tuned on the GoEmotions dataset for sentiment detection across 27 emotional classes [3]; the proposed ARIA (Affect-driven Retrieval for Intelligent Accompaniment) algorithm that queries a curated 7,200-progression corpus sourced from HookTheory [4] and the Chord Corpus (ChoCo) [5]; three state-of-the-art large language models for lyric generation; an audio synthesis stage producing a singable WAV file using a Karplus-Strong guitar synthesizer [6]; and a six-metric evaluation engine.

**Headline Result:** The Gemini x ARIA configuration achieved the highest overall composite score of 0.531 across 90 controlled experiments, with all three ARIA combinations occupying the top three positions.

---

## 2. System Architecture

MuseTune is implemented as a modular, fully automated pipeline composed of five sequential stages. The user provides a free-text journal entry, and the system returns a complete song artifact containing the detected emotion, a four-chord progression, four lines of lyrics, and a playable WAV file.

### 2.1 Pipeline Diagram
### 2.1 Pipeline Diagram
┌─────────────────────────────────────────────────────────────┐
│ USER JOURNAL ENTRY │
└──────────────────────────┬──────────────────────────────────┘
▼
┌─────────────────────────────────────────────────────────────┐
│ STAGE 1: EMOTION DETECTION │
│ RoBERTa fine-tuned on GoEmotions [3] │
│ 27 fine-grained emotion classes │
└──────────────────────────┬──────────────────────────────────┘
▼
┌─────────────────────────────────────────────────────────────┐
│ STAGE 2: CHORD GENERATION (THREE METHODS) │
│ ARIA retrieval over 7,200-progression corpus [4], [5] │
│ LLM-Direct baseline | Theory-Based baseline │
└──────────────────────────┬──────────────────────────────────┘
▼
┌─────────────────────────────────────────────────────────────┐
│ STAGE 3: LYRIC GENERATION (THREE LLMs) │
│ OpenAI ChatGPT | Google Gemini | Meta LLaMA via Groq │
└──────────────────────────┬──────────────────────────────────┘
▼
┌─────────────────────────────────────────────────────────────┐
│ STAGE 4: AUDIO SYNTHESIS │
│ Karplus-Strong guitar [6] + Edge TTS vocal narration │
└──────────────────────────┬──────────────────────────────────┘
▼
┌─────────────────────────────────────────────────────────────┐
│ STAGE 5: EVALUATION ENGINE │
│ Six citable metrics aggregated into composite score │
└──────────────────────────┬──────────────────────────────────┘
▼
┌─────────────────────────────────────────────────────────────┐
│ FINAL SONG ARTIFACT + SCORECARD │
└─────────────────────────────────────────────────────────────┘

### 2.2 Technology Stack

| Layer | Technology |
|-------|-----------|
| Frontend | Gradio 4.x |
| Emotion Detection | RoBERTa (SamLowe/roberta-base-go_emotions) [3] |
| Chord Database | Merged ARIA-DB: HookTheory [4] + ChoCo [5] (7,200 progressions) |
| Lyric Generation | OpenAI gpt-4o-mini, Google Gemini 2.5 Flash, Meta LLaMA 3.3-70B via Groq |
| Audio Engine | Karplus-Strong synthesis [6], Edge TTS vocal narration |
| Evaluation | music21 [7], mir_eval [8], GPT-2 [9], NLTK METEOR [10] |
| Deployment | Hugging Face Spaces (CPU tier) |
| Version Control | Git / GitHub |

---

## 3. Core Workflows

### 3.1 End-to-End Generation Workflow

The live demo workflow follows the five-stage pipeline:

1. The user submits a free-text journal entry via the Gradio interface
2. The RoBERTa GoEmotions classifier [3] returns a probability distribution over 27 emotional classes; the dominant class is forwarded with its confidence value
3. The ARIA algorithm maps the detected GoEmotions label to one of nine coarse musical buckets, retrieves filter rules over valence, energy, and mode, and queries the ARIA-DB corpus for the top-ranked progression
4. The selected lyric generation LLM receives the emotion and chord progression under a unified prompt template and produces 18 lines of lyrics structured as Verse-Chorus-Verse-Chorus-Bridge
5. The audio synthesis stage renders the song using Karplus-Strong guitar [6] with emotion-adaptive strumming patterns, bass, drums, pad, and Edge TTS vocal narration
6. The evaluation engine computes all six metrics and presents a radar scorecard

**Average runtime:** Approximately 28 seconds end-to-end.

### 3.2 Experimental Evaluation Workflow

The research evaluation was conducted as a 3x3 factorial design across ten target emotions, yielding n = 90 experiments:

1. Ten journal entries spanning the GoEmotions space are prepared (sadness, joy, anger, fear, excitement, calmness, love, grief, disappointment, hope)
2. For each entry, all nine LLM x chord-method combinations are evaluated
3. Every output is scored on all six citable metrics
4. Results are aggregated and saved to `musetune_full_results.csv`
5. Six publication-quality figures are generated for analysis
6. Statistical robustness is verified via box plot variance across the ten entries

**Total compute:** Approximately 18 minutes wall-clock time on Google Colab; API costs approximately USD 0.20.

---

## 4. Key Design Decisions

### 4.1 RoBERTa over Alternatives for Emotion Detection

RoBERTa was selected over alternatives such as BERT or DistilBERT due to its superior performance on emotion recognition benchmarks and its strong handling of informal language patterns common in journal entries. The 27-class GoEmotions taxonomy [3] is critical for songwriting expressiveness, as it permits distinctions far finer than the binary positive/negative or six-basic-emotion schemes that dominate earlier work [11], [12]. The distinction between sadness, grief, disappointment, and remorse, all of which would collapse into a single class in coarser taxonomies, supports meaningful variation in the resulting chord progressions and lyrical content.

### 4.2 Hybrid Retrieval over Pure LLM Generation

Modern LLMs such as GPT-4, Gemini, and LLaMA produce remarkably fluent text [9], but their outputs in symbolic music tasks often deviate from established human practice due to limited exposure to chord progression annotations. This motivated ARIA's hybrid design: an LLM-grounded pipeline supplemented by retrieval over a curated corpus of 7,200 human-composed progressions. The experimental results confirm this design decision: ARIA achieves Chord Similarity of 0.614 against LLM-Direct's 0.322, a 0.292-point gap directly attributable to the corpus grounding.

### 4.3 3x3 Factorial Experimental Design

The 3x3 design (three chord methods x three LLMs) supports rigorous statistical comparison of all nine pipeline configurations. With ten journal entries spanning the emotional space, this yields n = 90 data points, sufficient for box plot variance analysis and per-emotion disaggregation. This design improves substantially on prior work that often relies on small qualitative demonstrations (n = 1 to n = 3) or generic NLP scores such as BLEU and ROUGE [10].

### 4.4 Six Citable Evaluation Metrics

Each evaluation metric maps directly to a peer-reviewed publication, ensuring every reported score is defensible against academic scrutiny:

| Metric | Source |
|--------|--------|
| Harmonic Validity | music21 [7] |
| Chord Similarity | mir_eval / MIREX [8] |
| Tonal Tension | Spiral Array [13] |
| Fluency | GPT-2 perplexity [9] |
| METEOR | Semantic alignment [10] |
| Emotional Alignment | GoEmotions re-detection [3] |

The composite Overall Score is computed as:
Overall = 0.15 * Harmonic + 0.15 * ChordSim + 0.10 * (1 - Tension)
+ 0.20 * Fluency + 0.20 * METEOR + 0.20 * Alignment

The chord-side metrics weight 0.40 in aggregate and the lyric-side metrics weight 0.60, reflecting the relative dimensionality of lyric output.
### 4.5 Karplus-Strong Audio Synthesis
The Karplus-Strong plucked-string algorithm [6] was selected for guitar synthesis due to its computational efficiency and the absence of external SoundFont dependencies. Each chord's constituent pitches are resolved from the Roman numeral via music21 [7] using the emotion-determined key, synthesized individually with a 0.996 decay coefficient, and combined with a 30-millisecond inter-string strum delay to emulate the natural attack pattern of a strummed acoustic guitar.
### 4.6 ARIA Algorithm as Core Innovation
ARIA addresses the fragmented pipeline limitation by providing a unified emotion-to-chord retrieval mechanism. The algorithm operates in two stages: a learned GoEmotions-to-musical-bucket mapping followed by valence, energy, and mode filtering over the curated ARIA-DB corpus. This approach is conceptually similar to the random walk methods of Ren et al. [14] but introduces explicit emotional grounding through the Spotify audio features attached to each HookTheory entry [4].
---
## 5. Hugging Face Spaces Deployment
### 5.1 Deployment Overview
MuseTune is deployed as a fully public web application on Hugging Face Spaces, available at:
**https://huggingface.co/spaces/Arpitsoni0/musetune**
The deployment provides free, no-signup access to the complete MuseTune pipeline from any web browser. Users interact with the system through the Gradio interface and receive the detected emotion, the ARIA chord progression, the 18-line full song lyrics, and the synthesized audio file directly in the browser.
### 5.2 Deployment Architecture
The production deployment uses the Hugging Face Spaces free CPU tier with 16 GB RAM. The architecture mirrors the modular structure of the Colab research notebook, with all five pipeline stages running on the Spaces virtual machine. Cloud-based LLM access (OpenAI ChatGPT, Google Gemini, Meta LLaMA via Groq) is performed via secure API calls using environment-scoped secrets. The deployment file `app.py` contains the full production codebase including the gradio_client compatibility patch required by the current Spaces Python environment.
### 5.3 Continuous Deployment Pipeline
The Hugging Face Space is connected to the GitHub repository through a continuous deployment workflow. Every push to the main branch of the GitHub repository triggers an automatic rebuild and redeployment of the Hugging Face Space, ensuring that the live system always reflects the most recent verified state of the codebase.
### 5.4 Why Hugging Face Spaces
Hugging Face Spaces was selected as the deployment platform for four reasons. First, the free CPU tier supports the computational requirements of the RoBERTa, GPT-2, and music21 components without the cost barrier of dedicated GPU instances. Second, native integration with Gradio enables zero-configuration hosting of the interactive interface. Third, the platform's environment-scoped secrets system provides secure storage for the three required API keys (GEMINI_KEY, OPENAI_KEY, GROQ_KEY) without exposing them in source code. Fourth, the public nature of the platform aligns with the research goal of open reproducibility: any researcher or practitioner can directly experience the system without installation overhead.
### 5.5 Known Deployment Constraints
The free CPU tier introduces several documented constraints. The Space enters a cold-start state after approximately 30 minutes of inactivity, requiring 60 seconds for the first request after idle. Concurrent capacity is limited compared to commercial tiers. The system has no service-level availability guarantee. These constraints are acceptable for a research demonstration but would require migration to a dedicated tier for production-scale deployment.
---
## 6. Assumptions and Limitations
### 6.1 Assumptions
- Users will provide journal entries in English
- Western tonal music conventions serve as the baseline aesthetic
- Cloud API services (OpenAI, Google, Groq) remain accessible
- A response latency of approximately 30 seconds is acceptable
### 6.2 Known Limitations
- The Hugging Face Spaces free tier provides 16 GB RAM and CPU-only inference, constraining model selection
- API services are subject to rate limits and occasional 503 outages
- Audio synthesis prioritizes openness and reproducibility over fidelity; production-grade vocal singing remains future work
- The emotion model is trained on Reddit text (Western, English-speaking), introducing inherent cultural bias
- ARIA-DB is dominated by pop and rock genres; jazz, classical, and non-Western music are underrepresented
### 6.3 Out of Scope
- Real-time vocal performance by the user
- Multi-track audio editing capabilities
- Commercial music licensing
- Native mobile applications
---
## 7. External Libraries and Attribution
All third-party libraries are properly cited in `requirements.txt` and the project README.
| Library | Purpose | License |
|---------|---------|---------|
| transformers | RoBERTa [3] and GPT-2 [9] inference | Apache 2.0 |
| openai | ChatGPT API client | MIT |
| google-generativeai | Gemini API client | Apache 2.0 |
| groq | LLaMA inference client | MIT |
| music21 [7] | Roman numeral analysis and harmonic validity | BSD |
| mir_eval [8] | MIREX chord similarity computation | MIT |
| nltk | METEOR [10] scoring | Apache 2.0 |
| pydub | Audio mixing and export | MIT |
| edge-tts | Vocal narration synthesis | MIT |
| gradio | Web user interface | Apache 2.0 |
| pandas, numpy, scipy | Data processing | BSD |
| matplotlib, seaborn | Figure generation | PSF, BSD |
---
## 8. Testing Strategy
A comprehensive test suite covering 42 cases was executed across eight categories. Full results are documented in the companion `TEST_CASES.md` file.
| Test Category | Tests | Pass Rate |
|---------------|-------|-----------|
| Emotion Detection | 10 | 100% |
| Chord Retrieval | 9 | 100% |
| Lyric Generation | 3 | 100% |
| Audio Synthesis | 4 | 100% |
| End-to-End Pipeline | 5 | 100% |
| Edge Cases | 4 | 75% |
| API Resilience | 3 | 100% |
| Performance | 4 | 100% |
| **TOTAL** | **42** | **97.6%** |
The single failing test corresponds to non-English input, documented in Section 6.2 as a known constraint rather than a bug.
---
## 9. Reproducibility
The complete system can be reproduced in three steps:
1. Clone the GitHub repository at https://github.com/arpitsoni0/MuseTune
2. Install dependencies from `requirements.txt`
3. Configure three environment variables:
   - `GEMINI_KEY` from https://aistudio.google.com/apikey
   - `OPENAI_KEY` from https://platform.openai.com/api-keys
   - `GROQ_KEY` from https://console.groq.com/keys
The Colab notebook executes the full pipeline end-to-end with documented model versions (gpt-4o-mini, gemini-2.5-flash, llama-3.3-70b-versatile) to maximize reproducibility. Alternatively, the live deployment at https://huggingface.co/spaces/Arpitsoni0/musetune provides immediate access without local installation.
---
## 10. Ethics and Risk Assessment
Three real-world risks have been identified with corresponding planned safeguards:
| Risk | Planned Safeguard |
|------|-------------------|
| Mental health misuse | A planned crisis keyword detector will redirect users to Lifeline (13 11 14) prior to audio generation |
| Copyright and attribution | Chord progressions are not copyrightable under the US Music Modernization Act 2018; ARIA-DB tags provenance and attributes inspiration to source artists |
| Data privacy | Zero-logging API headers are enforced; a planned local-only LLaMA mode will preserve user data on-device |
A bias audit has also been conducted. The GoEmotions training data [3] is sourced from Reddit, introducing Western and English-speaking bias. ARIA-DB skews toward Anglo-American pop due to the underlying distribution of HookTheory [4]. Future work will extend the system to multilingual emotion detection and global music corpora.
---
## 11. Future Work
| Enhancement | Status |
|-------------|--------|
| Full ChoCo integration [5] for genre diversity | Code implemented, corpus not loaded in deployed pickle |
| Top-N stochastic retrieval in ARIA | Designed, not implemented |
| Neural singing voice integration (Bark, DiffSinger, RVC) | Planned for v2.0 |
| Local-only LLaMA mode for privacy-conscious users | Planned for v2.0 |
| Multilingual emotion detection | Research phase |
| Crisis keyword detector | Designed, not implemented |
| Emotion-adaptive method selector | Designed, not implemented |
| Ensemble LLM routing | Conceptual |
---
## 12. References
[1] S. Ding et al., "SongComposer: A large language model for lyric and melody generation in song composition," in Proc. ACL, 2024.
[2] X. Ma et al., "AI-Lyricist: Generating music and vocabulary constrained lyrics," in Proc. ACM Multimedia, 2024.
[3] D. Demszky, D. Movshovitz-Attias, J. Ko, A. Cowen, G. Nemade, and S. Ravi, "GoEmotions: A dataset of fine-grained emotions," in Proc. ACL, 2020, pp. 4040-4054.
[4] HookTheory LLC, "Theorytab database," 2024. [Online]. Available: https://www.hooktheory.com/theorytab
[5] J. de Berardinis, A. Merono-Penuela, A. Poltronieri, and V. Presutti, "ChoCo: A chord corpus and a data transformation workflow for musical harmony knowledge graphs," Sci. Data, vol. 10, no. 641, 2023.
[6] K. Karplus and A. Strong, "Digital synthesis of plucked-string and drum timbres," Computer Music Journal, 1983.
[7] M. S. Cuthbert and C. Ariza, "music21: A toolkit for computer-aided musicology and symbolic music data," in Proc. ISMIR, 2010, pp. 637-642.
[8] C. Raffel, B. McFee, E. J. Humphrey, J. Salamon, O. Nieto, D. Liang, and D. P. W. Ellis, "mir_eval: A transparent implementation of common MIR metrics," in Proc. ISMIR, 2014.
[9] A. Radford, J. Wu, R. Child, D. Luan, D. Amodei, and I. Sutskever, "Language models are unsupervised multitask learners," OpenAI Technical Report, 2019.
[10] S. Banerjee and A. Lavie, "METEOR: An automatic metric for MT evaluation with improved correlation with human judgments," in Proc. ACL Workshop, 2005, pp. 65-72.
[11] W. Medhat, A. Hassan, and H. Korashy, "Sentiment analysis algorithms and applications: A survey," Ain Shams Eng. J., vol. 5, no. 4, pp. 1093-1113, 2014.
[12] M. S. U. Miah et al., "A multimodal approach to cross-lingual sentiment analysis with ensemble of transformer and LLM," Sci. Rep., vol. 14, no. 9603, 2024.
[13] D. Herremans and E. Chew, "Tension ribbons: Quantifying and visualising tonal tension," in Proc. TENOR, 2017.
[14] T. Ren et al., "Guitar chord-generating algorithm based on complex network," Physica A, vol. 450, pp. 302-314, 2016.
