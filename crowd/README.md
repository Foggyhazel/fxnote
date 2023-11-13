# General Workflow (In Place Animation)

- import character animation: character rig, fbx, usd, lop, motion clip
- create `Agent` with the shelf tool then pack all animation clips into it with `Add Clip` shelf tool or use `Agent Clip`
- populate crowd with `Crowd Source` or `Populate` shelf tool
  - `crowd source` has an option to set initial velocity
  - if you feed only points into the second input, it will use those points to copy agent instead
- use `Simulate` shelf tool to setup DOP network which includes imported agent state, rbd solver and constraint setup for rag doll 

---

# Basic Simulation

## State Transition

- use `crowd trigger` and `crowd transition` to make state transition.
- during the transition *blending* happens. You can check which states are currently participated in blending by looking at `clipnames` attribute
- when an agent enters a new state, its speed range is changed and actual speed is ___clamped___ to that range. So if you want to randomly set the speed, use vex code. For example you can connect `POP wrangle` under the `run` state and randomly choose goal speed for each agent then accelerate it to that speed manually

```c
float goal_speed = fit01(rand(i@id), @speedmin, @speedmax));
vector dir = normalize(v@v);
float speed = length(v@v);

if(speed < goal_speed){
  float accel = 0.1;
  v@v += dir * accel;
}
```

## Movement Speed

- the solver use `speedmin`, `speedmax` to clamp particle speed
- this speed range is what make agent movement looks reasonable to its current state. For example, when an agent is in `run` state, the speed should be higher than when it is in `walk` state
- You can adjust the speed range for each state by changing `gait speed` on `Crowd State` dop

## Note

- `Crowd State` and `Crowd Trigger` likely create something like pop stream so any POP nodes down below only act on that stream
- Crowd setup works similar to state machine
- if you use locomotion instead of in place animation, you cannot directly adjust velocity like this

---

# Locomotion

## Agent Setup

- import character animation, disable locomotion so that it actually moves
- create agent, add clip
- on the `Agent Clip` SOP, set `Locomotion Node` and `Locomotion Orient` which represent the translation and overall rotation of the clip's locomotion
- `Agent Prep` 
  - define mapping between character's joints and joints that are understood by crowd tool
  - setup foot plant CHOP
    > From documentation: This is useful for creating channels that describe when a character’s feet are planted, which can be used for foot locking in a crowd simulation.
- `Collision Layer` and `Configure Joints` shelf tool: setup collision shape and joint rotation limit for later use in rag doll simulation