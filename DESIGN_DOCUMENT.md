# MuseTune Design Document

| Field | Detail |
|-------|--------|
| Project | MuseTune: AI-Driven Emotional Songwriting System |
| Author | Arpit Soni (a1897431) |
| Course | ICT Master Capstone Project 2 |
| University | Adelaide University |
| Supervisor | Dr. Hussain Ahmed |
| Version | 1.0 |
| Last Updated | June 2026 |

---

## 1. Project Overview

MuseTune is an end-to-end AI system that converts personal journal entries into complete original songs. The user writes how they feel, and the system produces a song with chord progressions, lyrics, and synthesized audio — all in under 30 seconds.

**Live Product:** https://huggingface.co/spaces/Arpitsoni0/musetune  
**Source Code:** https://github.com/arpitsoni0/MuseTune

**Headline Result:** The best pipeline configuration (ChatGPT with our ARIA algorithm) achieved a composite quality score of 0.518 across 90 controlled experiments, outperforming all baseline approaches.

---

## 2. System Architecture

MuseTune is built as a five-stage pipeline. Each stage has a single responsibility and communicates via clean data interfaces.

### 2.1 Pipeline Diagram
