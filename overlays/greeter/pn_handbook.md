# Welcome

## Introduction

Lorem Impsum

If you want to improve this text, merge requests are very very much appreciated!
(https://github.com/m-weigand/pinenote-debian-recipes/blob/mw_image/overlays/greeter/pn_handbook.md")[Improve here]

## Getting started

* User/Password: You are logged in as user "user" with password "1234". sudo is
  activated. We suggest to set a root password:

	sudo su - root
  	passwd

* The **Documents/** directory contains one sample .pdf and one .epub file. Try
  opening them and start reading!

## Xournalpp/Writing

* At this point, despite disabling anomations, GNOME still shows the spinning
  animation in the panel when xournalpp is started. This prevents proper and
  fast drawing of screen regions. For best experience, wait until the loading
  animation stops before you start drawing/writing.
* Switch to "BW+Dither" mode when working in Xpp
* The default configuration uses evsieve to merge events from the pen (for
  drawing) and the pen buttons. This is a solution to the problem that the pen
  input and the pen buttons are completely independent systems and therefore
  register as different inputs in the system.

  The evsieve solution add something like 15 ms lag to the input (according to
  the evsieve readme). You can disable this approach by running (in a root
  shell):

	systemctl stop evsieve.service
	systemctl disable evsieve.service

  Make sure to restart Xpp and to reconfigure the input sources in the
  settings.
* By default the touch screen is disabled as an input for Xpp. You need to
  activate it in the settings in order to scroll using touch gestures.
* If the pen buttons are configured, holding down the button nearest to the tip
  should allow you to scroll using the pen.
* After both pen and touch scrolling a global refresh is triggered

## How do I

* **Read a book (epub)/pdf**: Koreader is already installed and should be registered for corresponding file types
* **Take notes**: Xournalpp was slightly modified to provide an improved experience on the Pinenote.
* **Use the Pinenote as an external screen?**: TODO, link to weylus project
* **Use an external monitor with the Pinenote?**: TODO (won't directly work, need something like vnc and virtual monitor)


## What is not working?

* Open Issues

	* Gnome extension: There are issues when suspend/screen blanking (i.e., unloading of the extension is broken)
	* EBC artifacting
	* Cover detection ?
	* Resume/suspend
	* BT Issues (link to probably-working firmware?)


## EBC Kernel Driver
	* Introduction
	* boot-time module parameters
	* runtime-controlled module parameters
	* ioctls

		* trigger global refresh
		* set offline-screen
		* [to implement] set standby screen mask & behavior
	* misc:

		* discuss the waveform-switching issues in a general DE environment:
		  recommend to always do a global refresh after switching waveforms,
		  unless you know that your buffer is compatible

### Waveforms

* the **default_waveform** parameter controls which waveform is used. Based on
  information from include/drm/drm_epd_helper.h, the integer values are
  associated with the following waveforms:

		0: @DRM_EPD_WF_RESET: Used to initialize the panel, ends with white
		1: @DRM_EPD_WF_A2: Fast transitions between black and white only
		2: @DRM_EPD_WF_DU: Transitions 16-level grayscale to monochrome
		3: @DRM_EPD_WF_DU4: Transitions 16-level grayscale to 4-level grayscale
		4: @DRM_EPD_WF_GC16: High-quality but flashy 16-level grayscale
		5: @DRM_EPD_WF_GCC16: Less flashy 16-level grayscale
		6: @DRM_EPD_WF_GL16: Less flashy 16-level grayscale
		7: @DRM_EPD_WF_GLR16: Less flashy 16-level grayscale, plus anti-ghosting
		8: @DRM_EPD_WF_GLD16: Less flashy 16-level grayscale, plus anti-ghosting

* (side note): Based on information from drivers/gpu/drm/drm_epd_helper.c, the
  Pinenote uses eps lut form 0x19, which associated waveform types with the
  luts stored in the file as:

		.versions = {
		    0x19,
		    0x43,
		},
		.format = DRM_EPD_LUT_5BIT_PACKED,
		.modes = {
		    [DRM_EPD_WF_RESET]  = 0,
		    [DRM_EPD_WF_DU]     = 1,
		    [DRM_EPD_WF_DU4]    = 7,
		    [DRM_EPD_WF_GC16]   = 2,
		    [DRM_EPD_WF_GL16]   = 3,
		    [DRM_EPD_WF_GLR16]  = 4,
		    [DRM_EPD_WF_GLD16]  = 5,
		    [DRM_EPD_WF_A2]     = 6,
		    [DRM_EPD_WF_GCC16]  = 4,
		},

  For example, if you want to inspect/modify the A2 waveform, this corresponds
  to the 7th waveform in the lut file (index 6), but is activated via
  **default_waveoform** by writing value 1.

# Trimming the A2 waveform

You can trim the A2 waveform for improved writing performance, with the
downside that black sometimes is displayed in gray tones.

The supplied script is very slow and unoptimized and therefore not run
automatically (run time on pinenote ca. 20 minutes).

Call these command in a root shell to trim the A2 waveform (note: this will
reboot the pinenote once):

	cd /root
	# this command should take ca. 20 minutes !!!
    time python3 parse_waveforms_and_modify.py
    rm /lib/firmware/rockchip/ebc.wbf
   	ln -s /lib/firmware/rockchip/ebc_modified.wbf /lib/firmware/rockchip/ebc.wbf
   	cd /extlinux
    ./gen_uboot_image.sh
	reboot


# Topics

* EBC Driver and control

* DBUS service

	* contrl ebc driver
	* [to implement] handle pen pairing/unpairing

* The GNOME extension
* Pen
* Blocked packages & updates
* Resources
