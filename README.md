# nRF51-ble-peripheral-bond-handling

## Known issue
 - **The device will become stuck in an endless reset loop if the DM_NO_APP_CONTEXT assert is thrown in app_bond_init(). This can be**    **triggered if an unexepcet reset occurs**
   **while the application context is being written to. I will try to make a patch for this in the course of this or next week** 

The device manager accepts new bonding procedures even when the number of devices has reached the device manager's set limit(DEVICE_MANAGER_MAX_BONDS),
but the bonding information will not be stored persistently. In such cases, application will be notified with DM_DEVICE_CONTEXT_FULL 
as event result at the completion of the security procedure. It is in other words up to the application to handle this event. 

One way of handling this event is described in the SDK documentation: 

>Behaviour when maximum number of bonds is reached: All applications that support bonding use the bond manager module. 
>When the bond manager notifies the application that it cannot store a newly bonded master into the flash, the application will assert. 
>When this happens, allow the device to go into System Off mode and then press Button 1. This will wake up the application and clear all existing bonds. 
>The bonds can also be cleared by power-cycling the board by keeping the Button 1 pressed. ([SDK documentation])

Another alternative is to make use of the DM API to clear devices that are no longer needed rather than clearing all of them. However, the 
challenge for the application is to know which bond to delete. 

In this example I have chosen to implement automatic deletion of the bonded device that was least recently stored when the limit is reached, hence stopping the 
DM_DEVICE_CONTEXT_FULL event from occurring. This means that there will always be at least one row available for a new bond.

##Notes

 - The example always keeps one bond free, so make sure to adjust DEVICE_MANAGER_MAX_BONDS to desired number of bonds + 1.
 - The least recently bond stored may also be the one that is the most frequently used. In this case the device needs to establish a new bond with the peer.
   Thus, making it the most recently stored bond. 

##Requirements

 - nRF51 SDK version 9.0.0
 - nRF51 DK

The project may need modifications to work with other versions or boards.

To compile it, clone the repository in the /examples/peripheral/ folder in the nRF51 SDK v9.0.0 or later (or any other folder under /examples/)

## About this project

This application is one of several applications that has been built by the support team at Nordic Semiconductor, as a demo of some particular feature or use case. 
It has not necessarily been thoroughly tested, so there might be unknown issues. It is hence provided as-is, without any warranty.

However, in the hope that it still may be useful also for others than the ones we initially wrote it for, we've chosen to distribute it here on GitHub.

The application is built to be used with the official nRF51 SDK, that can be downloaded from http://developer.nordicsemi.com/.

Please post any questions about this project on https://devzone.nordicsemi.com.


[SDK documentation]:http://developer.nordicsemi.com/nRF51_SDK/nRF51_SDK_v8.x.x/doc/8.1.0/s110/html/a00064.html
