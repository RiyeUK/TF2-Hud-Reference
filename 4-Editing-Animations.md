# Editing animations

Animations are a very important part of many huds. The biggest example of an animation is buffed or low health changing color and flashing.

## hudanimations_manifest.txt

The manifest file defines what animations files should be used. In the default hud, this file is simply:
```
hudanimations_manifest
{

	"file"		"scripts/hudanimations.txt"
	"file"		"scripts/hudanimations_tf.txt"
}
```
Where each "file" points to an animation file.

It's worth mentioning this because some huds will add their own animations files.

## hudanimations_tf.txt

Hudanimations_tf.txt is where nearly all animations used in tf2 are stored. It comes with a helpful comment at the top that describes most of what you can do in an animation file:
```
// sample animation script
//
//
// commands:
//	Animate <panel name> <variable> <target value> <interpolator> <start time> <duration>
//		variables:
//			FgColor
//			BgColor
//			Position
//			Size
//			Blur		(hud panels only)
//			TextColor	(hud panels only)
//			Ammo2Color	(hud panels only)
//			Alpha		(hud weapon selection only)
//			SelectionAlpha  (hud weapon selection only)
//			TextScan	(hud weapon selection only)
//
//		interpolator:
//			Linear
//			Accel - starts moving slow, ends fast
//			Deaccel - starts moving fast, ends slow
//			Spline - simple ease in/out curve
//			Pulse - < freq > over the duration, the value is pulsed (cosine) freq times ending at the dest value (assuming freq is integral)
//			Flicker - < randomness factor 0.0 to 1.0 > over duration, each frame if random # is less than factor, use end value, otherwise use prev value
//			Gain - < bias > Lower bias values bias towards 0.5 and higher bias values bias away from it.
//			Bias - < bias > Lower values bias the curve towards 0 and higher values bias it towards 1.
//
//	RunEvent <event name> <start time>
//		starts another even running at the specified time
//
//	StopEvent <event name> <start time>
//		stops another event that is current running at the specified time
//
//	StopAnimation <panel name> <variable> <start time>
//		stops all animations refering to the specified variable in the specified panel
//
//	StopPanelAnimations <panel name> <start time>
//		stops all active animations operating on the specified panel
//
//  SetFont <panel name> <fontparameter> <fontname from scheme> <set time> 
//
//	SetTexture <panel name> <textureidname> <texturefilename> <set time>
//
//  SetString <panel name> <string varname> <stringvalue> <set time>
```

The events themselves look like this:
```
event OpenWeaponSelectionMenu
{
	StopEvent CloseWeaponSelectionMenu	0.0
	StopEvent WeaponPickup				0.0

	// make the display visible
	Animate HudWeaponSelection Alpha 		"128"		Linear 0.0 0.1
	Animate HudWeaponSelection SelectionAlpha 	"255"	Linear 0.0 0.1
	Animate HudWeaponSelection FgColor		"FgColor"	Linear 0.0 0.1
	//Animate HudWeaponSelection TextColor	"BrightFg"	Linear 0.0 0.1
	Animate HudWeaponSelection TextScan		"1" 		Linear 0.0 0.1
}
```

An important technique is to create loops, by using two animations that RunEvent eachother.
```
//Health Bonus Pulse
event HudHealthBonusPulse
{
	Animate	PlayerStatusHealthBonusImage 	Alpha		"255"		Linear 0.0 0.2
	Animate	PlayerStatusHealthBonusImage 	Alpha		"0"			Linear 0.2 0.4

	RunEvent HudHealthBonusPulseLoop	0.4
}

// call to loop HudHealthBonusPulse
event HudHealthBonusPulseLoop
{
	RunEvent HudHealthBonusPulse 0.0
}

event HudHealthBonusPulseStop
{
	StopEvent HudHealthBonusPulse 0.0
	StopEvent HudHealthBonusPulseLoop 0.0
}
```