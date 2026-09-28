# ML\_algo\_cursus

Oefeningen en labs voor het vak **Machine Learning Algorithms (AP, 2e jaar)**.

## Bronnen

* Hands-On Machine Learning – Aurélien Géron
* Artificial Intelligence: A Modern Approach – Russell & Norvig

***

## Overzicht setup met een devcontainer

In deze handleiding leer je stap voor stap:

1. Hoe je de repository **forkt** (eigen kopie maken)
2. Hoe je **Git instelt op je Windows-machine**
3. Hoe je de **devcontainer opent in VS Code**
4. Hoe je **wijzigingen commit & pusht** naar je eigen fork
5. Hoe je **updates binnenhaalt** van de hoofdbranch

---

### 1. Fork de repository

Een **fork** is je persoonlijke kopie van de repo op GitHub.
Je werkt in je eigen fork, maar kan later updates uit de originele repo (`upstream`) binnenhalen.

1. Ga naar de originele repository in je browser:
   `https://github.com/ML-Algorithms-2627/ML-Algo-student`
2. Klik op **"Fork"** (rechtsboven).
3. Kies je eigen GitHub-account als bestemming.
4. Laat **"Copy the `main` branch only"** aangevinkt en klik **Create fork**.

Je fork staat nu op:
`https://github.com/<JOUW_GEBRUIKERSNAAM>/ML-Algo-student`

> **Waarom forken?** Je kan vrij pushen zonder de originele repo te verstoren.
> Via `upstream` haal je later nieuwe oefeningen van de lector binnen.

---

### 2. Git installeren op je Windows-host

```powershell
winget install --id Git.Git -e
```

Of download van https://git-scm.com/download/win (64-bit).

**Check:** `git --version` moet `2.4x.x.windows.1` tonen.

---

### 3. Git configureren

Stel je naam en e-mail in **op Windows** (de devcontainer neemt dit over):

```powershell
git config --global user.name "Jouw Naam"
git config --global user.email "jouw.email@student.com"
```

---

### 4. Authenticatie (eenmalig)

Kies een van deze methodes:

#### A — GitHub CLI (aanbevolen)

```powershell
winget install --id GitHub.cli -e
gh auth login
```

Kies: **GitHub.com** > **HTTPS** > **Yes** > log in via browser.

#### B — SSH-key

```powershell
type C:\Users\%USERNAME%\.ssh\id_ed25519.pub
```

Voeg de output toe op https://github.com/settings/ssh/new

#### C — Personal Access Token

Maak een token aan op https://github.com/settings/tokens (klassiek, scopes: `repo`).
Bewaar het:

```powershell
git config --global credential.helper wincred
```

Bij de eerste push plak je het token.

---

### 5. Devcontainer openen

Clone **je fork** en open in VS Code:

```powershell
git clone https://github.com/<JOUW_GEBRUIKERSNAAM>/ML-Algo_student.git
cd ai_programming_student
code .
```

VS Code vraagt: **"Reopen in Container?"** → klik **Reopen**.
(Of `F1` → **"Reopen in Container"**)

De container:
- Trekt `ghcr.io/astral-sh/uv:python3.13-trixie` binnen
- Installeert Git
- Voert `uv sync` uit (Python packages)



---

### 6. Werken met Git in de container

```bash
git add .
git commit -m "Beschrijving van wat je veranderd hebt"
git push origin main
```

Je kan ook de VS Code Git UI gebruiken: Source Control-icoon (`Ctrl+Shift+G`).

---

### 7. Updates van de lector binnenhalen

**Eenmalig** — voeg de originele repo toe:

```bash
git remote add upstream https://github.com/ML-Algorithms-2627/ML-Algo-student.git
```

**Periodiek** — haal nieuwe oefeningen binnen:

```bash
git fetch upstream
git checkout main
git merge upstream/main
git push origin main
```

Doe dit voor elke les zodat je altijd de laatste versie hebt.


---

### 8. Problemen oplossen

| Probleem | Oplossing |
|---|---|
| `git: not found` in container | `F1` → **"Rebuild Container"** |
| `Permission denied (publickey)` | SSH-key toevoegen aan GitHub (stap 4) |
| `could not read Username` | `gh auth login` op **host** (niet in container) |
| Geen "Reopen" prompt | `F1` → **"Reopen in Container"** |
| Wijzigingen niet zichtbaar | Source Control (`Ctrl+Shift+G`) → bestanden **stage**-en |



***

## Werken met notebooks

* Open een `.ipynb` bestand
* Kies de Python kernel (in de container)
* Run cellen

Je hoeft **geen Jupyter server zelf te starten**

***

## Extra packages installeren (nodig in een later stuk van de cursus)

De container start **minimaal en snel**.  
Heb je extra libraries nodig, installeer ze on-demand:

### Deep learning

```bash
uv pip install -r requirements-dl.txt
```

### NLP

```bash
uv pip install -r requirements-nlp.txt
```

### Extra ML tools

```bash
uv pip install -r requirements-advanced.txt
```

***

## Waarom deze aanpak

* Snelle startup (geen zware installs)
* Minder fouten bij setup
* Flexibel: installeer enkel wat je nodig hebt

***

# Problemen oplossen

## OpenCV error (cv2)
Indien je een fout krijgt zoals:
`libGL.so.1 not found`

Voer uit in de container:
```bash
apt-get update && apt-get install -y libgl1
```

***




# 🧪 Oefeningen

## Automatische feedback (via GitHub Actions)

Na elke push wordt er via **GitHub Actions** automatisch een testbestand gedraaid.  
Dit is **geen formeel examen** of officiële evaluatie, maar een **hulpmiddel** om je te begeleiden — vooral als je deelneemt aan het oefeningenmoment.

- De tests controleren of je oplossingen correct zijn.
- Je krijgt feedback in de **Actions**-tab van jouw fork op GitHub.
- Gebruik deze feedback om je code te verbeteren.

> ⚠️ **Experimentele opzet**  
> Dit systeem wordt stap voor stap uitgerold en kan nog wijzigen. Laat gerust weten als je problemen tegenkomt of suggesties hebt.
***