---
title: "Akustischer Seitenkanalangriff auf Tastaturen"
date: 2025-01-14T17:10:00+01:00
slug: /akustischer-seitenkanalangriff-auf-tastaturen/
description: Akustische Seitenkanalangriffe auf Tastaturen ermöglichen die Rekonstruktion von Tastenanschlägen allein über Audioaufnahmen und stellen eine ernstzunehmende Bedrohung für Passwörter und andere vertrauliche Eingaben dar.
image: images/seitenkanal-tastatur.jpg
caption: Illustration eines akustischen Seitenkanalangriffs auf eine Tastatur - by Google Gemini
categories:
  - IT-Security
tags:
  - side-channel
  - akustik
  - deep-learning
  - coatnet
  - IT-Security
draft: false
author: Pius
---

## Wichtiger Hinweis

Dieser Artikel stellt ausschließlich den aktuellen **Forschungsstand zu akustischen Seitenkanalangriffen** dar und dient nur zu Informations- und Bildungszwecken. Die praktische Anwendung der beschriebenen Methoden ohne ausdrückliche Erlaubnis ist illegal und kann strafrechtliche Konsequenzen nach sich ziehen. Leser sollten diese Informationen ausschließlich im Rahmen legaler und ethischer Richtlinien nutzen.

## Einordnung: Warum Tastatur-Geräusche kritisch sind

In einer Welt voller Mikrofone – in Smartphones, Laptops und Smart-Geräten – entstehen neue Angriffsflächen für die IT-Sicherheit. Akustische Seitenkanalangriffe nutzen genau diese Mikrofone aus, um aus den Geräuschen einer Tastatur sensible Daten wie Passwörter oder Nachrichten zu rekonstruieren. Eine aktuelle Arbeit zeigt, dass ein Deep-Learning-Modell Tastenanschläge mit bis zu 95 % Genauigkeit aus Smartphone-Aufnahmen und 93 % über Zoom klassifizieren kann.

## Was ist ein akustischer Seitenkanalangriff?

Ein akustischer Seitenkanalangriff (Acoustic Side Channel Attack, ASCA) zielt darauf ab, **Informationen aus Geräuschen** zu extrahieren, die von elektronischen Systemen oder mechanischen Komponenten wie Tastaturen erzeugt werden. Diese akustischen Emissionen können mit empfindlichen Mikrofonen aufgezeichnet und anschließend per Signalverarbeitung und Machine Learning analysiert werden, um Eingaben oder kryptografische Operationen zu rekonstruieren.

Technisch basiert der Angriff auf der Korrelation zwischen Schallprofilen und internen Prozessen eines Systems. So verändern sich etwa Geräusche eines Prozessors während bestimmter Berechnungen, und die individuellen Klangsignaturen von Tastenanschlägen lassen sich einzelnen Tasten zuordnen. Moderne Deep-Learning-Modelle ermöglichen hierbei eine deutlich höhere Erfolgsquote als frühere, rein signalbasierte Verfahren.

## Ablauf des Angriffs

Der in der Forschung beschriebene Angriff läuft grob in fünf Schritten ab:

1. **Datenerfassung**  
   Akustische Signale der Tastatur werden mit einem Mikrofon aufgenommen, z. B. durch ein nahe platziertes Smartphone oder über eine Videokonferenzplattform wie Zoom. Die Aufnahmen dienen später als Trainings- und Testdaten für das Klassifikationsmodell.

2. **Keystroke-Isolierung**  
   Aus der Rohaufnahme werden die einzelnen Tastenanschläge extrahiert, häufig mittels Signalverarbeitung und Verfahren wie der Fast Fourier Transformation (FFT), um charakteristische Energiespitzen im Frequenzspektrum zu identifizieren. Jeder Keystroke wird in ein eigenes Audio-Sample überführt, das anschließend weiterverarbeitet wird.

3. **Feature-Extraktion mit Mel-Spektrogrammen**

   Die isolierten Samples werden in **Mel-Spektrogramme** umgewandelt, also 2D-Darstellungen der zeitlichen Frequenzverteilung. Zur Robustheitssteigerung kommen Techniken wie **SpecAugment** zum Einsatz, bei denen zufällig Bereiche im Spektrogramm maskiert werden, um das Modell gegen Rauschen und Variationen zu härten.

4. **Modelltraining mit CoAtNet**

   Für die Klassifikation der Tasten nutzen die Autoren ein Deep-Learning-Modell auf Basis von **CoAtNet**, einer Architektur, die Convolutional Neural Networks (CNNs) mit Transformer-Schichten kombiniert. 
   Wichtige Architekturbausteine sind:
   - **Stem-Stage (S0)** mit Faltungsschichten zur Reduktion der räumlichen Auflösung des Spektrogramms.
   - **MBConv-Blöcke (S1, S2)** für effiziente Extraktion lokaler Merkmale, häufig mit Squeeze-and-Excitation-Mechanismen.
   - **Downsampling** nach einzelnen Stufen, um die Rechenlast zu reduzieren und gleichzeitig relevante Features zu erhalten.
   - **Transformer-Stufen (S3, S4)** mit 2D-relativer Aufmerksamkeit (RelativeAttention2d), um globale Beziehungen im Spektrogramm zu modellieren.
   - **Feed-Forward-Netze (FFN)** in jeder Stufe, die nichtlineare Transformationen der Merkmalsrepräsentationen liefern.

   CNN-Komponenten erkennen vor allem lokale Muster wie Frequenzbänder und Impulse einzelner Keystrokes, während Transformer-Schichten globale Zusammenhänge und Kontextinformationen zwischen mehreren Anschlägen erfassen.[web:9][web:18]

5. **Rekonstruktion der Zeichen**

   Nach erfolgreicher Klassifikation ordnet das Modell jedem Audio-Sample eine Taste zu und rekonstruiert daraus die ursprünglichen Eingabesequenzen. Auf diese Weise können Passwörter, Chatnachrichten oder andere vertrauliche Texte rekonstruiert werden, ohne dass physischer Zugriff auf das Gerät erforderlich ist.

## Trainingssetup und Datengrundlage

Für das Training wurden Datensätze aus zwei Quellen verwendet: lokal mit einem Smartphone-Mikrofon aufgenommene Keystrokes sowie über Zoom aufgezeichnete Tastatureingaben. Getestet wurde auf einem MacBook-Pro-Keyboard, wobei 36 unterschiedliche Tasten (Buchstaben und Ziffern) jeweils mehrfach in verschiedenen Variationen gedrückt wurden, um genügend Variabilität abzudecken.

Das Modelltraining lief über zahlreiche Epochen mit einem Optimierer wie AdamW, einer kleinen Batchgröße und abfallender Lernrate, um eine stabile Konvergenz zu erzielen. Zur Reduktion von Overfitting trugen Techniken wie SpecAugment und weitere Regularisierungsmaßnahmen bei.[web:19]

## Ergebnisse: Erschreckend hohe Genauigkeit

Die Studie zeigt, dass ein Smartphone in der Nähe der Tastatur Tastenanschläge mit rund **95 % Genauigkeit** klassifizieren kann. Selbst über Videokonferenz-Software wie Zoom lassen sich noch etwa **93 % Genauigkeit** erreichen, was diesen Kanal trotz Kompression und Rauschen äußerst gefährlich macht.

Andere Berichte und Reproduktionen bestätigen, dass diese Erfolgsraten mit frei verfügbaren Deep-Learning-Frameworks und handelsüblichen Geräten erreichbar sind. Damit gelten akustische Angriffe auf Tastaturen nicht mehr nur als theoretisches Szenario, sondern als praktisch umsetzbare Bedrohung im Alltag.

## Schutzmaßnahmen gegen akustische Angriffe

Um das Risiko solcher Angriffe zu reduzieren, kommen mehrere Ansätze in Betracht:

- **Tippstil variieren**  
  Die Studie zeigt, dass bereits eine Änderung des Tippstils – etwa vom Ein-Finger- auf das 10-Finger-System – die Klassifikationsleistung spürbar beeinträchtigen kann.

- **Zwei-Faktor-Authentifizierung (2FA) nutzen**  
  Selbst bei kompromittierten Passwörtern reduziert eine zusätzliche Authentifizierungsebene das Risiko eines erfolgreichen Kontomissbrauchs erheblich.

- **Hintergrundgeräusche erzeugen**  
  Das Abspielen von Rauschen oder Musik während der Eingabe erschwert die saubere Extraktion einzelner Keystrokes aus dem Audiosignal.

- **Biometrische Verfahren einsetzen**  
  Authentifizierung per Fingerabdruck oder Gesichtserkennung verringert die Abhängigkeit von passwortbasierten Eingaben, die akustisch abgegriffen werden könnten.

Langfristig könnten auch spezielle Tastaturdesigns, Sound-Masking-Lösungen oder Betriebssystemfunktionen zur Rauschüberlagerung dazu beitragen, diese Angriffsfläche zu verkleinern.

## Fazit

Akustische Seitenkanalangriffe auf Tastaturen haben sich von einer Nischen-Forschungsfrage zu einer realistischen Bedrohung entwickelt. Deep-Learning-Modelle wie CoAtNet nutzen hochaufgelöste Audiofeatures, um Tastenanschläge mit beeindruckender Genauigkeit aus Alltagsgeräten wie Smartphones oder Videokonferenz-Streams zu rekonstruieren. Gleichzeitig stehen aber praktikable Schutzmaßnahmen zur Verfügung, die das Risiko deutlich reduzieren können, wenn sie bewusst in den eigenen Sicherheitsalltag integriert werden.

## Quellen

- Harrison, J. et al.: *A Practical Deep Learning-Based Acoustic Side Channel Attack on Keyboards*.
- Dai, Z. et al.: *CoAtNet: Marrying Convolution and Attention for All Data Sizes*.
