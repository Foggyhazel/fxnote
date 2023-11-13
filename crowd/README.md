# General Workflow (In Place Animation)

- import character animation: character rig, fbx, usd, lop
- create `Agent` with the shelf tool then packs all animation clips into it with `Add Clip` shelf tool or use `Agent Clip`
- populate crowd with `Crowd Source` or `Populate` shelf tool
  - `crowd source` has an option to set initial velocity
- use `Simulate` shelf tool to setup dop network
- now you get a dop network with crowd sim setup. It also has rbd for rag doll and agent states imported

---

# Basic Simulation

## State Transition

- use `crowd trigger` and `crowd transition` to make state transition.
- during the transition *blending* happens. You can check which states are currently participate in this intermediate state by looking at `clipnames` attribute
- when an agent enter a new state, its speed range is changed and its speed is ___clamp___ to that range. So if you want to randomly set the speed, use vex code. For example you can connect `POP wrangle` under the `run` state and randomly choose goal speed for each agent then accelerate it to that speed manually

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

- the solver use `speedmin`, `speedmax` to clamp particle velocity
- this speed range is what make particle movement looks reasonable to the current state of agent. For example when an agent is in `run` state, the velocity should be higher than when it is in `walk` state
- You can adjust the speed range for each state by changing `gait speed` on `Crowd State` dop

## Note

- `Crowd State` and `Crowd Trigger` likely create something like pop stream so any POP nodes down below only act on that stream
- Crowd setup works similar to state machine
- if you use locomotion instead of in place animation, you cannot directly adjust velocity like this

---

# Locomotion
