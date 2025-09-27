WIP Constructor Tutorial

This is the typical example I give when a GameMaker dev is trying to understand the power and flexability of structs.

Let's start with a basic implementation of HP on a player object, something every GameMaker developer has likely coded before:
```
obj_player:
// Create Event
hp_max = 100
hp = hp_max

// Collision with projectile
hp -= other.damage
hp = clamp(hp, 0, hp_max)

if hp <= 0 {
	instance_destroy() // Or however you want to kill your player.
}
```

Pretty basic and functional. We set a max value, we clamp our values so we don't go above the max, or below 0, and when we are at or below 0 we die.
If we wanted to expand this logic to work with enemies and bosses, maybe we make a parent object, such as ``obj_actor_parent``, and it can inherit the HP and damage logic.

Let's say we want to code other resources that behave similarly, like MP, Rage, Energy, or even something like XP, we'd have to duplicate a lot of the same code, clamping values, etc. Anytime you start a new project, same thing, retreading the same ground coding the same basic systems over and over.

Fortunately for us, constructors let us encapsulate this in a reusable format. Let's give it a try:

First create a Resource constructor, and create an instance of it:
```
function Resource(_max) constructor{
	
}

hp = new Resource(100)
```

Right now this doesn't do anything at all, but we can start envisioning what sort of functionality we would expect any of our resources to have. For HP we might want to:
1. Deplete
2. Restore
3. Check if full
4. Check if empty
5. Set current value
6. Get current value
7. Get current value (percentage)
8. Set max value
9. Get max value

With this in mind, I like to pretend this functionality exists, and start coding how I'd expect it to work:
```
// Create a new instance of "Health", with 100 max HP
hp = new Health(100)

// Remove 25 -> 75/100
hp.deplete(25)
// Add 50 -> 100/100
hp.restore(50) 

if hp.is_full() {
	// We would hit this code block
}

if hp.is_empty() {
	// And wouldn't hit this one
	instance_destroy()
}

if hp.get_percent() <= 15 {
	// Create low HP effect
}

var _hp = hp.get()
var _hp_max = hp.get_max()
show_debug_message($"HP: {_hp}/{_hp_max}")
// Should output: "HP: 100/100"

hp.set(1)
// Now we're at 1 HP
```

Keep in mind, none of this works, none of the logic has been coded, but we've mapped out how we want to be able to use this. Now that we know what functions we are expecting, we can create them:
```
function Resource(_max) constructor{
	value_max = _max
	value     = _max
	
	static deplete = function(){}
	static restore = function(){}
	static set = function(){}
	static get = function(){}
	static set_max = function(){}
	static get_max = function(){}
	static get_percent = function(){}
	static is_full = function(){}
	static is_empty = function(){} 
}
```

We'll ignore the keyword stacic for this tutorial for now. Once we have our functions in place, we can fill in the blank with the logic for each of theses functions:
```
function Health(_max) constructor{
	value_max = _max
	value     = _max
	
	static set = function(_value){
		value = clamp(_value, 0, value_max)
	}
	static get = function(){
		return value
	}
	static deplete = function(_amount){
		set(value - _amount)
	}
	static restore = function(_amount){
		set(value + _amount)
	}
	static set_max = function(_max){
		value_max = _max
	}
	static get_max = function(){
		return value_max
	}
	static get_percent = function(){
		return value/value_max*100
	}
	static is_full = function(){
		return value == value_max
	}
	static is_empty = function(){
		return value == 0
	} 
}
```






