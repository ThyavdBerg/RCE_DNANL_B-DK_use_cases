### Versiebeheer en samenwerken met Git

Deze werkinstructie ondersteunt medewerkers van Team Databeheer bij het gezamenlijk wijzigen van SQL-scripts en Jupyter-notebooks in de bestaande GitHub-repository. Dezelfde basiswerkwijze geldt voor GitLab; GitHub gebruikt de term *pull request* (PR) en GitLab *merge request* (MR).

### Uitgangspunten

- `main` bevat alleen gereviewde wijzigingen en is beschermd tegen rechtstreekse commits.
- Iedere afgebakende wijziging wordt in een eigen, behapbare branch uitgevoerd.
- Minimaal één collega beoordeelt een wijziging voordat deze met `main` wordt samengevoegd.
- Brondata, persoonsgegevens, wachtwoorden, tokens, lokale databasebestanden en andere secrets worden niet in Git opgenomen.
- Alleen gereviewde logica wordt later door de data engineer in de GitLab-repository en de geautomatiseerde DAP-keten geïntegreerd.

### Git stappenplan

Een medewerker plaatst de bestaande repository eenmalig met `git clone <repository-url>` op de laptop. Daarna wordt iedere wijziging volgens onderstaande stappen uitgevoerd. Een branchnaam beschrijft het doel, bijvoorbeeld `feature/controle-dubbele-monumenten`, `fix/lege-geometrie` of `docs/uitleg-bronvelden`. Met een prefix zoals `feature/`, `fix/` of `docs/` geef je het type wijziging aan.

| Stap | Handeling | Resultaat |
|---|---|---|
| 1. Begin met de laatste versie | Voer `git switch main` en `git pull` uit | De lokale `main` is gelijk aan de gedeelde versie |
| 2. Maak een branch | Voer `git switch -c <type>/<korte-omschrijving>` uit, waarbij het type bijvoorbeeld `feature`, `fix` of `docs` is | De wijziging staat los van `main` |
| 3. Werk en test lokaal | Voer het notebook van begin tot eind uit en test losse SQL tegen DuckDB | De wijziging is reproduceerbaar op de eigen laptop |
| 4. Controleer de wijziging | Controleer met `git status` en `git diff` wat wordt vastgelegd. Zet bestanden die nooit in Git horen in `.gitignore`, bijvoorbeeld `*.duckdb` voor lokale DuckDB-databasebestanden | Alleen bedoelde bestanden en wijzigingen gaan mee |
| 5. Leg de wijziging vast | Voer na de controle `git add .` uit. Maak daarna een commit, bijvoorbeeld `git commit -m "Voeg controles op dubbele monumenten toe"` | Git bewaart alle gecontroleerde wijzigingen als een herkenbaar lokaal wijzigingspunt |
| 6. Deel de branch | Voer `git push -u origin <branchnaam>` uit | De branch staat op GitHub en is beschikbaar voor collega's |
| 7. Vraag review | Open een PR in GitHub, beschrijf het doel, de gebruikte bron en de uitgevoerde controles en wijs een reviewer aan | Een collega kan de wijziging beoordelen voordat deze in `main` komt |
| 8. Verwerk feedback | Pas de bestanden aan en maak en push aanvullende commits | Dezelfde PR wordt bijgewerkt met de verbeteringen |
| 9. Voeg samen en ruim op | Laat de goedgekeurde PR via GitHub samenvoegen met `main`. Voer daarna lokaal `git switch main` en `git pull` uit en verwijder de lokale branch met `git branch -d <branchnaam>`. Als GitHub de remote branch niet automatisch heeft verwijderd, verwijder deze dan in GitHub | De lokale `main` bevat de gereviewde wijziging en de gebruikte branch is opgeruimd |

`git add .` voegt alle niet-genegeerde wijzigingen onder de huidige repositorymap toe aan de volgende commit. Controleer daarom altijd eerst `git status` en `git diff`. Een `.gitignore` kan bijvoorbeeld het volgende bevatten:

```text
*.duckdb
.env
__pycache__/
.ipynb_checkpoints/
```

### Review

De reviewer controleert:

- of het notebook van begin tot eind reproduceerbaar is;
- of de SQL inhoudelijk klopt en aansluit op de ontwikkel- en naamconventies;
- of een test of controle de verwachte uitkomst onderbouwt;
- of geen brondata, persoonsgegevens of secrets zijn opgenomen;
- of de logica en aannames voldoende zijn beschreven voor overdracht naar het DAP.

Feedback wordt in dezelfde PR verwerkt met aanvullende commits. De wijziging wordt pas samengevoegd wanneer de reviewer akkoord geeft en eventuele geautomatiseerde controles slagen. GitHub ondersteunt dit met branchregels voor verplichte PR's, goedkeuringen en statuscontroles [GitHub Docs — branches en reviews (2026)].

### Notebookconflicten

Twee personen wijzigen bij voorkeur niet gelijktijdig hetzelfde notebook. Een notebookbestand bestaat uit JSON en veroorzaakt daardoor sneller een moeilijk leesbaar mergeconflict dan een los SQL-bestand.

Als `main` tijdens het werk is gewijzigd, haalt de auteur deze wijzigingen eerst op in de eigen branch:

```bash
git switch <branchnaam>
git pull origin main
```

De auteur lost eventuele conflicten lokaal op, voert het notebook opnieuw uit en commit en pusht de oplossing. Bij een onduidelijk notebookconflict stemmen de auteurs af welke versie leidend is; gebruik geen gedwongen push naar `main`.

Voor aanvullende uitleg en oefeningen over remotes, branches, pull requests en reviews kan de Carpentries-les worden gebruikt [Collaborative Git and GitHub lesson (2025)].

Bronnen: 

- **Collaborative Git and GitHub lesson (2025)** — The Carpentries Incubator (2025). *Collaborative version control with Git and GitHub*, bijgewerkt 04-03-2025. https://carpentries-incubator.github.io/collaborative-git-and-github-lesson/, geraadpleegd 12-08-2026.

- **GitHub Docs — branches en reviews (2026)** — GitHub, Inc. (2026). *About protected branches* en *Giving reviews*. https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches en https://docs.github.com/en/pull-requests/concepts/giving-reviews, geraadpleegd 12-08-2026.