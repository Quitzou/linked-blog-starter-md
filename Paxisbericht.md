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


später wsl für docker?

# Datensatz

- 100Hz
- in mm
- 

# Arbeitszeit


# Quellen

 - anomaly detection with LSTM [Autoencoder]: https://dl.acm.org/doi/pdf/10.1145/3416013.3426457?__cf_chl_tk=fT.pJzxyMvxGBeRbMvMG2I0OT66WjWlLp3Vj0bSAoS0-1777448132-1.0.1.1-3lTNYQHoZ23JlIp0df1C4v3dCpy6JrRLT7ruQiDMbdI
	 - [[Autoencoder]] trained on normal data
	 - if error high -> anomaly 
	 - + trained OC SVM (One-class Support Vector Machine) on latent vector (faster + better separated) for classification