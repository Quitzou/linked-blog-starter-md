---
topic: "[[reinforcement learning.base]]"
related:
  - "[[Autoencoder]]"
  - "[[overview of mapping MoCap to Robot]]"
date: 2026-04-29 09:42
tags:
---

# Paxisbericht

erstmal mocap daten nach daten durchschauen
was hat digit für gelenke, was ist sinvolles mapping für loss

zu 3. alle gerade aus laufen lassen (oder nicht in globalem koordinatensystem)
foot contacts = welcher fuß hat bodenkontakt

# Datensatz

![[Pasted image 20260429173402.png]]

- 100Hz
- in mm
- xyz von jedem Punkt; hip, knee, ankle: deg, force, moment, power
- Metadaten in metadata.xlsx (Längen, distanzen ...)
- https://springernature.figshare.com/articles/dataset/3D_motion_analysis_dataset_of_healthy_young_adult_volunteers_walking_and_running_on_overground_and_treadmill/25592865/1?file=45621447
- Daten lesen: df = pd.read_csv('tests/test.csv', skiprows=13, nrows=792)
- gibt auch Joint positions?
- Normalisieren mit teilen durch Körpergröße?
![[Plug-in_Gate_bones1.png]]
![[Plug-in_Gate_bones2.png]]
![[Plug-in_Gate_bones3.png]]
- https://help.vicon.com/download/attachments/11378719/Plug-in%20Gait%20Reference%20Guide.pdf

## Normalisierung

### a) Einfach: 
alle Pos / Körpergröße
### b) Template Skeleton:
- Einheit: bodysize \[bs]
	- Gesamtgröße = 1
	- Längen von Körperteilen -> Proportionen
	- Geschwindigkeit: bs/s
	- für gegebene Größe dann umrechenbar, mit * Körpergröße

Berechnung: 
- nparray mit alten und eins mit neuen Koordinaten
- nächste Joint Positionen normalisieren mit: $$ (joint - pelv) * proportion / boneLength$$ 
- Rotation 
- Positionen parent joint -> joint normalisieren 
	- Positionen pelv -> joint = pelv -> parent + parent -> joint

In aktueller implementierung:
- komisch, dass vorzeichen von RHJC und LHJC in allen dim negativ
# Poetry

- Pakete herunterladen: poetry add pandas

# Fragen Manuel

- Speicherstruktur sequenzen: in einem Ordner? Im Project zu groß?
- was danach als todo?
	- was genauer aufbau?
- als Loss: Joint Positions von Digit und normalisiertem Mensch vergleichen (außer Knie)?
# Arbeitszeit

- 28.04. -- 9:30 - 12:00, 13:00 - 17:30
	- research zu autoencoder und attention layer
	- studi Jour Fixe
	- setup von obsidian für doku
- 29.04. -- 9:15 - 12:00, 13:00 - 18:00
	- Absprache mit Manuel zum Projekt und Aufgaben für die ersten Wochen (im git)
	- PC setup 
		- Ubuntu, Datensatz, Obsidian
	- erster Blick über Datensatz, ist schon vorsortiert in einzelnen Files, globales Koordinatensystem, Fußkontakt und lösen hinterlegt, 100Hz, keine Rotationen -> nur xyz, Start und Länge der Aufnahme
- 30.04. -- 9:45 - 12:00, 13:00 - 17:30
	- poetry setup angefangen, venv mit ersten requirements geschrieben
	- vs code setup (Problem mit Keyring)
	- Problem mit Obsidian gefixt
	- python excel tut , aber noch nicht fertig
- 04.05. -- 10:45 - 12:15, 12:45 - 17:30
	- weiter durch Datensatz + Doku, git pushes/ pulls, csv reader weiter
- 05.05. -- 10:20 - 13:00, 13:30 - 17:30
	- vor allem wichtige Daten im dataset zusammengetragen und im csv reader diese in den output übernommen
	- angefangen nicht präsente Daten zu berechnen 
- 06.05. -- 9:40 - 12:20, 14:20 - 17:30
	- csv_reader: contacts, gate_phase
	- gitlab + bericht + stundenzettel setup
- 08.05. -- 10:00 - 11:45, 12:45 - 17:00
	- velocities fertig, angefangen workflow dataset -> tensor, gedanken normalisierung: einfach/ genau? + umsetzung
- 11.05. -- 11:00 - 12:00, 13:00 - 17:30, 20:00 - 22:00
	- v.a. Visualisierung, Transformation und Normalisierung 
	- Präsi für Jour Fixe
- 12.05. -- 9:30 - 12:00, 13:00 - 14:30
	- Jour Fixe
	- sequenzen segmentieren, planung für Laden 
- 13.05. -- 10:00 - 12:00, 14:00 - 18:00
	- Laden aus einem Ordner -> Jax Tensor
	- Todo's mit Manuel abgesprochen (weitere Notwendigkeiten für Tensor)
- 18.05. -- 11:45 - 14:45, 15:30 - 17:30
	- zwischen strike und off stance swechsel gesetzt (für col StanceFoot und GaitPhase)
	- Anfang vor erstem event gelöscht
	- normierte zeit col (0 = RightStance, 0,5 = LeftStance, 1/0 = RightStance)
- 19.05. -- 10:45 - 12:00, 12:30 - 17:00
	- transformation in stance foot angefangen
		- fehler generell bei transformation aufgefallen: Winkel sind lokal und müssen nicht transformiert werden -> fehlende Orientierung
			- für Pelvis transformation nicht relevant, da da nur normiert wird
			- für stance foot transformation muss orientierung auch transformiert werden
				- dafür aus markern rotationsmatrix bauen (Trello)
	- in Plot stance foot und norm time eingebaut
# Quellen

 - anomaly detection with LSTM [Autoencoder]: https://dl.acm.org/doi/pdf/10.1145/3416013.3426457?__cf_chl_tk=fT.pJzxyMvxGBeRbMvMG2I0OT66WjWlLp3Vj0bSAoS0-1777448132-1.0.1.1-3lTNYQHoZ23JlIp0df1C4v3dCpy6JrRLT7ruQiDMbdI
	 - [[Autoencoder]] trained on normal data
	 - if error high -> anomaly 
	 - + trained OC SVM (One-class Support Vector Machine) on latent vector (faster + better separated) for classification