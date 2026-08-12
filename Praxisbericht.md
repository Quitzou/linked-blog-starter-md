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
- https://help.vicon.com/download/attachments/11378719/Plug-in%20Gait%20Reference%20Guide.pdf
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
- 06.07 -- 12:30 - 13:45 -- 14:30 - 18:45 -- 23:00 - 23:45
	- plots grid und größere Schriftart
	- foot contacts anzeigen
	- mit unterschiedlichen losses getestet (v.a. statt sum mean, statt m mm)
		- kein sichtbarer unterschied und kein unerwarteter loss 
	- test loss zum plot hinzu
	- beine verlängert von nature human
	- test mit gewichteten fehlern (elbow und foot weniger gewichtet)
		- klappt ganz gut
	- JourFixe präsi
- 07.07. -- 9:40 - 12:15 -- 13:30 - 17:00
	- JourFixe
	- tests mit alter Schulter:
		- bei einem Sample waren Schulter und Hüfte etwas eingedreht
		- muss Ausrichtung nochmal angepasst werden??
		- Schulter normalerweise teils leicht vor digit schulter
	- Stride = Frames gesetzt, dafür doppelte probanten, nur minimal schlechterer loss von 7e-5 zu 9e-5
	- andere paddings getestet: mit symmetric definitiv weniger wackeln
		- jetzt linear fortgeführt, deutlich besser, loss auch wieder bei 8e-5
	- Fuß ist bei bodenkontakt nicht gleichmäßig, durchschwingender fuß würde Boden berühren
		 ![[Screenshot From 2026-07-07 16-48-49.png|308]]
	- bei extremen "Armschwingern" teils sehr knapp mit Armen am Körper, habe aber noch kein retargeting mit Kontakt gesehen
		- ![[Screenshot From 2026-07-07 16-47-55.png|312]]
	- viewer beim vergrößern sehr laggy, ui geht aus dem bild und extrem wenig frames
- 08.07. -- 13:00 - 18:20
	- Loss Plot auf log scale umgestellt
		- darin zu erkennen in config mit 100 frames, 100 stride -> 425\*32 sequenzen:
			- ein trainingsbatch ausreißer:
				- ![[Pasted image 20260708134924.png|533]]
	- Aufgabe: erkennbar machen, welche sequenz zu welcher Aufnahme gehört
		- + test probanten unabhängig
		- daraus auslesen, wer schwerster probant ist, sowie anzeigen, welcher gerade geplottet wird
		- Erkenntnis aus den schlechtesten 10: in den meisten schlechten Sequenzen, wurde die Hand an den Kopf genommen (vermutlich in die Haare):
		- ![[Pasted image 20260708175151.png|225]]
		- eine war allerdings sehr komisch und braucht nähere betrachtung im preprocess, Arme waren im Körper, im video ist aber nichts verdächtiges zu sehen:
			- HN021/Session2/Overground_Walk/Walk_Fast/Post_Process/Walk_Fast3.csv
			- Arme scheinen vertauscht zu sein, im csv sind RFJC(x) > LFJC(x), aber RHANDO(x) < LHANDO(x)
			- am nächsten Tag ohne Human > Digit shoulder mapping ist es klar, Arme falsch gelabelt:![[Screenshot From 2026-07-09 11-48-03.png|145]]
- 09.07. -- 11:20 - 13:00 -- 13:45 - 17:15
	- Idee: echte Frames als Padding nehmen: Problem bei zweiter und folgenden Schichten im Netz
		- test mit nur bei erster layer: Padding entfernt, als input 2 frames mehr
	- Evaluation zu Ausreißern, Loss nach ln und slow/ fast training:
		- ![[Pasted image 20260709140526.png|230]] ![[Pasted image 20260709124006.png|234]]
		- 3\*IQR scheint gute Grenze zu sein
		- beim anschauen von grenznahen sequenzen fällt bei fast train folgende aufnahme auf: dataset/nature/Data_Run_Walk/HN021/Session2/Overground_Walk/Walk_Fast/Post_Process/Walk_Fast6.csv
			- Arme sind ungewöhnlich weit hinten, ist das Problem vom preprocess?
				- Antwort: Armbewegung von Probant bei walk fast ungewöhnlich weit hinten/ bei treadmill schneller schwung kurz vor körper + leichte neigung Oberkörper verstärkt effekt nach preprocess
					- von 9/10 sequenzen aus 2. max solche 
				- nach test ohne HN021 mit slow/ fast train:
				- ![[Pasted image 20260709154923.png|252]]![[Pasted image 20260709160018.png|252]]
	- Idee: als nächstes sequenzen über grenze raus, dann Batches neu erstellen und trainieren
		- auch noch mehr statistiken printen, wie % über grenze
		- sowie sequenzen um grenze o.ä. plotten
			- fertig
	- Problem: bei schnellen Gehbewegungen teils Oberkörper nach vorne gelehnt!
		- Idee: Oberkörper lehnen trainieren?? kann der z.B. Pelvis angle angepasst und trainiert werden??
	- Frage: Lieber wegfiltern oder neu machen?
	- Test: Schultern nur x und z pos setzen 
- 10.07. -- 13:00 - 15:00 -- 15:30 - 17:30
	- komischer bug, zweiter Frame jump der hand und elbow:
		- ![[Pasted image 20260710131327.png|369]]
		- war ein array aufruf fehler
	- alle plots in utils überführt + universell gemacht + schöner mit etwas mehr infos
- 15.07. -- 11:45 - 12:15 -- 13:15 - 16:15
	- model kann jetzt gespeichert und geladen werden
	- Test mit Pretraining: TRAIN VS TEST
		- ![[Pasted image 20260715143850.png|273]]![[Pasted image 20260715143914.png|274]]
		- ![[Pasted image 20260715144131.png|548]]
	- Test ohne Pretraining: TRAIN VS TEST
		- ![[Pasted image 20260715144511.png|279]]![[Pasted image 20260715144541.png|282]]
	- neues Projekt: apg learning mit imitation learning:https://github.com/google-deepmind/mujoco/blob/main/mjx/training_apg.ipynb
		- erste Infos:
			- Unterschied Zeroth-Order Policy Gradients (ZoPG) vs First-Order Policy Gradients (FoPG)
				- ZoPG: berechnet direkten gradient der simulations funktion f
					- algorithms: Proximal Policy Optimization (PPO)
				- FoPG: berechnet gradienten mit der Jakobi von f
					- algorithms: Analytical Policy Gradients (APG), Backpropagation through Time (BPTT)
- 16.07. -- 11:45 - 13:00 -- 14:15 - 16:00
	- RL übersicht erstellt: [[Reinforcement Learning]]
- 17.07. -- 11:40 - 12:45 -- 13:30 - 17:15
	- erste basics von gymnasium eingelesen
	- schreibe kleines RL projekt
		- fehlt noch optimierung der lernfunktion
- 20.07. -- 11:15 - 12:15 -- 13:10 - 16:00 -- 17:30 - 18:00
	- Ist es möglich das Netz so trainiert zu lassen, und als reference kinematic eine gate phase von bestem mapping zu nehmen, die dann periodisch zu einem Laufen wird?
		- Idee: das als reference tracking für RL nehmen
			- Notes: 
	- unterschiedlicher reward: hand/ elbow reference-tracking, fuß direction tracking? und Auflagefläche bei contact = 0, sonst spline bis max pos
	- Kleines RL Beispiel: actions: nach oben/ unten bewegen, observation: y pos, target sine wave folgen
- 21.07. -- 10:15 - 12:15 -- 13:15 - 16:10
	- Jour Fixe + Überlegungen für zukunft
- 22.07. -- 11:15 - 13:00 -- 13:45 - 17:20
	- angefangen Szene für Digit + gravity
		- brauche gelenkwinkel als trajektorie
		- erstmal als hack: einen menschlichen raussuchen, diesen retargeten und als trajektorie abspeichern (Motorwinkel und Joint Pos)
			- potentielle:
			- legs spread: dataset/nature/Data_Run_Walk/RC023/Session2/Treadmill_Walk/Treadmill_Walk_Fast/Post_Process/Treadmill_Walk_Fast.csv
			- comft: dataset/nature/Data_Run_Walk/MR015/Session2/Treadmill_Walk/Treadmill_Walk_Comfortable/Post_Process/Treadmill_Walk_Comfortable.csv
			- spread: dataset/nature/Data_Run_Walk/LL011/Session2/Treadmill_Walk/Treadmill_Walk_Fast/Post_Process/Treadmill_Walk_Fast.csv
	- anderen start wählen, um beine zusammen zu haben (zwischen off und strike)
		- mit anderem net: Number 6990: dataset/nature/Data_Run_Walk/MR015/Session1/Overground_Walk/Walk_Comfortable/Post_Process/Walk_Comfortable4.csv
	- Funktion um trajektorie zu extrahieren:
		- motor_seq -> sitepos + siteids -> save
	- speichern und laden einer trajetktorie (motorwinkel, Joint Pos)
- 23.07. -- 10:40 - 12:40 -- 13:30 - 17:20
	- forward step -> viewer
	- frequenz viewer: -- frequenz trajektorie: 100Hz
	- zum testen regelkreis: target angle - cur angle \* kp + d(target angle - cur angle) \* kd
		- bein scheint falsch zu sein (seitliches schwingen) -> lag an gear ratio -> damit skaliert hat funktioniert
	- als nächstes kleines netz trainieren: frame als input -> mj_data.ctrl als output
	- Idee: wenn aktion laufen, dann schauen welcher frame am nächsten, und von dem an laufbewegung machen
	- Anfang fertig: RL_training:
		- zu trajektorie loss berechnen (angles)
		- trajektorie + Roboter plotten
		- fehlt: lernen fertig machen
- 24.07. -- 11:40 - 13:30 -- 14:15 - 16:20
	- training funktioniert nicht sinnvoll, ein step dauert extrem lang: 100 frames \* 20 substeps 
		- und nur miniminimale verbesserung (eher random)
	- andere idee: winkel auf vorigen frame setzen, und dann einen step trainieren
		- oder besser: winkel um gewollten winkel setzen und dann trainieren
		- mit training von gesampelten einzelnen frames: ![[Pasted image 20260724162000.png|212]]
			- loss pendelt von anfang an um 0.24
	- ergebnis: training läuft durch, es wird aber noch nichts trainiert
- 28.07. -- 11:10 - 12:15 -- 13:00 - 17:00
	- Problem war unter anderem, dass gelenkwinkel nicht gesetzt wurden
		- allerdings nicht so einfach, da ich die gekoppelten gelenke nicht herleite, könnte fehleranfällig sein, diese herzuleiten
		- wird so nicht funktionieren, nach vielen implements immer noch komplett instabil
			- also ohne setzen von pos, sondern von startpos aus trainieren und ganze bewegung trainieren
	- neuer ansatz: ein step in train ist ein target+ ein ctrl -> 20 step sims
		- ausgangspunkt ist startzustand für nächsten step
		- parallelisierte trajektorien (starten unterschiedlich)
		- funktioniert insgesamt zumindest im ansatz
			- ![[Pasted image 20260728164727.png|302]]![[Pasted image 20260728164806.png|173]]
- 29.07. -- 11:45 - 13:00 -- 13:30 - 17:00
	- observation space: no ground contact: joint angle(20), joint vel(20), last act(20),
		- ground contact: + IMU
	- target: joint angle(20), 
	- selber rumprobieren an vereinfachtem problem war nicht zielführend
	- Code generiert zu richtigem training basieren auf trajektorie und model
		- muss noch reviewed und getestet werden
			- auf ersten blick scheint reward sich zu erhöhen
		- weitere tests mit video sehen gut aus: nach 10_000_000 steps(45 min):
			- ![[video_14_0010.32M.mp4]]
			- nächste wichtige änderungen: bessere Trajektorie nutzen:
				- dafür sequenzen auf eine gate phase bringen und mittlere trainingssequenz als referenz nutzen
				- außerdem fußtracking besser machen (richtige orientierung + fuß flach?)
- 30.07. -- 11:10 - 13:00 -- 13:40 - 17:10
	- notes zu next steps: 
		- preprocess auf gait phase umstellen
			- mittlere gait länge als num_steps wählen, andere dahin interpolieren
			- retargeting -> mittlerer retargeting loss als referenztrajektorie
		- Als test: Fuß-Winkel nicht in RL training
		- preprocess foot loss neu
			- alte idee von menschfuß immer flach auf boden?
		- training besser?
			- ja -> residual training mit rauschen
				- besser? -> störungen
		- Später: Training mit unterschiedlichen actions als input?
			- dadurch ändert sich ziel, z.b. körperachse mit vel phi drehen und cos(phi) vel nach vorne --> unterschiedlich geschwindigkeit mit aktuellem ansatz nicht möglich (evtl trainierbar nach training und ohne trajektorie bzw walk slow)
	- Analyse Framenumber von Gait Cycle (slow, comf und fast):
	- ![[Pasted image 20260730150743.png|311]]
	- und aufgeteilt: Fast -- Comf -- Slow
	- ![[Pasted image 20260730150932.png|208]]![[Pasted image 20260730151105.png|208]]![[Pasted image 20260730151149.png|209]]
	- es entstehen sehr eindeutige peaks, die als zentrum von ausgewählten sequenzen dienen könnten. damit können trainingsdaten für unterschiedliche geschwindigkeiten gesammelt werden (Schrittweite kann noch variieren, sollte im mittel aber den frequenzen entsprechend sein)
	- für erste tests sollte der peak bei 109 oder 120 sinnvoll sein
		- was sind dann sinnvolle bereiche?
		- allererster test nur welche von der genauen framenumber?
			- code dafür noch nicht überprüft
- 04.08. -- 9:50 - 12:15 -- 13:15 - 16:50
	- fußproblem ist vorallem, im retargeting wird toe-a und toe-b gesetzt, allerdings haben die keinen einfluss nach forward auf wahre fußposition
		- nach ergänzung fällt auf, dass fuß/ bein unterbestimmt sind im retargeting:
		- ![[Pasted image 20260804112930.png|122]]
		- loss erweitert auf foot_roll = 0, dadurch wieder gut:
		- ![[Pasted image 20260804121610.png|145]]
	- [ ] Becken Rotation rausrechnen (im retargeting)
		- tests mit FAST -- COMF -- SLOW mit smoke (2M Steps)
			- FAST mit ca 1.3 m/s scheint zu schnell, SLOW mit 0.8 m/s zu langsam, rund 1 m/s besser
			- aber becken rotation scheint zu fehlen für vorwärtsmoment
				- extremste tilts im vergleich ohne tilt-rot: 2.4° (DA013) zu 14.5° (GF022)
		- ![[training.png|263]]![[training 1.png|263]]
			- im smoke test sieht man keinen Unterschied, das heißt, gelenke haben wenig mit körperneigung zu tun, in diesem beispiel eher unterschiedliche körperproportionen
	- [x] gelenkgrenzen in netz einbauen (tanh * jnt_range)
	- [x] beinlängen neu berechnen
		- nicht geändert, passt aber fürs erste
	- [x] erweiterter foot loss
		- foot-roll als target auf 0 gesetzt
	- [ ] fuß loss neu im retargeting (flach setzen?)
		- gerne ferse+flach(z von ferse und toe auf min(ferse, toe))
	- [ ] gespeicherte trajektorie anzeigen

	- Probant HN021 hat auffälligkeiten in torsowinkel über die 2 sessions: 7.2° vs. 12.3°  (5.1°) wahrscheinlich unterschiedliche markerpositionen, bei allen anderen probanten konsistent (0.88° streuung)
		- HN021 aus datensatz raus (war schon länger auffällig)
- 05.08. -- 10:00 - 12:00 - 12:45 - 16:45
	- 10M trainingstest mit 1m/s trajectory (median of 120 frames gait cycles)
		- ![[training 2.png|278]]
		- episodenlänge ausgeschöpft, training geht in plateau, allerdings auch am ende noch beim laufen einen linksdrift -> in reward?
	- Code mit [Schaubild](https://claude.ai/code/artifact/a8520cfc-1d42-48bc-8b00-8beadbce85b4) verstehen
	- Paper zu DeepMimic angefangen zu lesen, sowie eigene [übersicht](Git/DeepMimic) mit Fragen angefangen (bis Multi-Skill Integration)
- 06.08. -- 10:30 - 12:15 -- 13:15 - 17:40
	- Tipps von Manuel:
		- center of mass anschauen
		- passive füße dregler
		- Lectures:  
			- David Silver: https://www.youtube.com/watch?v=2pWv7GOvuf0
			- DeepMind: https://www.youtube.com/watch?v=TCCjZe0y4Qc
			- PPO Pseudocode: https://spinningup.openai.com/en/latest/algorithms/ppo.html
			- PPO: https://openai.com/index/openai-baselines-ppo/
	- erste zwei videos von David Silver geschaut + notes in [[Reinforcement Learning]] gemacht
- 07.08. -- 11:30 - 13:15 -- 14:15 - 17:00
	- weiter videos von David Silver geschaut
- 11.08. -- 10:30 - 12:30 -- 13:00 - 17:15
	-  video 5 und 6 bis 1:23
- 12.08. -- 10:30 - 12:30 -- 13:00 - 
	- video 6 und 7
	- blackjack training gymnasium 
	- angefangen cartpole

# Quellen

 - anomaly detection with LSTM [Autoencoder]: https://dl.acm.org/doi/pdf/10.1145/3416013.3426457?__cf_chl_tk=fT.pJzxyMvxGBeRbMvMG2I0OT66WjWlLp3Vj0bSAoS0-1777448132-1.0.1.1-3lTNYQHoZ23JlIp0df1C4v3dCpy6JrRLT7ruQiDMbdI
	 - [[Autoencoder]] trained on normal data
	 - if error high -> anomaly 
	 - + trained OC SVM (One-class Support Vector Machine) on latent vector (faster + better separated) for classification