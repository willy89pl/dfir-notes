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
What is the filename of the malware binary that is dropped in the AppData directory?
<details>
  <summary>Answer: Click me</summary>
  WmiPrvSE.exe
</details>

```
Posiadając klucz deszyfrujący mozemy odszyfrować fragemnt:
string text = NB2mi1VBTSN5U40DfEsDcrzgxWCrxt7i1yCoMW0Zb5dK9QwIjZ6W6wYeHriq.pAiK6SOy8HEO6uDLFXMlZSPdAbNKgcHqwR32QBERmGnbcKxg5SelHoKfUgGc + "\\" + NB2mi1VBTSN5U40DfEsDcrzgxWCrxt7i1yCoMW0Zb5dK9QwIjZ6W6wYeHriq.EB5J4sIzfH74BwfgRjacCtnEuNWFxu93z57nr4HrttTW5asXOhadv7pC7YFu;
gdzie jest konkatenacja:
%AppData% + // + *nasa odpowiedź"
```

#### Q6
Which cryptographic algorithm does the malware use to encrypt or obfuscate its configuration data?
<details>
  <summary>Answer: Click me</summary>
  AES
</details>

```
Obszerne wytłumaczenie w Q4 wskazuje na klase która tworzy klucz deszyfrujący, jest tam wskazówka do odpowidzi.
```

#### Q7
To derive the parameters for its encryption algorithm (such as the key and initialization vector), the malware uses a hardcoded string as input. What is the value of this hardcoded string?
<details>
  <summary>Answer: Click me</summary>
  8xTJ0EKPuiQsJVaT
</details>

```
Obszerne wytłumaczenie w Q4 wskazuje na klase która tworzy klucz deszyfrujący, wczytując się w logikę tworzenia klucza natrafiamy na odniesienie do string'u który jest odpowiedzią.
```

#### Q8
What are the Command and Control (C2) IP addresses obtained after the malware decrypts them?
<details>
  <summary>Answer: Click me</summary>
  185.117.250.169,66.175.239.149,185.117.249.43
</details>

```
Posiadając klucz do deszyforwania w klasie NB2mi1VBTSN5U40DfEsDcrzgxWCrxt7i1yCoMW0Zb5dK9QwIjZ6W6wYeHriq odnajdujemy cały config. Deszyfrując wartości z tej klasy wypada między innymi zest adresów IP który wykorzystywany jest w klasie która wygląda na nawiązywanie komunikacji i sterowanie przepływem danych.
```

#### Q9
What port number does the malware use for communication with its Command and Control (C2) server?
<details>
  <summary>Answer: Click me</summary>
  7000
</details>

```
Analogicznie do pytania Q8
```

#### Q10
The malware spreads by copying itself to every connected removable device. What is the name of the new copy created on each infected device?
<details>
  <summary>Answer: Click me</summary>
  USB.exe
</details>

```
W konfigu nattafiamy na ciekawy string który jest wykorzytywany w klasze która wygląda jak mechanizm replkiacji na wszystkich urządzeniach typu pendrive.
```

#### Q11
To ensure its execution, the malware creates specific types of files. What is the file extension of these created files?
<details>
  <summary>Answer: Click me</summary>
  .lnk
</details>

```
Analogicznie do Q10 w kodzie widzmy tworzenie plików o rozszerzeniu bedącym odpowiedzia na to pytanie.
```

#### Q12
What is the name of the DLL the malware uses to detect if it is running in a sandbox environment?
<details>
  <summary>Answer: Click me</summary>
  SbieDll.dll
</details>

```
To pytanie znowu wysyła nas do etapu w którym analizowalismy klasę wskazaną przez entrypoint. Do analizu kodu zabezpieczeń anty-debug. Trochę dziwne miejsce na to pytanie, powinno paść wcześniej. Bardziej by pasowało do rozwijajacej się analizy tego malware.
```

#### Q13
What is the name of the registry key manipulated by the malware to control the visibility of hidden items in Windows Explorer?
<details>
  <summary>Answer: Click me</summary>
  ShowSuperHidden
</details>

```
Przeglądając kolejne klasy natrafiamyna VRti6vhPYugo9GdL3aQYj2eDRdhSfKIazXyfNr18qkYGBHO0iTkiZoRtMwtI7vEZAaW8tY"fk5m7J2 w której znajdziemy rejestr "Software\\Microsoft\\Windows\\CurrentVersion\\Explorer\\Advanced" widzmy manipulajce wartością rejestru "ShowSuperHidden"
```

#### Q14
Which API does the malware use to mark its process as critical in order to prevent termination or interference?
<details>
  <summary>Answer: Click me</summary>
  RtlSetProcessIsCritical
</details>

```
W klasie ke48iewt5U3eoIMbjLCt możemy odnaleść wywołanie biblioteki NTdll.dll która to wywołu API które jest odpowiedzia na to pytanie. Funkcja ta słuzy do oznaczania procesu jako Critical Process
```

#### Q15
Which API does the malware use to insert keyboard hooks into running processes in order to monitor or capture user input?
<details>
  <summary>Answer: Click me</summary>
  SetWindowsHookEx
</details>

```
Wykorzystując wiedzę że mamy doczynienia z .NET ,powinniśmy szukać wokoło DLL które mają funkcje związane z przechwytywaniem znaków z klawiatury. W jednej z klas odnajdziemy fragment kodu który wywołuje jedną z takich funkcji z bilbioteki user32.dll
```

#### Q16
Given the malware’s ability to insert keyboard hooks into running processes, what is its primary functionality or objective?
<details>
  <summary>Answer: Click me</summary>
  Keylogger
</details>

```
Sumując wszystkie informacje które odkryliśmy w trakcie kodu źródłowego i dokonując OSINT o malware oraz uwzględniając sens pytania możemy postawić odpowiedź.
```


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

  |zdolności malware
  v
    - wykradanie portfeli kryptowalut
    - keylogging
    - rozpstrzenianie się na urządenia przenośne



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

Wykorzystanie Funkcji *RtlSetProcessIsCritical* z bibltioteki NTdll.dll do oznaczania procecu jako krytyczny. Terminowanie takiego procesu spowoduje zawieszenie systemu co będzie bardzo mocnym zabezpieczeniem przed ubiciem/amaliza/forensic

Biblioteki które powinny zwrócić uwagę i funkcje które byly zauważone w trakcie analizy:

| DLL | Element w kodzie | Opis | Technika |
|-----|------------------|------|----------|
| user32.dll | GetLastInputInfo | Sprawdza czas od ostatniej aktywności użytkownika (wykrywanie bezczynności sandboxa) | Anti-idle (sandbox evasion) |
| user32.dll | GetForegroundWindow, GetWindowText | Pobiera tytuł aktywnego okna użytkownika | Monitoring aktywnego okna |
| kernel32.dll | CreateMutex | Zapobiega uruchomieniu wielu instancji malware | Mutex (single instance) |
| advapi32.dll | RegCreateKeyEx, RegSetValueEx, RegQueryValueEx | Zapis i odczyt danych w rejestrze systemowym | Persistence (rejestr) |
| kernel32.dll | SetThreadExecutionState | Zapobiega przejściu systemu w stan uśpienia | Anti-sleep |
| brak (.NET) | RijndaelManaged, MD5CryptoServiceProvider | Szyfrowanie/odszyfrowywanie danych i stringów | Obfuskacja stringów |
| brak (.NET) | GZipStream | Kompresja i dekompresja danych | Kompresja payloadu |
| brak (.NET) | Random | Generowanie losowych wartości | Randomizacja |
| user32.dll | AddClipboardFormatListener | Monitorowanie zmian w schowku systemowym | Clipboard hijacking |
