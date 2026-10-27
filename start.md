# Zakres Wsparcia – Terraform Enterprise (TFE)

---

## Faza 1 – Uruchomienie (jednorazowo)

### Wdrożenie TFE
- [ ] Instalacja aplikacji TFE na klastrze Kubernetes
- [ ] Konfiguracja HA (min. 2 repliki, PDB)
- [ ] Procedura DR – zdefiniowany RTO/RPO, udokumentowany runbook


---

### Integracja tożsamości
- [ ] SSO/SAML z Entra ID (lub innym IdP)
- [ ] Mapowanie grup AD na role TFE (admin / operator / reader)
- [ ] Weryfikacja logowania end-to-end z min. jednym zespołem pilotażowym

---

### Telemetria
- [ ] Eksport logów TFE do centralnego systemu (SIEM / log aggregator)
- [ ] Eksport metryk (CPU, pamięć, kolejki workerów) do systemu monitoringu
- [ ] Zdefiniowanie alertów dla kluczowych progów (worker queue, błędy API)

---

### Szkolenie startowe
- [ ] Warsztat (0.5–1 dzień) dla core teamu platformy
- [ ] Przekazanie materiałów: architektura TFE, workflow operacyjny, procedury eskalacji
- [ ] Zdefiniowanie kto jest L1/L2/L3 po stronie klienta


---

## Faza 2 – Bieżące Utrzymanie

### Co tydzień

- [ ] **Health check** – klaster TFE, PostgreSQL (backend state), storage bucket
- [ ] **Wsparcie L2/L3** – locki na state, błędy workerów, awarie API, problemy z providerami
- [ ] **Drift check** – weryfikacja odchyleń w krytycznych workspace'ach

> **❓ Pytania:**
> - Jak zgłaszacie incydenty – Jira, ServiceNow, e-mail, Teams? Jaki jest wymagany SLA na odpowiedź L2/L3?
> - Które workspace'e są „krytyczne" i wymagają drift checku – czy lista jest zdefiniowana?

---

### Co miesiąc

- [ ] **Patch management** – aktualizacje minor TFE w uzgodnionym oknie serwisowym
- [ ] **Aktualizacja providerów** – przegląd i podbijanie wersji AzureRM / GCP / AWS w ustandaryzowanych modułach
- [ ] **Test DR** – wyrywkowe odtworzenie bazy i plików stanu z backupu

> **❓ Pytania:**
> - Czy jest zdefiniowane okno serwisowe (dzień/godzina) akceptowalne dla patchy?
> - Kto zatwierdza aktualizacje providerów po stronie klienta – czy wymagany jest Change Management?

---

### Co kwartał

- [ ] **Major upgrade TFE** – planowane okno, rollback plan, testy regresyjne po aktualizacji
- [ ] **Audyt bezpieczeństwa** – przegląd uprawnień, rotacja tokenów serwisowych, audyt dostępu do workspace'ów
- [ ] **Strategic review** – utylizacja licencji, roadmapa, feedback od zespołów deweloperskich

> **❓ Pytania:**
> - Czy upgrade'y major wymagają formalnego procesu CAB / Change Advisory Board?
> - Kto bierze udział w strategic review po stronie klienta (platforma, architekci, management)?

---

## Faza 3 – Doradztwo i Rozwój

> [!NOTE]
> Poniższe tematy są opcjonalne – wybieramy wspólnie priorytety na podstawie aktualnych potrzeb.

### Onboarding nowych projektów
- [ ] Zdefiniowanie standardowego szablonu workspace (zmienne, backend, tagi)
- [ ] Dokumentacja ścieżki onboardingu dla nowego zespołu
- [ ] Opcjonalnie: automatyczny „Workspace Vending Machine" (self-service przez API / formularz)

> **❓ Pytania:**
> - Ile nowych projektów/zespołów rocznie trafia na platformę?
> - Czy self-service jest wymagany teraz, czy to perspektywa przyszłości?

---

### Architektura kodu Terraform
- [ ] Warsztaty/sesja doradcza: monolit vs. mikro-state'y
- [ ] Rekomendacja podziału na warstwy (np. foundation → network → app)
- [ ] Zdefiniowanie zasad zarządzania zależnościami między state'ami (`terraform_remote_state` vs. outputs)

> **❓ Pytania:**
> - Czy macie już istniejący kod Terraform do zrefaktoryzowania, czy zaczynamy od zera?
> - Czy różne zespoły piszą własne moduły niezależnie, czy jest centralny rejestr?

---

### Private Module Registry
- [ ] **Cloud Foundation** – Landing Zones, Resource Groups, IAM
- [ ] **Network** – VNet/VPC, Firewalle, WAF, Private Endpoints
- [ ] **Identity** – Entra ID, App Registrations

> **❓ Pytania:**
> - Które domeny są pilne – cloud foundation, sieć czy tożsamość?
> - Czy moduły mają przechodzić review/zatwierdzenie przed publikacją? Przez kogo?

---

### CI/CD dla Terraform
- [ ] Szablon pipeline'u z: `fmt`, `validate`, `tfsec`/`checkov`, plan na PR, apply na merge
- [ ] Integracja z GitHub Actions lub Azure DevOps (w zależności od stacku klienta)
- [ ] Konfiguracja notyfikacji (Slack / Teams) o wynikach planu/apply

> **❓ Pytania:**
> - Który system CI/CD jest w użyciu – GitHub Actions, Azure DevOps, GitLab CI?
> - Czy preferujecie `tfsec`, `checkov`, czy macie już narzędzie do skanowania IaC?

---

### Policy-as-Code
- [ ] Zdefiniowanie katalogu wymaganych polityk (tagging, regiony, Private Link)
- [ ] Implementacja w OPA (Conftest) lub Sentinel (natywny dla TFE)
- [ ] Tryb `advisory` (tylko ostrzeżenie) lub `mandatory` (blokada apply) – uzgodniony per polityka

> **❓ Pytania:**
> - Czy organizacja ma zdefiniowane polityki bezpieczeństwa/compliance dla chmury (np. wymagania CIS, ISO, własne)?
> - OPA/Conftest czy Sentinel – czy jest preferowana technologia?
> - Kto jest właścicielem polityk – Security, Platforma, Architekci?

---

## Otwarte pytania – model współpracy

> [!IMPORTANT]
> Poniższe decyzje determinują całą architekturę wsparcia i muszą być ustalone na pierwszym spotkaniu.

- [ ] **Model uruchamiania planów** – Pełne TFE (agenty, UI, API) czy TFE tylko jako backend state + rejestr modułów, a pipeline'y na własnych runnerach?
- [ ] **Komunikacja i ticketing** – Jira, ServiceNow, Teams, e-mail? Jaki jest oczekiwany SLA na odpowiedź i rozwiązanie?
- [ ] **Poziom dokumentacji** – Formalne runbooki (bus-factor prevention) czy self-documenting code + README wystarczy?
- [ ] **Model finansowania zmian** – Czy zmiany rozwojowe (Faza 3) są w abonamencie, czy rozliczane osobno (T&M / story points)?
