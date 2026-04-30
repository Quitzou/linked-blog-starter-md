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
- xyz von jedem Punkt
- Metadaten in metadata.xlsx (Längen, distanzen ...)
- https://springernature.figshare.com/articles/dataset/3D_motion_analysis_dataset_of_healthy_young_adult_volunteers_walking_and_running_on_overground_and_treadmill/25592865/1?file=45621447
- keine beschleuigungen -> selber berechnen
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
# Quellen

 - anomaly detection with LSTM [Autoencoder]: https://dl.acm.org/doi/pdf/10.1145/3416013.3426457?__cf_chl_tk=fT.pJzxyMvxGBeRbMvMG2I0OT66WjWlLp3Vj0bSAoS0-1777448132-1.0.1.1-3lTNYQHoZ23JlIp0df1C4v3dCpy6JrRLT7ruQiDMbdI
	 - [[Autoencoder]] trained on normal data
	 - if error high -> anomaly 
	 - + trained OC SVM (One-class Support Vector Machine) on latent vector (faster + better separated) for classification