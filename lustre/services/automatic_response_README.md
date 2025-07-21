# Model #

This feature branch contains the Kind2 model of an OpenUxAS service that responds to detections by DAIDALUS.  DAIDALUS is the reference implementation of the Minimum Operational Performance Standard for Detect and Avoid established by the RTCA Special Committee SC-228.  DAIDALUS was formally specified in PVS by NASA with implmentations in JAVA and C++ that has simulations that conform to the formal specification with a deviation of less than 0.1% for the test cases used.  

In OpenUxAS, every time state updates occur, that information is received by a service that packages the ownship and intruder(other cooperative vehicles that are not the ownship when OpenUxAS is running on each vehicle) information and invokes DAIDALUS to produce alerting information with respect to altitude, heading, and groundspeed.  This banding information is then used to adhere to a protocol designed to produce a response to indicated imminent loss of well clear for the ownship. After receiving the banding information, the response protocol determines whether action is necessary by comparing the projected time to violation of the well clear volume to a threshold.  If action is needed to prevent/ameliorate a loss of well clear, the protocol will determine a right-of-way vehicle among the vehicles projected to cause the conflict.  The right-of-way vehicle maintains its course.  Other vehicles transition to a divert state during which the DAIDALUS banding information is used to determine either an acceptable action or a prescribed fallback action to set a divert heading/speed/altitude command.  The divert state can also result in no-recourse under certain circumstances.  After picking a divert state, the protocol automatically transitions to a OnHold state where either the divert command or the right-of-way vehicle command is held until the next state information update triggers a re-assessment of the situation.

# Running the Model #
The kind2 model of the automatic response to DAIDALUS, located in `\services ` can be run using `kind2 automatic_response.lus --compositional true --modular true`

This model invokes the functions:
1. `SetDivertState.lus` - selects the divert state
2. `SafeToReturn.lus` - evaluates next mission waypoint with respect to violation of well clear volume

The `SetDiverState` function then invokes:
1. `altitude_resolution.lus` - checks for altitude resolution using DAIDALUS band information.
2. `heading_resolution.lus` - checks for heading resolution using DAIDALUS band information.
3. `speed_resolution.lus` - checks for groundspeed resolution using DAIDALUS band information.

