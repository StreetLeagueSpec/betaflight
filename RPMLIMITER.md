# RPM Limiter
## Operation
The RPM limiter uses a PID controller to activly limit the RPM to a maximum. The addition of the rpm linearization feature will allow for a smooth throttle feel right off the launch, and a consistent throttle feel throughout the pack. More details about rpm linearization are mentioned below.
## After flashing
IMPORTANT: After flashing, make sure you either paste a dump from before flashing, or copy your custom defaults from here (sometimes betaflight doesn't apply custom defaults when flashing a local hex) https://github.com/betaflight/unified-targets/tree/master/configs/default
## CLI Commands
### rpm_limiter
Turns rpm limiter on and off 
### rpm_limiter_rpm_limit
Set to desired RPM limit / 100. For example, a 12000 RPM limit would be `set rpm_limiter_rpm_limit = 120`
### rpm_limiter_full_linearization
Turning this feature will attempt to actively control the rpm based off the throttle position. For example, linearization will attempt to maintain 50% of the RPM limit at 50% throttle, 25% of RPM limit at 25% throttle etc. This is essentially vbat sag compensation on crack. Keep in mind, default PIDs are designed for this feature to be turned on, you may have to increase them if linearization is turned off, see the tuning section.
### rpm_limiter_idle_rpm
Set to the expected idle rpm / 100. For street league a good starting place is 1400 RPM, which is `set rpm_limiter_idle_rpm = 14`. This feature is only used with rpm linearization in order to give correct throttle feel. 

WARNING: THE QUAD WILL SPIN UP TO THIS RPM WHEN ARMED, SO DO NOT SET TO ANY VALUE ABOVE 50

# Tuning
Tuning should not be nessesary as the default values will work well for linearization.
## Street League Setup w/ Linearization
```
set rpm_limiter = ON
set rpm_limiter_p = 100
set rpm_limiter_i = 50
set rpm_limiter_d = 25
set rpm_limiter_rpm_limit = 130
set rpm_limiter_idle_rpm = 14
set rpm_limiter_full_linearization = ON
```
## Street League Setup w/o Linearization
```
set rpm_limiter = ON
set rpm_limiter_p = 300
set rpm_limiter_i = 50
set rpm_limiter_d = 25
set rpm_limiter_rpm_limit = 130
set rpm_limiter_idle_rpm = 14
set rpm_limiter_full_linearization = OFF
```
Note: RPM error can be viewed by enabling debug as an OSD element. The first value on the osd element is RPM error which is negative for underspeed and positive for overspeed. Must do `set debug_mode = RPM_LIMITER`

### Simple tuning guide:
1. P should be raised if the limiter feels sloppy, and lowered if the rpm limiter causes vibrations
2. D should be raised until there the P is dampened and there is little overshoot
3. I should be raised if there is a consistent error when full throttle

# Blackbox Setup
You can view the RPM error, P term, I term and D term in the blackbox. Simply `set debug_mode = RPM_LIMITER` in the cli. Values are as follows in this order
1. Average RPM
2. RPM error: Difference between desired RPM limit and smoothed average rpm. RPM limit is rpm_limiter_rpm_limit for linearization off and rpm_limiter_rpm_limit*throttle for linearization on. Positive means overspeed, negative means underspeed
3. I term (positive means term is pulling the throttle down)
4. D term (positive means term is pulling the throttle down)
