# XWorm Lab
Analyze malware behavior to identify persistence methods, evasion techniques, and C2 infrastructure by extracting artifacts and configuration data from static and dynamic analysis.

## Scenario
An employee accidentally downloaded a suspicious file from a phishing email. The file executed silently, triggering unusual system behavior. As a malware analyst, your task is to analyze the sample to uncover its behavior, persistence mechanisms, communication with Command and Control (C2) servers, and potential data exfiltration or system compromise.

## Tools
Opis z labu sygeruje użyć:
Detect It Easy
CFF Explorer
PEStudio
dnSpy
ProcMon
RegShot
Python3

## Q&A

#### Q1
What is the compile timestamp (UTC) of the sample?
<details>
  <summary>Answer: Click me</summary>
  2024-02-25 22:53
</details>

```
Data kompilacji czyli innymi słowy creation time, pliku. Itotne że pytanie jest o czas UTC, soft (np. DiE) pokazuje czas lokalny (na czas kompilacji) natomiast szukajac po hash np. w VT dostaniemy prawidłowy czas UTC.
```

#### Q2
Which legitimate company does the malware impersonate in an attempt to appear trustworthy?
<details>
  <summary>Answer: Click me</summary>
  Adobe
</details>

```
Na podstawie informacji z DiE i VT wiemy że mamy do czynienia z *.NET executable* Przydatnym narzędziem będzie teraz dnSpy. Analizując ten .exe w narzędziu widzimy metadane assembly. Jest tam wskzówka do odpowiedzi.
```

#### Q3
How many anti-analysis checks does the malware perform to detect/evade sandboxes and debugging environments?
<details>
  <summary>Answer: Click me</summary>
  5
</details>

```
Przechodząc pod adresy entrypoint trafiamy do klasy *oQm0xzosrWM7CGTCsMZCODumwvt5ODG1drdBoIeM03A6xt9SK5NFYiMYXb1U* w której dokonuje się kilka ważnych rzeczy. Jest sleep, odszyfrowanie configu, anti-debug, drop pliku na dysk, perstystencja, wyłaczenie Defendera, no i start głównej logiki (wiele wątków). W tym pytaniu interesuje nas część anti-debug. W kodzie widzimy artefkaty sprawdzające: VM-detection, Debuger detection, Sandbox detection, OS check(Win XP), hosting/VPS detection
```

#### Qx
pytanie
<details>
  <summary>Answer: Click me</summary>
  odpowiedz
</details>

```
komentarz, wyjasnienie etc
```

#### Qx
pytanie
<details>
  <summary>Answer: Click me</summary>
  odpowiedz
</details>

```
komentarz, wyjasnienie etc
```

#### Qx
pytanie
<details>
  <summary>Answer: Click me</summary>
  odpowiedz
</details>

```
komentarz, wyjasnienie etc
```

#### Qx
pytanie
<details>
  <summary>Answer: Click me</summary>
  odpowiedz
</details>

```
komentarz, wyjasnienie etc
```

#### Qx
pytanie
<details>
  <summary>Answer: Click me</summary>
  odpowiedz
</details>

```
komentarz, wyjasnienie etc
```

## tl;dr czyli Kill Chain
1. Initial Access & Reconnaissance
  - asdf

2. Execution
  - asdf

3. Discovery/Enumeration
  - asdf

4. Credential Access 
  - asdf

5. Lateral Movement
  - asdf

6. Privilege Escalation
  - asdf

7. Container Escape
  - asdf

8. Command & Control (C2)
  - asdf

9. Persistence & Impact
  - asdf

10. Defense Evasion
  - asdf

# analiza malware

1. plik .exe, podszywanie sie pod adobe

|entrypoint
v

2. główna funkcja:
  - Sleep (opóźnienie)
  - Odszyfrowanie configu
  - Anti-VM / anti-debug
  - Drop pliku na dysk
  - Persistence (3 metody!)
  - Wyłączenie Windows Defendera
  - Start wielu wątków (główna logika malware)


***
# lessons&learned
- DiE do szybkiego zorientowania się z jakiego rodzaju plikiem mamy do czynienia
- dnSpy do do pracy z plikami .NET
- nagłowek pliku dostarcza metadanych, między innymi entrypoint
- analiza startowej funkcji (main lub podobne)
- przykłądy sprawdzenia anty debug:
  - Wykrywanie maszyny wirtualnej (VM detection)
```
new ManagementObjectSearcher("Select * from Win32_ComputerSystem")

string text = ...["Manufacturer"].ToString().ToLower();

if (text.Contains("vmware") ||
    model.Contains("VIRTUAL") ||
    model == "VirtualBox")
{
    return true;
} 
```
  - Wykrywanie debuggera
```
[DllImport("kernel32.dll", EntryPoint = "CheckRemoteDebuggerPresent")]

CheckRemoteDebuggerPresent(Process.GetCurrentProcess().Handle, ref flag);
```
  - Wykrywanie Sandboxa
```
GetModuleHandle("SbieDll.dll")
```
  - Wykrywanie starego systemu (Windows XP)
```
new ComputerInfo().OSFullName.ToLower().Contains("xp")
```
  - Wykrywanie środowiska hostingowego (VPS / cloud)
```
new WebClient().DownloadString("http://ip-api.com/line/?fields=hosting")
```

***
# TEST AREA

graph TD
    %% Definicja węzłów
    A[Domain: lumafrost.com] -- DNS --> B(IP: 194.26.x.x)
    B -- Scan --> C{Port 443}
    
    %% Logika decyzji
    C -- "JARM Hash" --> D[Pivot: star-service.website]
    C -- "HTML Body" --> E[Network Device Login]
    
    %% Stylizacja (opcjonalna)
    style E fill:#f96,stroke:#333,stroke-width:2px
    style A fill:#bbf,stroke:#333