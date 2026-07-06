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

![[Pasted image 20260429173402.png|461]]

- 100Hz
- in mm
- xyz von jedem Punkt; hip, knee, ankle: deg, force, moment, power
- Metadaten in metadata.xlsx (Längen, distanzen ...)
- https://springernature.figshare.com/articles/dataset/3D_motion_analysis_dataset_of_healthy_young_adult_volunteers_walking_and_running_on_overground_and_treadmill/25592865/1?file=45621447
- Daten lesen: df = pd.read_csv('tests/test.csv', skiprows=13, nrows=792)
- gibt auch Joint positions?
- Normalisieren mit teilen durch Körpergröße?
![[Plug-in_Gate_bones1.png|443]]
![[Plug-in_Gate_bones2.png|445]]
![[Plug-in_Gate_bones3.png|447]]
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

<<<<<<< HEAD

# Loss calc checklist

- [x] motor id's
- [x] set and foreward motor pos
- [ ] get endeffector pos 
- [ ] calc diff between pos
# Fragen

- robot model description als vergleich
- weiter an csv reader/ writer? 
=======
# Fragen Manuel

- als Loss: Joint Positions von Digit und normalisiertem Mensch vergleichen (außer Knie)?
	- origin/main

# Notes

- bone scaling
	- dafür erstmal viele sequenzen schnell hintereinander plotten, um unterschied zu sehen
	- dann digit modell anschauen um pelvis pos rauszulesen und pelvis wirklich als ursprung zu nehmen
		- im modell war torso verschiebung, dadurch pelvis zu hoch
	- jetzt bonescaling finetunen
		- schon besser
	- jetzt mal elbow als target versuchen
		- nicht wirklich möglich perfekt zu skalieren, da schulter höhe etwas variiert, dadurch lässt sich elbow nicht gut fitten
- was genau endziel vom retargeting? womit kann ich dann weiter machen?
- 
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
- 20.05. -- 10:30 - 12:00 -- 13:00 - 17:30
	- beim rotieren um neues foot coordinate system: chaos in z richtung, nach z Ausrichtung nach Labor z -> rotation quasi keine auswirkung, nur richtung nicht mehr gespiegelt bei manchen Aufnahmen, dann aber besser gespiegelte finden, und dann -x und -y 
	- matplotlib kennen lernen und erweiterbaren gait phase plot
- 22.05. -- 10:15 - 12:30 -- 13:30 - 15:00 -- 15:30 - 17:30
	- zuerst Laufphasen implementiert
	- dann mit Manuel neue Aufgaben geklärt
	- dann Gedanken zu Fuß an Digit anpassen
		- könnte zwei Marker für Fuß nehmen, dann lowest z als z und Knie Position in Plane neu berechnen
	- für Jour Fixe:
		- am Anfang über neue Implementationen reden
		- neuen Plot zeigen?
		- am Ende Gedanken zu Fuß neu mappen darstellen
- 25.05. -- 16:00 - 18:00
	- Jour Fixe Vorbereitung
- 26.05. -- 9:45 - 12:00 -- 13:00 - 17:30
	- Jour Fixe
	- JAX verstehen, NN zur mnist erkennung bauen
- 27.05. -- 10:10 - 12:00 -- 13:00 - 18:00
	- autoencoder bauen
	- struktur für richtige pipeline
- 28.05. -- 11:00 - 12:00 -- 12:45 - 17:00
	- autoencoder auf sinuskurven umbauen 
		- finetunen und plotten
	- von Kevin code von seiner pipeline 
- 29.05. -- 14:00 - 16:00
	- bisschen in mujoco eingelesen
- 01.06. -- 12:00 - 13:00 -- 13:45 - 17:30
	- preprocess code aufräumen
	- Struktur Autoencoder recherchieren 
		- wenn z(100, latent_dim) verliert zeitaspekt?
	- batches: ggf später noch shuffeln
	- speichern und laden von jnp.arrays, format nach laden noch nicht ganz richtig
- 02.06. -- 11:30 - 13:45 -- 14:30 - 17:00
	- mit kevin git project aufgeräumt ->poetry raus
	- laden von gestern gefixed
	- processen von den csv daten direkt aus dem datensatz
		- bei Laden und verarbeiten von drei Probant\*innen bereits speicherproblem beim tensor von größe (3873, 32, 100, 133)
			- 7.90GiB von 6.14GiB
				- auch bei (944, 32, 123, 133) (stride=4) problem mit 1.84GiB
			- fix als numpy array speichern und dann batches einzeln als jax array
			- vom dritten probant wurde walkfast8 raus genommen
- 03.06. -- 11:15 - 12:45 -- 13:45 - 17:30
	- mujoco modell von Kevin 
		- erste simulationen
		- erste winkelanpassungen 
		- mjx modell funktioniert noch nicht, wegen kollision
- VERLETZUNGSPAUSE
- 17.06. -- 11:30 - 12:30 -- 13:15 - 17:00
	- VPN
	- Überblick über das Projekt
	- auf alle motoren winkel geben + bspw. echte daten zum loss berechnen holen
		- --> jetzt fehlt noch nach forward ee_ids und damit ee_pos zu bestimmen
- 18.06. -- 11:30 - 14:15
	- positionen sind bestimmbar, allerdings model sehr klein -> bone_length anpassen?
	- genaues messen der segmente schwierig, da unklar welche position sinnvoll ist, geschätzte längen im stl gemessen
	- noch schwierig die daten sinnvoll zu nutzen und zu berechnen
- 19.06. -- 14:00 - 16:00
- 22.06. -- 11:15 - 12:15 -- 13:00 - 17:30
	- temporal autoencoder
	- komplette trainingspipeline aufgebaut (vor allem loss calc für sequenzen geschrieben)
	- erste trainingsergebnisse (loss reduktion)
		- allerdings bekanntes Problem: Koordinatenursprung nicht übereinstimmend
- 23.06. -- 9:45 - 11:30 -- 12:15 - 16:30
	- jour fixe, manuel hat mir fix für Koordinatenverschiebung gezeigt (im model)
	- viel mit dem viewer und der visualisierung der ee's 
		- am ende neue sites erstellt, hat semi gut geklappt, evtl noch für alle joints implementieren
		- insgesamt ist die viewer funktion zu unübersichtlich, geht evtl noch effizienter
- 24.06. -- 11:15 - 12:30 -- 13:30 - 17:30
	- nach vielen versuchen klappt training noch immer nicht besser, arme und beine scheinen an einen punkt zu gehen, bei dem im durchschnitt wenig loss ist und wackeln dann zufällig leicht hin und her
- 25.06. -- 11:00 - 12:30 -- 13:00 - 16:15
	- erster erfolg beim training mit nur einer target pos und nur target pos als input
	- zweiter erfolg mit 4 target pos und 4 target pos als input und sehr simples netzwerk von Kevin (einfacher 3 layer AE)
		- allerdings bei mehreren targets sieht es noch nicht gut aus, alles zittert sehr und nicht gerade präzise
		- conv net sind flüssige bewegungen, scheint bei mehreren targets überfordert durch überlagerung??
	- bisschen plot code aufgeräumt
- 29.06. -- 12:00 - 14:15 -- 14:45 - 17:30
	- sachen wie frames reduzieren getestet
	- neue targets wie ankle genommen (elbow muss noch getestet werden)
	- plots erweitern, um ganzen human zu plotten zum debuggen (noch fehlerhaft)
		- schon geschafft: human in eigene xml datei
	- generell motion_retargeting code etwas aufgeräumt, v.a. plot functions in utils
- 30.06. -- 11:20 - 12:30 -- 13:15 - 17:00
	- convnet nochmal getestet und verbessert: 
		- dim bottleneck rausgenommen und padding gestpiegelt, um anfangs und end effekte zu vermeiden
		- funktioniert jetzt viel besser: sehr smoothe bewegungen und recht akkurat
		- nach weiteren tests mit mehr trainingsdaten und diese geshuffled und mehr training nochmal bessere und vorallem viel konstantere ergebnisse
		- aktuelle Probleme: ellbogen teils nach vorne gebogen (ggf ellbogen als target hinzufügen)
			- allerdings größeres Problem: normalisierung oder Koordinatentransformation scheint nicht perfekt zu funktionieren: bei manchen sequenzen sind die schultern zu hoch
		- viele finetuning tests mit knochenlängen und elbow als weiteres target
	-  bone scaling
	- dafür erstmal viele sequenzen schnell hintereinander plotten, um unterschied zu sehen
	- dann digit modell anschauen um pelvis pos rauszulesen und pelvis wirklich als ursprung zu nehmen
		- im modell war torso verschiebung, dadurch pelvis zu hoch
	- jetzt bonescaling finetunen
		- schon besser
	- jetzt mal elbow als target versuchen
		- nicht wirklich möglich perfekt zu skalieren, da schulter höhe etwas variiert, dadurch lässt sich elbow nicht gut fitten
- 01.07. -- 11:30 - 12:30 -- 13:15 - 18:00
	- test: elbow loss weniger gewichten: Problem mit schulterpos und armlänge bleibt bestehen
	- fast mode, um schnell an die plots zu kommen
	- idee: elbow möglichst weg vom pelvis: eher verworfen, wäre meist unnatürlich!
	- experiment: stärkere verzerrung der trainingsdaten: schulter fest auf digit schulter position setzten
		- dafür digit schulterpos über sequenz plotten und gucken, ob gleich bleibt
			- problem, schulter entweder zu nah am körper, sonst durch kinematik bewegt sich die digit schulter nach oben und unten durch roll
		- feste digit schulter als site setzen, dafür auch digit mit null schulter verschiebung ohne bewegung plotten
			- Problem: roll bewegung in natürlicher armschwing phase mitbewegt:
			  ![[Screenshot From 2026-07-01 13-44-16.png|106]]
		- dann genaue oberarm und unterarm längen berechnen (theorie)
		- erstmal auf eis, entstehen mehr fragen als lösungen
	- experiment: statt hand und elbow pos lieber hand elbow gerade vorgeben und dann hand und elbow auf gerade mappen
		- erste idee für ansatz:
			- gerade berechnen
			- in loss einbauen
			- loss plot überarbeiten
		- erste implementierung:
			- beide vektoren bestimmen und cos sim berechnen + hand pos
			- einmal komischer bug, bei test 23 13 war eine target hand über dem kopf 
			- vergleich zu hand und elb pos
				- quasi gleiche trainingszeit
				- keine sichtbare verbesserung, evtl für fuß relevant
		- tests zu optimieren vom netz
			- erst mit anderen kernel und größerem netz
			- notes dazu in einem file
- 02.07. -- 10:20 - 11:40 -- 12:50 - 17:30
	- test mit neuer architaktur: Spatio-Temporal Transformer
		- erste implementierung hat länger trainiert und schlechtere ergebnisse geliefert, mehr zittern, weniger genauigkeit und sehr schlechtes mapping bei einem bein (knie klappt hoch)
		- ![[Screenshot From 2026-07-02 13-12-36.png|221]]![[Screenshot From 2026-07-02 13-13-55.png|95]]
		
	- nochmal ansatz mit fester schulter verfolgen
		- schulter site digit richtig setzen
		- durchschnittliche schulter position aufnehmen
			- ist natürlich abhängig von datensatz und gewählten site pos und bone length, 
				- erste config: links: -0.17755021 -0.05651654  0.41037458 rechts: 0.17717035 -0.05651654  0.4125045
				- abstände: Shoulder-Elbow: 0.3500m Elbow-Hand:     0.3691m
			- besserer loss: 0.0052 zu 0.0025 und meiste ist jetzt im fuß, wobei manche im elbow auch noch schlecht sind, aber nicht mehr in z richtung
			- ![[Screenshot From 2026-07-02 15-15-19.png|109]]![[Screenshot From 2026-07-02 15-17-14.png|89]]
	- Test mit 8 statt 3 Probanten, loss bleibt stabil, allerdings habe ich memory probleme, muss noch weiter untersucht werden, evtl mit split vom tensor und auf festplatte speichern und nacheinander laden
	- *müsste auch nochmal den foot loss anders machen ggf mit cos sim*
- 06.07 -- 12:30 - 13:45 -- 14:30 - 18:45
	- plots grid und größere Schriftart
	- foot contacts anzeigen
	- mit unterschiedlichen losses getestet (v.a. statt sum mean, statt m mm)
		- kein sichtbarer unterschied und kein unerwarteter loss 
	- test loss zum plot hinzu
	- beine verlängert von nature human
	- test mit gewichteten fehlern (elbow und foot weniger gewichtet)
		- klappt ganz gut

# Quellen

 - anomaly detection with LSTM [Autoencoder]: https://dl.acm.org/doi/pdf/10.1145/3416013.3426457?__cf_chl_tk=fT.pJzxyMvxGBeRbMvMG2I0OT66WjWlLp3Vj0bSAoS0-1777448132-1.0.1.1-3lTNYQHoZ23JlIp0df1C4v3dCpy6JrRLT7ruQiDMbdI
	 - [[Autoencoder]] trained on normal data
	 - if error high -> anomaly 
	 - + trained OC SVM (One-class Support Vector Machine) on latent vector (faster + better separated) for classification