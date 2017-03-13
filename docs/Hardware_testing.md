# Testing the hardware
test the components in the following order, installing each component
and required parts in this order.

## Battery + charger
- using lab PSU simulate 5v USB input on charger, check output of ~3.7V
- using battery, check if lab psu is utilized for charging: ok
- check using battery that nothing flows back to usb: ok

## PSU
- check output of 3.3V with scope

## CPU
- test using jtag, quote from datasheet:
> During and just after reset, the alternate functions are not active and the I/O ports are configured in input floating mode.
> The debug pins are in AF pull-up/pull-down after reset:
> - PA15: JTDI in pull-up
> - PA14: JTCK/SWCLK in pull-down
> - PA13: JTMS/SWDAT in pull-up
> - PB4: NJTRST in pull-up
> - PB3: JTDO in floating state
- alternatively switch jusing JP1 to dfu mode and test using JTAG

## Crystal
- test using scope output of xtal
##  USB
- use JP1 to switch to dfu mode, power on, check over usb if dfu device is seen flash pitchfork firmware
##  Display
- test using `tests/oled`
## Buttons
- test using `tests/keys`
## SDCard
- test using pitchfork firmware in MSC mode
## Radio
- test using `tests/nrf`
