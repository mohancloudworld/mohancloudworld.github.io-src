Date: 2015-11-18
Title: Audio 

Note: Some of the following commands work when logged-in locally or via VNC-Viewer but does not work when logged-in remotly (SSH).
In that case set the shell environment parameter DISPLAY to ":0.0" (type 'DISPLAY=:0.0' in logged-in shell).

Play ogg/oga files:
-------------------
(1) (a) echo ogg123 <file> |at now
	 (b) ogg123 <file>
			Note: 'DISPLAY=:0.0' is needed

Play mp3 files:
---------------
(1) mpg123 <file>

Play wav files:
---------------
(1) On Default Sound Card (hw:0)
	aplay <file>
	Note: 'DISPLAY=:0.0' is needed
	(or)
	aplay -D hw:0 <file>

Change Audio Mixer Setting:
---------------------------
(1) In CLI, using interactive menu
	alsamixer
	Note: 'DISPLAY=:0.0' is needed
(2) In CLI, non-interactive 
	(a) set playback volume to X% of maximum [Range 0-100%]
		amixer set Master playback <percent>%
		e.g.: amixer set Master playback 90%
	(b) increment playback volume by X% of maximum [add '+' sign]
		amixer set Master playback <percent>%+
		e.g.: amixer set Master playback 5%+
	(c) decrement playback volume by X% of maximum [add '-' sign]
		amixer set Master playback <percent>%-
		e.g.: amixer set Master playback 5%-
	(d) Without mentioning the soundcard (automatically 'Default' is used) 
		amixer set Master playback <percent>%
		Note: 'DISPLAY=:0.0' is needed

Pulseaudio for multiple users:
------------------------------
See: http://billauer.co.il/blog/2014/01/pa-multiple-users/
When we login into a desktop user, the audio is only accesseble only to that user. This procedure will enable access to other users while loging-in to that user through terminal, while logged into the default desktop user via GUI.
(1) As default desktop user
    (a) If not done already, creating a custom configuration for the default desktop user
        mkdir ~/.pulse/
        cp /etc/pulse/default.pa ~/.pulse/
    (b) Enable pulse audio to accept connections through loop-back TCP port
        load-module module-native-protocol-tcp auth-ip-acl=127.0.0.1
    (c) Restart pulse audio
        pulseaudio -k # kill
        pulseaudio -D # start daemon
(2) As another user (for which you want to enable pulse audio access concurrently with default desktop user)
    (a) Specify the access method
        mkdir ~/.pulse
        echo "default-server = 127.0.0.1" > ~/.pulse/client.conf
        Note: If using Tor browser-bundle, firefox runs in "<TOR_ROOT_DIR>/Browser/" directory and is its home directory. So create the ".pulse/client.conf" in this directory.
    (b) Now application running as this user (changed via terminal {using 'su' or 'ssh'} or switch user via GUI) can access audio. 
        e.g.,start browser via terminal through following command
        firefox --display=:0
        (or)
        google-chrome --display=:0

Pulseaudio Dynamic Range Compression
------------------------------------
See: https://github.com/gotbletu/shownotes/blob/master/pulseaudio-dynamic-range-compression.md
    http://plugin.org.uk/ladspa-swh/docs/ladspa-swh.html#tth_sEc2.92
    https://www.youtube.com/watch?v=91qs3fux5HY
    https://askubuntu.com/questions/31580/is-there-a-way-of-leveling-compressing-the-sound-system-wide
(1) Install dynamic-range-compression plugin (ladspa-swh-plugins) and its dependencies 
    yum install pulseaudio pavucontrol ladspa-swh-plugins
(2) If not done already, creating a custom configuration for the default desktop user
        mkdir ~/.pulse/
        cp /etc/pulse/default.pa ~/.pulse/
(3) Append following lines to ~/.pulse/default.pa
    .ifexists module-ladspa-sink.so
    .nofail
    load-module module-ladspa-sink sink_name=dynamic-range-compression plugin=sc4m_1916 label=sc4m control=1,1.5,401,-30,20,5,12
    .fail
    .endif
(4) Reboot Pulse-Audio 
    pulseaudio --kill
    pulseaudio --start
(5) Now play some audio, then open pavucontrol and change the stream to the LADSPA compressor. Or set it as default output option
