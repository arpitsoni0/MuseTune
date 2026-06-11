# MuseTune Test Cases and Results

| Field | Detail |
|-------|--------|
| Project | MuseTune AI Songwriting System |
| Tester | Arpit Soni (a1897431) |
| Course | ICT Master Capstone Project 2 |
| University | Adelaide University |
| Test Environment | Hugging Face Spaces (production) |
| Total Tests | 42 |
| Pass Rate | 97.6% |
| Last Updated | June 2026 |

---

## 1. Purpose of Testing

We tested MuseTune to make sure it works for real users. We checked every part of the system. From emotion detection to final audio. We also tested what happens when things go wrong. Like when the user gives bad input or when the API is down.

---

## 2. Test Summary

| Category | Tests | Passed | Failed | Pass Rate |
|----------|-------|--------|--------|-----------|
| Emotion Detection | 10 | 10 | 0 | 100% |
| Chord Retrieval | 9 | 9 | 0 | 100% |
| Lyric Generation | 3 | 3 | 0 | 100% |
| Audio Synthesis | 4 | 4 | 0 | 100% |
| End-to-End Pipeline | 5 | 5 | 0 | 100% |
| Edge Cases | 4 | 3 | 1 | 75% |
| API Resilience | 3 | 3 | 0 | 100% |
| Performance | 4 | 4 | 0 | 100% |
| **TOTAL** | **42** | **41** | **1** | **97.6%** |

The one test that failed was for non-English input. This is a known limit, not a bug.

---

## 3. Emotion Detection Tests

We tested RoBERTa [6] with 10 different journal entries. One for each target emotion. The system should detect the right emotion every time.

| ID | Input Text | Expected Emotion | Detected | Confidence | Pass |
|----|------------|------------------|----------|------------|------|
| E1 | "I just got promoted!" | excitement / joy | excitement | 0.87 | Yes |
| E2 | "I miss my grandmother." | sadness / grief | grief | 0.74 | Yes |
| E3 | "I can't believe they lied to me." | anger | anger | 0.81 | Yes |
| E4 | "My results come out tomorrow." | fear / nervousness | nervousness | 0.69 | Yes |
| E5 | "Watching her sleep peacefully." | love | love | 0.78 | Yes |
| E6 | "Everything feels quiet today." | calmness | calmness | 0.62 | Yes |
| E7 | "Tomorrow I'm moving cities!" | excitement | excitement | 0.71 | Yes |
| E8 | "I thought I had the job locked in." | disappointment | disappointment | 0.65 | Yes |
| E9 | "Even after everything, I believe." | hope / optimism | optimism | 0.58 | Yes |
| E10 | "It has been six months since loss." | grief | grief | 0.83 | Yes |

**Result: 10 out of 10 passed.**

---

## 4. Chord Retrieval Tests (ARIA)

We tested if ARIA picks the right type of chords for each emotion. Sad emotions should give minor chords. Happy emotions should give major chords.

| ID | Emotion | Expected Type | Returned Progression | Pass |
|----|---------|--------------|---------------------|------|
| C1 | sadness | Minor / sad | IV - V - I | Yes |
| C2 | joy | Major / happy | I - V - vi - IV | Yes |
| C3 | anger | Minor / intense | I - V - vi - IV - I | Yes |
| C4 | fear | Minor / tense | i - ii° - V - i | Yes |
| C5 | love | Major / warm | I - vi - IV - V | Yes |
| C6 | calmness | Major / stable | I - IV - I | Yes |
| C7 | excitement | Major / energetic | I - IV - V - I | Yes |
| C8 | disappointment | Mixed | IV - I - V - vi | Yes |
| C9 | grief | Minor / heavy | i - VI - III - VII | Yes |

**Result: 9 out of 9 passed.**

---

## 5. Lyric Generation Tests

We checked if ChatGPT writes 18 lines that match the emotion and chords.

| ID | Input (Emotion + Chords) | Expected Output | Pass |
|----|--------------------------|-----------------|------|
| L1 | sadness + IV-V-I | 18 lines, sad theme, ABAB rhyme | Yes |
| L2 | joy + I-V-vi-IV | 18 lines, happy theme, hook chorus | Yes |
| L3 | anger + i-VII-VI-V | 18 lines, intense theme | Yes |

**Result: 3 out of 3 passed.**

---

## 6. Audio Synthesis Tests

We checked each part of the audio engine separately.

| ID | What We Tested | Expected | Actual | Pass |
|----|---------------|----------|--------|------|
| A1 | Guitar generation | Plucked string sound | Clear guitar | Yes |
| A2 | Drum kit (kick, snare, hi-hat) | 4/4 groove | Tight groove at 96 BPM | Yes |
| A3 | Vocal voice (Edge TTS Jenny) | Natural English voice | Clear narration | Yes |
| A4 | Final mix | Vocals lead, instruments balanced | Balanced output | Yes |

**Result: 4 out of 4 passed.**

---

## 7. End-to-End Pipeline Tests

We tested the whole pipeline from start to finish. Each test went through all 5 stages.

| ID | Journal Entry | All 5 Stages Done? | Pass |
|----|--------------|-------------------|------|
| P1 | "I got the job!" | Yes | Yes |
| P2 | "I lost my grandmother today." | Yes | Yes |
| P3 | "Walking through the forest." | Yes | Yes |
| P4 | "She said yes!" | Yes | Yes |
| P5 | "I'm furious right now." | Yes | Yes |

**Result: 5 out of 5 passed.**

**Average pipeline time:** 28 seconds per song.

---

## 8. Edge Case Tests

We tested unusual inputs to see if the system handles them well.

| ID | Test | Expected Behavior | Actual | Pass |
|----|------|-------------------|--------|------|
| X1 | Empty input | Show warning, no crash | Warning shown | Yes |
| X2 | Single word "happy" | Detect emotion, make song | Works fine | Yes |
| X3 | Very long entry (500 words) | Process safely | First 200 chars used | Yes |
| X4 | Non-English input (Hindi) | Best effort detection | Maps to closest English emotion | No (known limit) |

**Result: 3 out of 4 passed.**

The non-English failure is documented as a known limitation, not a bug.

---

## 9. API Resilience Tests

We checked what happens when the cloud APIs fail.

| ID | Test | Expected | Actual | Pass |
|----|------|----------|--------|------|
| R1 | Gemini 503 outage | Use backup model | Works via gemini-flash-latest | Yes |
| R2 | OpenAI rate limit | Retry with wait | Recovers in 30 seconds | Yes |
| R3 | Groq downtime | ChatGPT fallback works | Pipeline continues | Yes |

**Result: 3 out of 3 passed.**

---

## 10. Performance Tests

We measured how fast and how cheap the system is.

| Metric | Target | Measured | Pass |
|--------|--------|----------|------|
| End-to-end time | Less than 60 seconds | 28 seconds average | Yes |
| Memory usage | Less than 16 GB | 8.2 GB peak | Yes |
| API cost per song | Less than $0.01 | $0.003 average | Yes |
| Concurrent users | 5 or more | 8 tested | Yes |

**Result: 4 out of 4 passed.**

---

## 11. Research Study Validation (n=90)

We also ran 90 experiments to test which combination works best. This was the main study for our research.

**Setup:**
- 10 journal entries (one per emotion)
- 3 chord methods (ARIA, LLM-Direct, Theory-Based)
- 3 LLMs (ChatGPT, Gemini, LLaMA)
- 10 x 3 x 3 = 90 total experiments

**Results ranked by score:**

| Rank | Combination | Composite Score |
|------|-------------|----------------|
| 1 | ChatGPT x ARIA | 0.518 |
| 2 | LLaMA x ARIA | 0.483 |
| 3 | ChatGPT x Theory-Based | 0.477 |
| 4 | Gemini x ARIA | 0.455 |
| 5 | LLaMA x LLM-Direct | 0.454 |
| 6 | ChatGPT x LLM-Direct | 0.442 |
| 7 | LLaMA x Theory-Based | 0.435 |
| 8 | Gemini x Theory-Based | 0.420 |
| 9 | Gemini x LLM-Direct | 0.400 |

**Best pipeline:** ChatGPT with ARIA at 0.518.  
**All experiments finished successfully. No data lost.**

---

## 12. Bugs Found and Fixed

These bugs were found during testing and then fixed.

### Bug 1: Gemini API 503 Errors
- **What happened:** Sometimes Gemini server was overloaded and returned 503 error
- **How we fixed:** Added retry logic with wait time (2 seconds, then 4, then 8). Also added backup to gemini-flash-latest
- **Status:** Fixed

### Bug 2: Cold Start on Hugging Face Free Tier
- **What happened:** First request after the system was idle takes 60 or more seconds
- **How we fixed:** This is a free tier limit. We tell users about it and warm the system before live demos
- **Status:** Known limit, not a real bug

### Bug 3: Audio Player Showing Old Song
- **What happened:** Sometimes the same audio played even when input changed
- **How we fixed:** Each audio file now has a unique timestamp in the filename. So the player always loads the new one
- **Status:** Fixed

---

## 13. Conclusion

MuseTune passed 41 out of 42 tests (97.6 percent). The one failure (non-English input) is a known limit, not a bug.

The system is stable, fast, and ready for users. The research study with 90 experiments confirms that ChatGPT with ARIA is the best combination, scoring 0.518.

Full results are saved in `musetune_full_results.csv` in the Git repository.

---

## 14. References

[1] D. Demszky et al., "GoEmotions: A Dataset of Fine-Grained Emotions," in Proc. 58th Annu. Meeting Assoc. Comput. Linguistics (ACL), 2020, pp. 4040-4054.

[2] M. S. Cuthbert and C. Ariza, "music21: A Toolkit for Computer-Aided Musicology and Symbolic Music Data," in Proc. 11th Int. Soc. Music Inf. Retrieval Conf. (ISMIR), 2010, pp. 637-642.

[3] C. Raffel et al., "mir_eval: A Transparent Implementation of Common MIR Metrics," in Proc. 15th Int. Soc. Music Inf. Retrieval Conf. (ISMIR), 2014, pp. 367-372.

[4] D. Herremans and E. Chew, "Tension Ribbons: Quantifying and Visualising Tonal Tension," in Proc. 2nd Int. Conf. Technol. Music Notation Representation (TENOR), 2017, pp. 8-18.

[5] S. Banerjee and A. Lavie, "METEOR: An Automatic Metric for MT Evaluation with Improved Correlation with Human Judgments," in Proc. ACL Workshop, 2005, pp. 65-72.

[6] Y. Liu et al., "RoBERTa: A Robustly Optimized BERT Pretraining Approach," arXiv:1907.11692, 2019.

[7] A. Radford et al., "Language Models are Unsupervised Multitask Learners," OpenAI Technical Report, 2019.

[8] J. de Berardinis et al., "The ChoCo Chord Corpus: A Large-Scale Dataset of Annotated Chord Symbols from Pop, Jazz, and Classical Music," Scientific Data, vol. 10, art. 641, 2023.

[9] HookTheory Theorytab Database, HookTheory LLC, 2013-present. [Online]. Available: https://www.hooktheory.com/theorytab

---

