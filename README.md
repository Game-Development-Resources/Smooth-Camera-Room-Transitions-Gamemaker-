# Smooth-Camera-Transitions-Gamemaker-

The code below works in Gamemaker.

I made an algorithm for one of my games that makes the camera snap to the height of the view in the room with smooth transition in between (imagine shovel knight but without the game being paused in between rooms, and the player always has full control over the character). The script below works for y values, but you could easily modify this for x values as well. I tried to write out an explanation for how the code works, but the algorithm is somewhat complicated.

	t = #; //transition height
	v = view_hview[0];
	if y mod v > v - t or y mod v < t {
		var snap = ((y - v/2) div v)*v;
		view_yview[0] = snap + (((t + y - (snap + v)) / (t*2))*v);
	}
	else {
		view_yview[0] = (y div v)*v;
	}

The only variable you have to set yourself is the transition height.

For x values, you'll want to replace the following:

* y -> x
* view_hview[0] -> view_wview[0]
* view_yview[0] -> view_xview[0]