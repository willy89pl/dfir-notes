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
No tutaj żeby zacząc odpowiadać zaczyna sie juz poważna praca do wykonania... Klasa którą analizowaliśmy w Q3 pełna jest zaobfuskowanych metod, funkcji etc. I tak żeby poznać nazwę pliku która jest zahardkodowanym stringiem który trzeba odszyfrować:

public static string EB5J4sIzfH74BwfgRjacCtnEuNWFxu93z57nr4HrttTW5asXOhadv7pC7YFu = "sJHKF5x7kjxy85oLMym05A==";
który znajduje się w klasie poniżej
[...]    
ProcessStartInfo processStartInfo = new ProcessStartInfo("schtasks.exe");
				processStartInfo.WindowStyle = ProcessWindowStyle.Hidden;
				if (Conversions.ToBoolean(MUaDlUN9X5rN98KUAn5WbH3KOZ85RyCCg3qIDoLO8mHqWoqZYUPKBUWIW2vuwan1zJDsD93oLEVavFhmWRM9urmZakxV.9wp6DW38pEGrxGQEOQkzV4F6DVSJViAZDNdsO9gtbzZBQrydyvd059AgNPuLYcnNJjNwBFhzo8yNTC1aOPH4fLXTZlHK()))
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
[...]
odszyforwanie naszego string wykouje się tu:
NB2mi1VBTSN5U40DfEsDcrzgxWCrxt7i1yCoMW0Zb5dK9QwIjZ6W6wYeHriq.EB5J4sIzfH74BwfgRjacCtnEuNWFxu93z57nr4HrttTW5asXOhadv7pC7YFu = Conversions.ToString(yEA8oSg5e02FNWc6DpGE.f5Mo9y1FK1yJy4poW9CE(NB2mi1VBTSN5U40DfEsDcrzgxWCrxt7i1yCoMW0Zb5dK9QwIjZ6W6wYeHriq.EB5J4sIzfH74BwfgRjacCtnEuNWFxu93z57nr4HrttTW5asXOhadv7pC7YFu));

przy użyciu klasy:
yEA8oSg5e02FNWc6DpGE

jej logika:

public class yEA8oSg5e02FNWc6DpGE
	{
		// Token: 0x0600013B RID: 315 RVA: 0x000069B8 File Offset: 0x00004BB8
		public static object f5Mo9y1FK1yJy4poW9CE(string 3pXqYfeWgCBZOAYUjYnh)
		{
			RijndaelManaged rijndaelManaged = new RijndaelManaged();
			MD5CryptoServiceProvider md5CryptoServiceProvider = new MD5CryptoServiceProvider();
			byte[] array = new byte[32];
			byte[] sourceArray = md5CryptoServiceProvider.ComputeHash(ACX0qTJzEzq40qP5qFxb.wVkaAAeCf6BeWi8Flwtq(NB2mi1VBTSN5U40DfEsDcrzgxWCrxt7i1yCoMW0Zb5dK9QwIjZ6W6wYeHriq.DhMybcleyUJ8bZbaqtAkL3FTz6SQ840xELBsFWt9yekNCVYQ1WgRtjL1bTF3));
			Array.Copy(sourceArray, 0, array, 0, 16);
			Array.Copy(sourceArray, 0, array, 15, 16);
			rijndaelManaged.Key = array;
			rijndaelManaged.Mode = CipherMode.ECB;
			ICryptoTransform cryptoTransform = rijndaelManaged.CreateDecryptor();
			byte[] array2 = Convert.FromBase64String(3pXqYfeWgCBZOAYUjYnh);
			return ACX0qTJzEzq40qP5qFxb.sJljw7gGxcYB8jRe1fPv(cryptoTransform.TransformFinalBlock(array2, 0, array2.Length));
		}

uff, ale to jeszcze nie koniec...
Zauważyć można kilka istotnych informacji:
RijndaelManaged - algorytm AES
CipherMode.ECB - tryb ECB
tworzenie tablicy 32 bajty (dla klucza deszyfrującego, który będzie utworzony w specyficzny sposób)
md5CryptoServiceProvider.ComputeHash - najistotniejszy element gdzie obliczne jest MD5 dla wartości początkowej pobranej z DhMybcleyUJ8bZbaqtAkL3FTz6SQ840xELBsFWt9yekNCVYQ1WgRtjL1bTF3
public static string DhMybcleyUJ8bZbaqtAkL3FTz6SQ840xELBsFWt9yekNCVYQ1WgRtjL1bTF3 = "8xTJ0EKPuiQsJVaT";
istotny jest też fragment tworzenia zawartości tablicy:
      Array.Copy(sourceArray, 0, array, 0, 16);
			Array.Copy(sourceArray, 0, array, 15, 16);

Dziłanie w praktyce:
string(8xTJ0EKPuiQsJVaT) jest obliczny przez MD5
wynik wypełnia pierwszych 16 bajtów tablicy(od 0 do 15) , nastepnie dodawana jest ta sama wartość do tablicy ale począwszy od pozycji 15 (co powoduje nadpisanie jednego bajtu), do reszty dodane sa 0 aby wypełnić tablicę do 32 bajtów. W efekcie otrzymujemy klucz deszyfrujący do elementów config'u malware.
```
Postac klucza:
```
aecd2c6f5b77e319e535564f1fd601aecd2c6f5b77e319e535564f1fd601d300
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
  8xTJ0EKPuiQsJVaT
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