# Smooth-Camera-Room-Transitions-Gamemaker-

[[ https://i.gyazo.com/d3edc64494349c04d16144c9e6c1d148.gif | height = 200px ]]

[Video of code in action](https://www.youtube.com/watch?v=Mw02Pni0v9Q&index=1&list=PLye9mcKwe2zyYF3ei2xr2YznNDZJxQ_85)

The code below works in Gamemaker.

I made an algorithm for one of my games that makes the camera snap to the height of the view in the room with smooth transition in between (imagine shovel knight but without the game being paused in between rooms, and the player always has full control over the character). The script below works for y values, but you could easily modify this for x values as well.

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

---
Further explanation
-

First of all:

* view_hview is the height of the view in the room
* view_yview is the y position of the view in the room

Let's start with this:

	view_yview[0] = (y div v)*v

First we want to know how many times our view height (v) goes into the y value of our player (y), returning a whole number.

	y div v

We then multiply the whole number by the view height (v) giving us the location we want to snap the view to.

	(y div v)*v
	
Lastly, we set the current view to that location.

	view_yview[0] = (y div v)*v
	
Whenever the player isn't in the transition zone (t), this is what is going on.

So set your the transition zone (t) to some number, say 50. Now, whenever your character gets within 50 pixels of the top or bottom of the view, the smooth transition code will activate using this:

	if y mod v > v - t or y mod v < t

Firstly we want to see what the remainder is of our player's y value (y) divided by the view height (v) to see if:

* the remainder is more than the height of the view (v) minus the transition height (t)
* or
* the remainder is less than the transition height (t)

If either of those conditions are met (meaning the player is in the transition zone on the top or bottom of the screen), then we execute the smooth transition code:

Here's what it looks like as one big algorithm

	((y - v/2) div v)*v + (((t + y) - (((y - v/2) div v)*v + v)) / (t*2))*v

Looks pretty messy huh. Here's what I ended up simplifying it into so as to better understand what was going on.

	snap = ((y - v/2) div v)*v
    view_yview[0] = snap + (((t + y) - (snap + v)) / (t*2))*v
	
Let's break down the new variable we created that was used to simplify the algorithm.

	((y - v/2) div v)*v

You may have noticed that it looks a lot like this one:
	
	(y div v)*v
	
Our new code serves the same purpose, it gives us a reference point for where the view should be, except this time it is offset by half the view height (v). The reason for this is that we want to be able to have a constant reference while jumping from one snap point to another. If we simply used this:

	(y div v)*v

When we reached the bottom of one transition and moved to the top of another (or visa versa), the reference would change. So instead we create the new variable:

	snap = ((y - v/2) div v)*v
	
And we want to know how we can use it to transition from one snap point (v) to another. These snap positions (v) are the end points of a camera movement gradient we want to create, the gradient will be based on the the size of the transition zone (t) and where our player is (y) in that transition zone (t). Here's the whole algorithm again:

	snap + (((t + y) - (snap + v)) / (t*2))*v
	
First off, we have the "snap" variable, which is our reference point, everything after that is just what we're adding to get form the one snap position (v) to the next. So let's take out our first "snap".

	(((t + y) - (snap + v)) / (t*2))*v
	
We can basically break this down into three parts:

where the player is in the transition divided by the transition zone size multiplied by the snap position. With this, we're finding the percent of the transition zone the player has crossed and making the camera mimic that as a percent of the distance between snap points (v).

Percent of transition zone crossed by player:

	((t + y) - (snap + v)) / (t*2)
	
And we just multiply by "v" to set the view to the corresponding percent of the way from one snap position (v) to the next.

Since the transition zone (t) is the radius rather than the full width of the transition zone (t), in order to figure out what percent the player has crossed, we must multiply it by two.
	
	t*2
	
Now we are just left with figuring out where the player is in relation to the transition zone (t).

	(t + y) - (snap + v)

Remember, we're looking for a value of less than t\*2 here because we want to end up with a percent when we divide by t\*2. This one's kinda weird, but we're really just taking the player's y position (y) modified by the transition height (t) and subtracting the snap value modified by the view height (v). While y is in the transition zone, the number this spits out is in fact a positive number less than t\*2. I can't remember how I figure out this part of the algorithm, but it works. So there you go, now you know (hopefully I explained it well enough) what this is and does:

	snap = ((y - v/2) div v)*v
    view_yview[0] = snap + (((t + y) - (snap + v)) / (t*2))*v
