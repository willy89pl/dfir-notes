# ContainerBreak - Rootkit Trail Lab
## Scenario
Following the network intrusion, the attacker successfully escaped from the container to the underlying Linux host system. After killing the packet capture process, the attacker uploaded malicious files, installed a kernel level rootkit, and carefully removed all installation artifacts before disconnecting.
Days later, the compromised server exhibited suspicious behavior - hidden processes, unexplained network connections, and system instability. A complete forensic collection was performed on the live system to investigate the extent of the compromise and identify the attacker's persistence mechanisms.

## Tools
Pliki wejściowe do analizy zabezpieczono narzedziem Unix-like Artifacts Collector. Ciekawe narzędzie które wykonuje szereg skyrptów i pluginów zabezpieczając najważniejsze informacje i pliki z sytemu Linuxo podobnego.

