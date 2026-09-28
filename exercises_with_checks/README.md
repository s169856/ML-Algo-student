# Checkpoints — hoe gebruik je dit in de les?

Automatische checkpoints per week: studenten vullen een opgave-notebook in en pushen het;
GitHub Actions voert het uit en controleert of het juist is. Resultaat is zichtbaar als
✅/❌ bij de commit in GitHub.

## Hoe het werkt (voor de student)

1. Open het checkpoint-notebook van de week, bv.
   `exercises_with_checks/week01/knn_checkpoint_opgave.ipynb`.
2. Vul alle cellen in. **Let op de gevraagde variabelennamen** (`accuracy_iris`,
   `mse_mall`, `antwoord_normaliseren`, …) — daarnaar wordt door de automatische
   controle gezocht. Verkeerde naam = checkpoint faalt.
3. Sla het notebook op **met alle cellen uitgevoerd** en push het naar je repo.
4. GitHub Actions draait automatisch (bij elke push die iets in
   `exercises_with_checks/` wijzigt). Na ±1–2 min zie je een groene vink (alle
   checks geslaagd) of een rood kruis (klik erop voor de details: welke oefening faalt en waarom).
5. Je mag zo vaak pushen als je wil tot alles groen is.

## Lokaal testen in de devcontainer (geen git nodig)

Je kan de checks ook **in de devcontainer** uitvoeren, zonder te pushen naar GitHub.
Dat is sneller voor feedback en je hebt er geen git-setup voor nodig.

1. **Open de repo in de devcontainer**  
   VS Code detecteert de `.devcontainer`-map en stelt voor de container te openen.
   Klik op *"Reopen in Container"*.  
   De devcontainer installeert automatisch alle dependencies (reken ±1 minuut).

2. **Vul het notebook in en voer alle cellen uit**  
   Open het checkpoint-notebook, bv.  
   `exercises_with_checks/week01/knn_checkpoint_opgave.ipynb`.  
   Sla het op **met alle cellen uitgevoerd** (ook de cellen die je niet aanpaste).

3. **Draai de pytest-checks**  
   Vanuit de root van de repo:
   ```bash
   pytest exercises_with_checks/checks/test_week01.py -v --tb=short
   ```
   Je ziet per test ✅ (geslaagd) of ❌ (gefaald met uitleg).

4. **Pas aan en herhaal**  
   Verbeter de fouten in het notebook, voer de gewijzigde cellen opnieuw uit,
   sla op en draai `pytest` opnieuw. Blijf doen tot alles groen is.

> ⚠️ **Belangrijk**: Sla het notebook telkens op **met uitgevoerde cellen** vóór je
> de tests draait. De pytest voert het notebook namelijk opnieuw uit vanuit het
> opgeslagen bestand — niet-werkcellen worden overgeslagen en missen dan variabelen.

# Oplossingen

Oplossingsfiles (al dan niet volledig, soms worden delen van een oefening als oefening gelaten voor de studenten) bevinden zich in een andere folder `solutions` binnen de map per week.
> ⚠️ **Belangrijk**: deze `solutions` folder zal dus files bevatten die wekelijks worden overgeschreven. Zet hier niet je eigen oplossing in, want dan wordt die met `git pull` overschreven of leidt die tot een merge conflct. 
