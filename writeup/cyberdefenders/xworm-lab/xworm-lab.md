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
Przechodząc pod adresy entrypoint trafiamy do klasy *oQm0xzosrWM7CGTCsMZCODumwvt5ODG1drdBoIeM03A6xt9SK5NFYiMYXb1U* w której dokonuje się kilka ważnych rzeczy. Jest sleep, odszyfrowanie configu, anti-debug, drop pliku na dysk, perstystencja, wyłaczenie Defendera, no i start głównej logiki (wiele wątków). W tym pytaniu interesuje nas część anti-debug. W kodzie widzimy artefkaty sprawdzające: VM-detection, Debuger detection, Sandbox detection, OS check(Win XP), hosting/VPS detection. Szczegóły i przylady takich technik (i odnalezionych artefaktów) w sekcji LessonsLearned
```

#### Q4
What is the name of the scheduled task created by the malware to achieve execution with elevated privileges?
<details>
  <summary>Answer: Click me</summary>
  WmiPrvSE
</details>

```
No tutaj żeby zacząc odpowiadać zaczyna sie juz poważna praca do wykonania... Klasa którą analizowaliśmy w Q3 pełna jest zaobfuskowanych metod, funkcji etc. I tak żeby poznać nazwę pliku:
    {
					processStartInfo.Arguments = string.Concat(new string[]
					{
						"/create /f /RL HIGHEST /sc minute /mo 1 /tn \"",
						Path.GetFileNameWithoutExtension(NB2mi1VBTSN5U40DfEsDcrzgxWCrxt7i1yCoMW0Zb5dK9QwIjZ6W6wYeHriq.EB5J4sIzfH74BwfgRjacCtnEuNWFxu93z57nr4HrttTW5asXOhadv7pC7YFu),
						"\" /tr \"",
						text,
						"\""
					});
				}

yEA8oSg5e02FNWc6DpGE
```

#### Q5
pytanie
<details>
  <summary>Answer: Click me</summary>
  odpowiedz
</details>

```
komentarz, wyjasnienie etc
```

#### Q6
Which cryptographic algorithm does the malware use to encrypt or obfuscate its configuration data?
<details>
  <summary>Answer: Click me</summary>
  AES
</details>

```
komentarz, wyjasnienie etc
```

#### Q7
pytanie
<details>
  <summary>Answer: Click me</summary>
  odpowiedz
</details>

```
komentarz, wyjasnienie etc
```

#### Q8
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


┌─────────────┐                             
│             ┼───────────────────────┐     
│             ┼────────────────────┐  │     
│             │                    │  │     
└─────────────┘                    │  │     
                            ┌─────────▼────┐
                            │              │
                            │              │
                            │              │
                            │              │
                            │              │
                            └──────────────┘