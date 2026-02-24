# Active Directory & Cybersecurity Home Lab 🛡️

## 📌 Project Overview
Celem tego projektu było zbudowanie od podstaw wirtualnego środowiska laboratoryjnego (Home Lab) symulującego małą sieć firmową (Enterprise Network). Projekt służy do nauki administracji systemami Windows Server, zarządzania tożsamością (Active Directory) oraz testowania bezpieczeństwa sieciowego (Red Teaming/Pentesting).

### 🎯 Główne cele:
- Wdrożenie kontrolera domeny (Domain Controller) na Windows Server 2022.
- Konfiguracja usług sieciowych (DNS, DHCP, NAT Network).
- Zarządzanie stacjami roboczymi (Windows 10/11) w środowisku domenowym.
- Podstawowy rekonesans sieciowy przy użyciu Kali Linux (Nmap).

---

## 🏗️ Network Topology (Topologia)
Środowisko zostało zbudowane w oparciu o hypervisor **Oracle VirtualBox**. Wszystkie maszyny pracują w odizolowanej sieci `NAT Network` (10.0.2.0/24), co pozwala na komunikację między nimi przy zachowaniu dostępu do Internetu.

| Machine Name | OS | Role | IP Address |
| :--- | :--- | :--- | :--- |
| **DC01** | Windows Server 2022 | Domain Controller, DNS | `10.0.2.10` (Static) |
| **WIN10-CLIENT** | Windows 10 Pro | Domain Workstation | DHCP |
| **KALI-BOX** | Kali Linux | Attacker / Auditor | DHCP |

---

## 🛠️ Implementation Details (Co zostało zrobione)

### 1. Konfiguracja Windows Server (DC01)
- Instalacja systemu w wersji **Desktop Experience**.
- Konfiguracja statycznego adresu IP oraz DNS (wskazującego na localhost `127.0.0.1`).
- Instalacja roli **AD DS (Active Directory Domain Services)**.
- Promocja serwera do roli Kontrolera Domeny (utworzenie nowego lasu: `cyberlab.local`).

### 2. Konfiguracja Klienta (Workstation)
- Zmiana ustawień DNS karty sieciowej, aby wskazywała na serwer DC (`10.0.2.10`).
- Weryfikacja komunikacji poleceniem `ping cyberlab.local`.
- Dołączenie stacji roboczej do domeny. Logowanie poświadczeniami Administratora domeny.

### 3. Security Reconnaissance (Kali Linux)
- Przeprowadzenie skanowania sieci w celu wykrycia usług działających na kontrolerze domeny.
- Użyte narzędzie: **Nmap**.
- Wykryte otwarte porty:
    - `53` (DNS)
    - `88` (Kerberos)
    - `389` (LDAP)
    - `445` (SMB)

---

## 🐛 Troubleshooting & Challenges (Rozwiązane problemy)
Podczas budowy laba napotkałem krytyczny błąd instalacji Windows Server na VirtualBox 7.x:
> *"Windows cannot find the Microsoft Software License Terms"*

**Analiza problemu:**
VirtualBox w trybie domyślnym próbuje wykonać "Unattended Installation", montując wirtualny napęd Floppy, co powoduje konflikt z instalatorem Windows Server.

**Rozwiązanie:**
1. Ręczne usunięcie kontrolera Floppy z ustawień maszyny wirtualnej.
2. Wymuszenie ręcznej instalacji poprzez zaznaczenie opcji *"Skip Unattended Installation"* przy tworzeniu nowej maszyny.
3. Po tym zabiegu instalator uruchomił się poprawnie.

---

## 📸 Screenshots
![jan kowalski windows](https://github.com/user-attachments/assets/6c14f60c-eb46-40cf-82f1-972c9386ef7d)
![system jan kowalski ](https://github.com/user-attachments/assets/38523fb0-5414-4ea9-8b0f-97a8e2109545)
![ipconfig Jan Kowalski](https://github.com/user-attachments/assets/0c6dd968-f1a6-418e-af02-fdb8e447ce02)
![domena Windows Serwer](https://github.com/user-attachments/assets/a6931bf4-f36d-4602-8d7a-378ebe8f4e97)


---

## 🚀 Future Roadmap
Planowany rozwój laboratorium:
- [ ] Utworzenie struktury organizacyjnej (OU) i użytkowników w AD.
- [ ] Konfiguracja Group Policy Objects (GPO) - np. blokada Panelu Sterowania, tapeta firmowa.
- [ ] Wdrożenie serwera plików (File Server) i mapowanie dysków.
- [ ] Testy penetracyjne Active Directory (np. ataki na SMB).



# 🛠️ Enterprise Windows Infrastructure Lab
**Lokalne Środowisko Domenowe Active Directory**

## 📖 Opis projektu
Projekt skupia się na budowie, konfiguracji i zabezpieczaniu infrastruktury IT opartej o systemy Microsoft Windows. Celem jest symulacja rzeczywistych scenariuszy administracyjnych w środowisku korporacyjnym.

---

## 🏗️ Architektura Systemu
* **Domena:** `cyberlab.local`
* **Kontroler Domeny:** Windows Server 2022 (IP: `10.0.2.10`)
* **Stacja Robocza:** Windows 10 Pro (Użytkownik: `jan.kowalski`)
* **Sieć:** VirtualBox Internal Network z dostępem do Internetu przez NAT

---

## 🚀 Zrealizowane Kamienie Milowe

### 1. Zarządzanie Zasobami i Personalizacją (GPO)
Wdrożono scentralizowane zarządzanie stacjami roboczymi przy użyciu **Group Policy Management**:
* **Firmowa Tapeta:** Automatyczne wymuszenie tła pulpitu z zasobu sieciowego `\\10.0.2.10\Zasoby\tapeta.jpg`.
* **Mapowanie Dysków:** Automatyczne podmontowanie dysku sieciowego **Z:** wskazującego na folder `\\10.0.2.10\Dane` przy użyciu **GPO Preferences**.

### 2. Cyberbezpieczeństwo i Kontrola Dostępu
* **Blokada Magazynów Wymiennych:** Skonfigurowano restrykcyjną polisę `All Removable Storage classes: Deny all access`. 
* **Efekt:** Całkowite zablokowanie możliwości odczytu/zapisu na urządzeniach USB dla użytkowników nieuprawnionych.

### 3. Zaawansowany Troubleshooting (Case Studies)
Podczas sesji rozwiązano krytyczne problemy techniczne:
* **Błąd Kerberos (Clock Skew):** Naprawiono brak synchronizacji czasu między klientem a serwerem, co blokowało logowanie i aktualizację zasad.
* **Naprawa usługi W32Time:** Rozwiązano błąd systemowy **1058** (usługa wyłączona) oraz błąd braku danych synchronizacji.

---

## 💻 Technical Cheat Sheet (Użyte komendy)

### Diagnostyka GPO
```powershell
gpupdate /force       # Wymuszenie natychmiastowej aktualizacji zasad
gpresult /r           # Wyświetlenie raportu zastosowanych polis (RSOP)


:: Odblokowanie i start usługi czasu
sc config w32time start= auto
net start w32time

:: Wymuszenie synchronizacji z hierarchii domeny
w32tm /config /syncfromflags:domhier /update
w32tm /resync
# 🛡️ Enterprise Windows Infrastructure & Security Lab
**Środowisko: Windows Server 2022 | Windows 10 Pro | Active Directory**

## 📖 Przegląd Projektu
Projekt skupia się na budowie bezpiecznej infrastruktury domenowej, implementacji zaawansowanych zasad grup (GPO) oraz audycie bezpieczeństwa protokołów sieciowych. Lab symuluje rzeczywiste wyzwania administratora systemów, łącząc konfigurację usług z rozwiązywaniem problemów (troubleshooting).

---

## 🏗️ Architektura i Zarządzanie (OU & GPO)

### 1. Struktura Organizacyjna (OU)
Zaimplementowano hierarchiczną strukturę w Active Directory, dzieląc zasoby na jednostki organizacyjne (OU), co pozwala na precyzyjne stosowanie polityk bezpieczeństwa.
* Obiekty komputerowe (np. `comp1`) zostały przeniesione do dedykowanych jednostek OU, aby umożliwić zdalne zarządzanie.

### 2. Group Policy Preferences (GPO)
Wdrożono mapowanie dysków sieciowych przy użyciu **Group Policy Preferences** (GPP).
* Wykorzystano sekcję `User Configuration -> Preferences -> Windows Settings -> Drive Maps` do automatycznego montowania zasobów dla pracowników.

---

## 🔒 Bezpieczeństwo Danych (File Server & NTFS)

Folder `C:\dane` na serwerze został skonfigurowany zgodnie z zasadą **Least Privilege**:
* **Wyłączenie dziedziczenia:** Usunięto dziedziczenie uprawnień z nadrzędnego dysku (Inherited from: **None**), co widać po dostępnym przycisku "Enable inheritance".
* **Precyzyjna kontrola:** Dostęp został ograniczony wyłącznie do dedykowanych grup bezpieczeństwa (np. `GG_Dostep_Dane`), eliminując domyślne uprawnienia dla grupy `Everyone`.



---

🛡️ Enterprise Windows Infrastructure & Security Lab - Sesja 2
Środowisko: Windows Server 2022 | Windows 10 Pro | Active Directory

📖 Przegląd Sesji
Druga faza projektu skupiła się na wdrożeniu zaawansowanych mechanizmów kontroli dostępu, optymalizacji struktury Active Directory oraz przeprowadzeniu audytu bezpieczeństwa protokołu SMB. Kluczowym elementem było rozwiązanie problemów z komunikacją RPC oraz hardening systemu poprzez wyłączenie podatnych protokołów uwierzytelniania.

🏗️ Zarządzanie Strukturą i GPO (Active Directory)
1. Reorganizacja Jednostek Organizacyjnych (OU)
W celu umożliwienia precyzyjnego stosowania zasad grupy, obiekty komputerowe (np. comp1.cyberlab.local) zostały przeniesione z domyślnego kontenera do dedykowanej struktury OU.

Rozwiązano problem braku widoczności stacji roboczych przez serwer podczas zdalnego odświeżania polis.

2. Group Policy Preferences (GPP)
Wykorzystano Preferencje GPO do automatyzacji środowiska użytkownika:

Skonfigurowano mapowanie dysków sieciowych w sekcji User Configuration -> Preferences -> Windows Settings -> Drive Maps.

Wdrożono blokadę Panelu Sterowania oraz Ustawień systemowych dla jednostki OU "Pracownicy".

🔒 Bezpieczeństwo Danych i Uprawnienia NTFS
Folder zasobów C:\dane został zabezpieczony zgodnie z modelem Least Privilege:

Wyłączenie dziedziczenia: Usunięto dziedziczenie uprawnień z nadrzędnego systemu plików (Inherited from: None).

Hardening NTFS: Dostęp do zasobów został ograniczony wyłącznie do dedykowanych grup bezpieczeństwa, co potwierdza stan przycisku "Enable inheritance" (obecnie wyłączone).

🛠️ Case Studies: Troubleshooting Techniczny
Podczas sesji zdiagnozowano i rozwiązano krytyczne błędy komunikacji:

Błąd	Opis Problemu	Rozwiązanie
8007071a / 800706ba	"The RPC server is unavailable" - blokada zdalnego GPUpdate.	Odblokowanie reguł RPC/WMI w zaporze Windows Defender oraz weryfikacja profilu sieciowego.
System error 1219	Konflikt wielu sesji użytkownika z jednym zasobem sieciowym.	Zastosowanie komendy net use * /delete w celu wyczyszczenia aktywnych sesji przed audytem.

Eksportuj do Arkuszy

🕵️ Audyt Bezpieczeństwa (SMB Security Recon)
Przeprowadzono testy penetracyjne mające na celu wykrycie podatności na ataki typu Null Session (anonimowe logowanie):

Wynik testu: Próba nawiązania sesji anonimowej zakończyła się błędem System error 1937.

Analiza: Serwer skutecznie odrzucił połączenie z powodu wymuszenia bezpiecznych protokołów i całkowitego wyłączenia uwierzytelniania NTLM na rzecz Kerberosa.

Wniosek: Środowisko jest odporne na podstawowe techniki rekonesansu SMB i ataki typu Relay.

💻 Technical Cheat Sheet (Admin Commands)
DOS

:: Diagnostyka i czyszczenie sesji sieciowych
net use * /delete
net use \\10.0.2.10\ipc$ "" /u:""

:: Zarządzanie zaporą (PowerShell)
Enable-NetFirewallRule -DisplayGroup "Remote Scheduled Tasks Management"
Enable-NetFirewallRule -DisplayGroup "Windows Management Instrumentation (WMI)"

