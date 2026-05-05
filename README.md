Cruelty School
Name:
Samuel Dolan

Student Number:
A00036207

Class Group:
1st Year game design

Video:
https://youtu.be/ggax8JsRST4

Screenshots

<img width="1080" height="629" alt="image" src="https://github.com/user-attachments/assets/d17f569c-960d-4bd8-850e-ad918ee0f598" />
<img width="1119" height="617" alt="image" src="https://github.com/user-attachments/assets/bb6db901-d969-42bd-81fc-c335a03e8120" />
<img width="1128" height="629" alt="image" src="https://github.com/user-attachments/assets/3580c2cc-f8f5-4474-a093-ac3dc9923a46" />
<img width="1134" height="628" alt="image" src="https://github.com/user-attachments/assets/8cbf70ad-68c0-4a3b-b075-25dfc07030f3" />
<img width="1140" height="637" alt="image" src="https://github.com/user-attachments/assets/830908ab-d1bf-471a-a4f1-1f5a8a0740a2" />
<img width="1135" height="642" alt="image" src="https://github.com/user-attachments/assets/94d88337-ab20-4d06-bf13-8527a7954045" />



Description of the project
This is a small game about a part of my college day. In my game you must go from your starting space, my apartment, through a reimagining of dublin to a bus parked the other side of the map. You will take in the sights and walk, run or jump through the map as you make your way there.

Instructions for use
just click play and have a walk though. take your time, have a look at the variety of textures, patterns and the overall visual identity. eventually you will come accross the bus and maybe youll find the second bus hidden in the map. just walk into it and youll be transported to the end screen.

How it works:
the start menu is linked to 2 things. 1, the start button links to the world scene and lets you actually play and adventure. the second is the quit button which closes the game to the desktop. when you are in the game youll move around with my custom coded character controller which includes jumping sprinting with headbobbing when walking and running and an fov change depending on how fast you go. eventually you will walk to the bus which has a collision and area 3d which is linked to say when you enter it you get transported to the ending scene which unlocks your mouse again to click buttons.you can click on the credits which again links to another seperate scene. you can also press back here which puts you back to the ending scene. and once again you can click quit to end the game.

List of classes/assets in the project
Class/asset	Source
MyClass.cs	Self written
MyClass1.cs	Modified from reference
MyClass2.cs	From reference
References
Item 1
Item 2
What I am most proud of in the assignment
Im actually incredibly proud of the whole thing. i made something i really liked. however if i had to pick one thing it would be the character controller. it took the most time to code and i think it payed off. its very smooth and the bobbing and fov changes are really fun.

What I learned
Through this assignment i believe my ability to code improved tenfold. i didnt really get gdscript before this however aftergoing off on my own and playing with the engine i was able to code things i was proud of and was able to create a short experience that i love.

Character Controller:

	extends CharacterBody3D

	var speed
	const WALK_SPEED = 5.0
	const SPRINT_SPEED = 8.0
	const JUMP_VELOCITY = 4.5
	const SENSITIVITY = 0.003

	#bob vari
	const BOB_FREQ = 2.0
	const BOB_AMP = 0.08
	var t_bob = 0.0

	#fov variables
	const BASE_FOV = 75.0
	const FOV_CHANGE = 1.5

	@onready var head = $head
	@onready var camera = $head/Camera3D

	func _ready():
	Input.set_mouse_mode(Input.MOUSE_MODE_CAPTURED)

	func _unhandled_input(event):
	if event is InputEventMouseMotion:
		head.rotate_y(-event.relative.x * SENSITIVITY)
		camera.rotate_x(-event.relative.y * SENSITIVITY)
		camera.rotation.x = clamp(camera.rotation.x, deg_to_rad(-40), deg_to_rad(60))

	func _physics_process(delta: float) -> void:
		# Add the gravity.
		if not is_on_floor():
			velocity += get_gravity() * delta

	
		# Handle jump.
		if Input.is_action_just_pressed("jump") and is_on_floor():
			velocity.y = JUMP_VELOCITY

		#handle sprint
		if Input.is_action_pressed("sprint"):
			speed = SPRINT_SPEED
		else:
			speed = WALK_SPEED
		
		# Get the input direction and handle the movement/deceleration.
		# As good practice, you should replace UI actions with custom gameplay actions.
		var input_dir := Input.get_vector("left", "right", "up", "down")
		var direction = (head.transform.basis * Vector3(input_dir.x, 0, input_dir.y)).normalized()
		if is_on_floor():
			if direction:
				velocity.x = direction.x * speed
				velocity.z = direction.z * speed
			else:
				velocity.x = 0.0
				velocity.z = 0.0
		else:
			velocity.x = lerp(velocity.x, direction.x * speed, delta * 2.0)
			velocity.z = lerp(velocity.z, direction.z * speed, delta * 2.0)

		#head bob
		t_bob += delta * velocity.length() * float(is_on_floor())
		camera.transform.origin = _headbob(t_bob)
	
		#FOV
		var velocity_clamped = clamp(velocity.length(), 0.5, SPRINT_SPEED * 2)
		var target_fov = BASE_FOV + FOV_CHANGE * velocity_clamped
		camera.fov = lerp(camera.fov, target_fov, delta * 8.0)
		move_and_slide()
	
	func _headbob(time) -> Vector3:
		var pos = Vector3.ZERO
		pos.y = sin(time * BOB_FREQ) * BOB_AMP
		pos.x = cos(time * BOB_FREQ / 2) * BOB_AMP
		return pos


Menu code:
	extends Node2D

	func _on_start_pressed() -> void:
		get_tree().change_scene_to_file("res://World.tscn")


	func _on_quit_pressed() -> void:
		get_tree().quit()
	
Bus Code:

	extends Area3D



	func _on_body_entered(body):
		if body.is_in_group("Players"):
			get_tree().change_scene_to_file("res://END.tscn")
		print("ENTERED")
		print(body.name)

	
