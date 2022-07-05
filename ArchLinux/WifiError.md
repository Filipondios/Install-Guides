# Solution to: Error when typing the command "ip link set interface up"

If you typed that command, and i response you recieved "ioctl[SIOCSIFFLAGS]: Operation not possible due to RF-kill",
the solution is the following:
```Bash
rfkill unblock all
ip link set Yourinterface up
```
