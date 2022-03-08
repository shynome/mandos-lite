# Introduction

let mandos server running on termux

## how

1. install proot  
   `apt install proot proot-distro`

1. install debian through proot  
   `proot-distro install debian`

1. login proot debian  
   `proot login debian`

1. install mandos in proot debian  
   `apt install mandos`

1. change `/usr/sbin/mandos` follow "change mandos python file" section

1. add clients to `/etc/mandos/clients.conf`  
   remember change checker to `checker = echo 1`

1. run mandos  
   `mandos --no-dbus --no-zeroconf --no-restore --port 9601 --debug`  
   `mandos --no-dbus --no-zeroconf --no-restore --port 9601 --foreground`

# change mandos python file

run command: `mandos --no-dbus --no-zeroconf --no-restore --port 9601 --debug`

code change
- remove syslogger
- fix: call dbus when don't use dbus

disabled option
- disable dbus. the dbus can't run in termux proot debian 
- disable zeroconf
- disable restore. we can't op the clients status

```python
@@ -184,14 +184,14 @@ def copy_function(func):
 def initlogger(debug, level=logging.WARNING):
     """init logger and add loglevel"""

-    global syslogger
-    syslogger = (logging.handlers.SysLogHandler(
-        facility=logging.handlers.SysLogHandler.LOG_DAEMON,
-        address="/dev/log"))
-    syslogger.setFormatter(logging.Formatter
-                           ('Mandos [%(process)d]: %(levelname)s:'
-                            ' %(message)s'))
-    logger.addHandler(syslogger)
+    # global syslogger
+    # syslogger = (logging.handlers.SysLogHandler(
+    #     facility=logging.handlers.SysLogHandler.LOG_DAEMON,
+    #     address="/dev/log"))
+    # syslogger.setFormatter(logging.Formatter
+    #                        ('Mandos [%(process)d]: %(levelname)s:'
+    #                         ' %(message)s'))
+    # logger.addHandler(syslogger)

     if debug:
         console = logging.StreamHandler()
@@ -3227,7 +3227,9 @@ def main():
     # From the Avahi example code
     DBusGMainLoop(set_as_default=True)
     main_loop = GLib.MainLoop()
-    bus = dbus.SystemBus()
+    global bus
+    if use_dbus:
+        bus = dbus.SystemBus()
     # End of Avahi example code
     if use_dbus:
         try:
```
