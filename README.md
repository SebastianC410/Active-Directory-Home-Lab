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
*(Tutaj wklej zrzuty ekranu, np. widok Server Managera, wynik polecenia `ipconfig /all` na kliencie, lub skan z Nmapa)*

---

## 🚀 Future Roadmap
Planowany rozwój laboratorium:
- [ ] Utworzenie struktury organizacyjnej (OU) i użytkowników w AD.
- [ ] Konfiguracja Group Policy Objects (GPO) - np. blokada Panelu Sterowania, tapeta firmowa.
- [ ] Wdrożenie serwera plików (File Server) i mapowanie dysków.
- [ ] Testy penetracyjne Active Directory (np. ataki na SMB).
