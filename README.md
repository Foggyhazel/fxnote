# POP simulation

## Settings if plan to retime to slow down later

- negative jitter birth time
- disable reap at frame end

This way you have extra data just before particle birth and dead so retiming does not flicker too much and has no frozen points. 
Have to delete age<0 and dead=1 manually after retime
