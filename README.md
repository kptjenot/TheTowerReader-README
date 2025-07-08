# TheTowerReader 🏰📲

[![Version](https://img.shields.io/badge/version-1.0.0-blue.svg)](https://thetowerreader.onrender.com/healthcheck?token=YOUR_TOKEN)
[![API](https://img.shields.io/badge/API-protected-green.svg)](#)
[![Status](https://img.shields.io/badge/status-online-brightgreen.svg)](https://thetowerreader.onrender.com/healthcheck?token=YOUR_TOKEN)
[![Render](https://img.shields.io/badge/hosted%20on-Render.com-blueviolet)](https://thetowerreader.onrender.com)
[![License: Proprietary](https://img.shields.io/badge/license-private-lightgrey.svg)](#)

**TheTowerReader** to aplikacja na Androida służąca do automatycznego rozpoznawania i zapisywania danych z gry mobilnej [**The Tower – Idle Tower Defense**](https://play.google.com/store/apps/details?id=com.TechTreeGames.TheTower).

Aplikacja wykorzystuje technologię OCR (ML Kit) do odczytu wyników z ekranu i przesyła je bezpośrednio do arkusza Google Sheets, umożliwiając ich dalszą analizę lub porównywanie progresu w grze.

System oparty jest na lekkim backendzie Flask + Google Sheets API, hostowanym na Render.com, i działa w pełni automatycznie po wybraniu screenshota z gry.

---
![TheTowerReader1](https://github.com/user-attachments/assets/6b00cb1b-fcd8-47a3-96fe-1a217ae4ff89) ![TheTowerReader2](https://github.com/user-attachments/assets/34706e0c-c779-4f29-bf89-52421fc6cae3)
---
## ✨ Główne funkcje

### 📱 Aplikacja Android:
- 🔎 Rozpoznawanie tekstu z ekranu (OCR) przy użyciu ML Kit.
- 📊 Parsowanie wybranych danych (np. `Tier`, `Total Coins`, `Date`, `Duration`, itp.).
- 🔁 Konwersja formatów:
  - `15,4K` → `15400`
  - `17,1T` → `17100000000000`
  - `10:44:53` → `HH:MM:SS`
- ☁️ Automatyczne przesyłanie danych do arkusza Google Sheets przez serwer pośredniczący.
- 📘 Obsługa statusu serwera, informacja o powodzeniu lub błędach.
- ✅ Ikony stanu uploadu i logowanie odpowiedzi.
- 🆕 **Wyświetlanie ostatniego zapisu z arkusza bezpośrednio w aplikacji (po udanym uploadzie)**
- 🖼️ **Podgląd przyciętego fragmentu zrzutu przed rozpoznaniem tekstu**
- 💬 **Porównanie surowego tekstu OCR z ostatnim wpisem w arkuszu w formie dwóch kolumn**
- 🔲 **Ramka „Nowe dane” z animacją, podświetlająca ostatni wpis z arkusza**

---

## 🖥️ Backend Flask (serwer pośredniczący)

Backend aplikacji działa na [Render.com](https://render.com) jako publiczny serwis webowy zbudowany na Flask + Python.  
Odpowiada za przyjmowanie danych z aplikacji i zapis do arkusza Google Sheets. Komunikacja odbywa się przez POST (`/submit`) i GET (`/status`, `/last-n`, `/healthcheck`).

### 🔐 Główne funkcje:
- Tokenowa autoryzacja dostępu do endpointów `/status`, `/last-n`, `/healthcheck`
- Zapis danych do arkusza Google Sheets z automatyczną konwersją typów
- Endpoint `/status` – pokazuje ostatni wpis w kolumnach A–G
- Endpoint `/last-n` – zwraca ostatnie n wpisów z danych aplikacji
- Endpoint `/healthcheck` – pokazuje status serwera i dostęp do Google API
- Blokada nieautoryzowanego dostępu do danych diagnostycznych

---

## 🔄 Przepływ danych

🧭 **1. Aplikacja .apk (TheTowerReader na Androida)**  
Rozpoznaje tekst ze zrzutu ekranu, przetwarza dane, konwertuje je i przesyła do backendu.

🖥️ **2. Serwer backendowy (Flask + Render)**  
Odbiera dane i zapisuje je do arkusza Google Sheets z konwersją formatów.

☁️ **3. Google Sheets**  
Działa jako magazyn danych – nowe rekordy trafiają do kolumn A–G w pierwszym pustym wierszu.

🗂️ **4. GitHub**  
Repozytorium kodu backendu oraz dokumentacji (np. README, `server.py`, konfiguracja).

⏱️ **5. UptimeRobot / monitoring (opcjonalnie)**  
Może pingować `/healthcheck` i powiadamiać, jeśli backend przestaje odpowiadać.

---

## 🛠️ Wymagania

### Android:
- Android 8+
- ML Kit (OCR)
- Internet do przesyłania danych

### Backend (Python):
- Python 3.10–3.13
- Flask
- gspread
- oauth2client
- `credentials.json` z dostępem do arkusza

### Google:
- Udostępniony arkusz Google Sheets (zakładka RAW Data)
- Włączone API:
  - Google Sheets API
  - Google Drive API

---

## 🌍 Endpointy backendu (zabezpieczone tokenem `?token=YOUR_TOKEN`)

| Endpoint        | Metoda | Opis |
|-----------------|--------|------|
| `/submit`       | POST   | Przyjmuje dane z aplikacji i zapisuje do arkusza (brak tokenu) |
| `/status`       | GET    | Zwraca ostatni wpis z kolumn A–G i liczbę rekordów |
| `/last-n`       | GET    | Zwraca ostatnie *n* wpisów (`?count=5`) |
| `/healthcheck`  | GET    | Zwraca status serwera, wersję, uptime, dostęp do arkusza |
| `/errors`       | GET    | Placeholder – może zwracać ostatnie błędy |
| `/`             | GET    | Landing page z komunikatem statusowym (publiczny) |

---

## 📦 Przykład danych

```json
[
  "Tier 10",
  "8203",
  "17100000000000",
  "26.06.2025",
  "153700",
  "15400",
  "10:44:53"
]
```

Dane te trafiają jako jeden wiersz do arkusza Google Sheets (kolumny A–G).

---

## 📁 Struktura projektu

```bash
thetowerreader-backend/
├── server.py             # Główny plik Flask
├── credentials.json      # Klucz serwisowy do Google API (lokalnie lub z Render)
├── requirements.txt      # Lista zależności Python
└── README.md             # Dokumentacja
```

---

## 🔐 Prawa i dostęp

Ten projekt ma charakter demonstracyjny i jest udostępniony wyłącznie w celach prezentacyjnych (portfolio).  
Wszelkie prawa zastrzeżone. Kod nie jest przeznaczony do ponownego użycia ani dystrybucji bez zgody autora.

---

## ✅ Plany rozwoju

- [ ] (BUG) Poprawić pobieranie daty (aplikacja bierze aktualną, zamiast z nazwy screena)
- [ ] Historia wysyłek w aplikacji
- [ ] Edytowalność danych przed wysyłką
- [ ] Automatyczny backup danych do drugiego arkusza
- [ ] Skalowanie obszaru OCR z poziomu aplikacji
- [ ] Zmiany w interfejsie

---

## 👨‍💻 Autor

Projekt: [Kapitan Jenot](https://github.com/KapitanJenot)  
Współpraca techniczna: ChatGPT (OpenAI)  
Licencja: prywatna (All rights reserved)
