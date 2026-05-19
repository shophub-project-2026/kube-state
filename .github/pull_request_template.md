## Opis promene
Molimo detaljno opišite koje izmene donosi ovaj PR (koji klaster, koji release, koje value override).

## Vrsta promene
Označite vrstu izmene:
- [ ] `feat`: Nov release ili nov klaster
- [ ] `fix`: Ispravka konfiguracije ili values override
- [ ] `chore`: Bump verzije charta, sync metadata
- [ ] `docs`: Dokumentacija (README, komentari)

## Cluster impact
- [ ] Promena utiče samo na `clusters/local/` (dev/local).
- [ ] Promena utiče na produkcioni klaster (zahteva dodatnu reviju).
- [ ] Helm chart verzija u `helm.yaml` je validna i postoji u registry-ju.

## Checklist
- [ ] YAML fajlovi prolaze `yamllint`.
- [ ] `helm.yaml` referencira ispravnu OCI ili Git putanju do charta.
- [ ] `values.yaml` override-i su minimalni — samo polja koja se razlikuju od defaulta charta.
- [ ] Commit poruke prate Conventional Commits format.

## Povezani tiketi/issues
(Npr. Resolves #123)
