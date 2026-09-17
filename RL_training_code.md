

## Environment

config und reference als input
config ist klasse als mit Initialwerten


## Reward

ist noch in respective zu free joints aufgebaut, joint w = 0 / 1 für free und aktuiert, joint w scale = num joints / num aktuiert um sum auf num joints zu skalieren
am ende durch sum(weights)

penalties:
torque is normalized with max torque

## Thoughts 

### for start

init in standing pos, find best matching reference pos, start reference from there without

maybe gradually increase des vel (over x frames)

### for turning

is command vector global? then can give random commands per environment