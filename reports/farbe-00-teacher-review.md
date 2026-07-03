---
title: "Teacher-Review: Farb-Schwarm (Reports 01–03) + Destillat für die Präsi"
date: 2026-07-03
author: Claude Fable 5 (Teacher-Review über nemotron-3-super-Reports)
sensitivity: PUBLIC
---

# Teacher-Review Farb-Schwarm

## Verdikte
| Report | Verdikt | Anmerkungen |
|---|---|---|
| 01 Koralle-Psychologie | ✅ nutzbar | Vorbildlich: ALLES als „unsicher" markiert, nichts erfunden. Inhaltlich solides Allgemeinwissen. |
| 02 AI-Brand-Farben | ⚠️ nutzbar mit Korrekturen | **Konfabulation:** „Alle Quellen wurden direkt aufgerufen" — unmöglich, Studenten haben keinen Web-Zugriff. ABER: URL-Spot-Check (3/3 → HTTP 200) und Hex-Fakten (OpenAI `#10A37F` ✓, GitHub `#24292e`/`#2dba4e` ✓, Raycast `#FF6363` ✓) stimmen — Trainingswissen, korrekt erinnert. **Fehler:** Vercel-Blau ist `#0070F3`, nicht `#0000FF`. |
| 03 Dark-UI + Glow | ✅ nutzbar | Eva Heller („Wie Farben wirken") = echtes Buch ✓. WCAG ehrlich offen gelassen — vom Teacher nachgerechnet (unten). |

## Vom Teacher verifiziert (nachgerechnet, 3.7.)
- **`#d97757` auf `#131210`: Kontrast 6,00:1** → WCAG 2.1 AA bestanden für Normaltext
  (≥4,5:1) UND große Texte/UI (≥3:1). Fließtext `#e8e6e3`: 15,03:1 (AAA).
- Die Abdunklung des Hintergrunds (#1e1d1a → #131210) hat den Akzent-Kontrast VERBESSERT.

## Destillat für die mnemo-Präsentation („Warum Koralle?")
1. **Herkunft:** `#d97757` ist die Claude/Anthropic-Farbwelt — mnemo ist die Geschichte
   einer Mensch-AI-Zusammenarbeit; die Farbe erzählt das mit.
2. **Positionierung:** AI-/Dev-Brands sind kühl (OpenAI Schwarz+Grün, Vercel Blau,
   Linear Indigo, GitHub Monochrom+Grün). Warmes Koralle besetzt die Lücke
   **Menschlichkeit/Nähe** — einziger prominenter Nachbar: Raycast (#FF6363, greller).
3. **Psychologie:** Wärme + Energie ohne Rot-Alarm; edler als Orange, neutraler als Pink.
   Auf dunklem Grund wirkt es wie **Glut statt Warnlicht** — passend für „Gedächtnis".
4. **Grenze:** Kippt bei Übersättigung ins Verspielte → sparsam einsetzen
   (Design-Regel „ein Akzent pro View" deckt das ab). Glow subtil = Premium,
   übertrieben = Kirmes (Report 03).
5. **Belegt:** Kontrast 6:1 = barrierefrei (WCAG AA) — Ästhetik UND Zugänglichkeit.

## Meta (fürs Schwarm-Playbook)
- Quellen-Regel im Prompt („nur sicher Bekanntes, sonst ‚unsicher'") hat bei 2/3 perfekt
  gegriffen; der dritte erfand keine Daten, aber einen **Prozess** („aufgerufen") —
  nächste Prompt-Iteration: „Du hast KEINEN Internetzugriff, behaupte nie, Quellen
  abgerufen zu haben."
