# POP simulation

## Settings if plan to retime to slow down later

- negative jitter birth time
- disable reap at frame end

This way you have extra data just before particle birth and death so retiming does not flicker too much at birth and have no frozen points before death. 
Have to delete `age<0` and `dead==1` manually after retime
