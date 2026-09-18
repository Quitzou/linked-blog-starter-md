
## constants

TOE_COUPLING = {

'left': dict(pitch_gain=-1.0551, roll_gain=3.3657,

pitch_range=(-0.7471, 0.5724), roll_range=(-0.6251, 0.5717)),

'right': dict(pitch_gain=-1.0551, roll_gain=3.3657,

pitch_range=(-0.5724, 0.7471), roll_range=(-0.5717, 0.6251)),

}

## test RL runs
**pc in name = parameter chacnge**

090913:  
free_joints: tuple[str, ...] = ("toe",)
actuated_toes: bool = True
toe_kp_scale: float = 0.50
toe_kd_scale: float = 2.0
k_joint_vel: float = 0.1
k_site_pos: float = 40.0 
k_lin_vel: float = 4.0
k_ang_vel: float = 4.0
k_upright: float = 5.0
k_base_height: float = 20.0 

füße schön flach, dadurch aber beim schwingen bleibt linker fuß im boden hängen
-> fuß kontakt als loss? / fuß höhe wenn kein kontakt als reward? oder 
sonst params reward unschärfer gemacht, dadurch nicht so unnatürlich steife hüfte


090916:
neue traj mit flachen füßen getestet
schrittlänge scheint etwas weit zu sein, kommt zu sehr schnellen bewegungen
könnte auch noch an mapping von ferse auf fuß hinten liegen (besser vllt auf mitte)

![[Screencast From 2026-09-09 17-10-01.mp4]]

## test retargeting runs

090916:
![[Screencast From 2026-09-09 16-28-21 1.mp4]]
der bounce kommt davon, das der hintere fuß beim abdrücken nach unten geht, vermutlich weil der menschliche fjc marker beim auf den vorderfuß rollen nach unten geht