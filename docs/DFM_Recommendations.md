# Recommandations DFM (Design for Manufacturing) & Optimisation

## 1. Empilement PCB & Fabrication
-   **Empilement Actuel**: 4 Couches (Sig/GND/PWR/Sig).
-   **Recommandation**: Assurez-vous que les profils d'impédance contrôlée sont spécifiés si le module de communication (`SC943`) utilise des lignes haute vitesse ou des antennes externes routées sur la carte principale.
-   **Tailles de Perçage**: Vérifiez que les mises à jour de tolérance de la "Release 16.0" sont compatibles avec le fabricant de PCB choisi (capacités standard vs avancées).

## 2. Testabilité (DFT)
-   **Points de Test**: La conception inclut `MIR1`, `MIR2`, `MIR3` (Mires fiduciales) mais manque de points de test explicitement étiquetés pour les signaux critiques (ex: `TP_3V3`, `TP_GND`, `TP_MCLR`).
-   **Recommandation**: Ajouter des pastilles de test dédiées (diamètre 1mm) pour :
    -   Rails de Tension (12V, 5V, 3.3V).
    -   Bus de Communication (Lignes TX/RX vers le module).
    -   Broches ICSP / Programmation pour le PIC16F946 (sauf si géré par le connecteur HE10).

## 3. Assemblage & Gestion Thermique
-   **Densité de Relais**: La carte embarque environ 17 relais.
    -   **Risque**: Échauffement localisé important si plusieurs relais adjacents (`K1`-`K3`, `K4`-`K9`) sont actifs simultanément.
    -   **Atténuation**: Assurez-vous que le boîtier dispose de fentes de ventilation au-dessus de la banque de relais. Vérifiez la largeur des pistes PCB pour le contact "Commun" des relais (~16A nécessite une largeur de cuivre significative ou des plans de masse).
-   **Dégagement des Composants**: Les borniers verticaux (`Phoenix Contact FFKDS`) sont hauts. Assurez-vous qu'ils n'interfèrent pas avec le couvercle du boîtier ou le placement de la carte fille (`SC943`).

## 4. Cycle de Vie des Composants
-   **PIC16F946**: C'est un produit ancien et mature. Vérifiez la disponibilité à long terme ou envisagez de migrer vers une série PIC16F1xxx ou PIC18F plus récente pour une meilleure densité de code et une consommation réduite.
-   **LMR12010**: Actif et bonne disponibilité.
-   **Relais**: Les séries Finder 40/34 sont des standards de l'industrie avec une excellente disponibilité.
