# Estimation du Budget de Puissance : SC940D

> [!WARNING]
> Ce budget est une estimation théorique basée sur les fiches techniques des composants et des valeurs typiques. Une vérification réelle sur le matériel est requise.

## 1. États du Système
Nous définissons deux états de puissance principaux pour l'analyse :
1.  **Repos / Veille (Idle)**: MCU en marche, Module communication en réception/sommeil, aucun relais actif.
2.  **Transmission Max**: Module communication en émission (TX), MCU actif.
3.  **Activation Complète**: Tous les relais activés (Pire Cas).

## 2. Analyse de Consommation par Composant

### A. Cœur Logique (Rail 3.3V / 5V)
| Composant | État | Courant (Est.) | Puissance (Est.) |
| :--- | :--- | :--- | :--- |
| **PIC16F946** | Marche (4MHz) | ~2 mA | 10 mW |
| **PIC16F946** | Sommeil | < 1 µA | ~0 mW |
| **SC943 (Module)** | Repos/RX | ~10-20 mA | ~50-100 mW |
| **SC943 (Module)** | TX (RF Haut) | ~100-150 mA | ~500 mW |
| **Protection/Fuite** | Veille | < 100 µA | < 1 mW |

### B. Actionneurs (Rail 12V)
*Note : Les bobines des relais sont les principaux consommateurs d'énergie.*

| Composant | Quantité | Courant Unitaire (12V) | Puiss. Unitaire | Puiss. Max Totale |
| :--- | :--- | :--- | :--- | :--- |
| **Finder 40.61** | ~12 | ~40 mA (0.5W) | 0.5 W | 6.0 W |
| **Finder 34.51** | ~5 | ~15 mA (0.17W) | 0.17 W | 0.85 W |

## 3. Estimation Totale de Puissance (Entrée 12V)

### Scénario 1 : Veille (Aucun Relais)
-   **Charge Logique**: ~20mA @ 5V (via Buck)
-   **Efficacité Buck**: ~85%
-   **Courant Entrée (12V)**: ~1-10 mA.
-   **Puissance Totale**: **< 0.2 W**.

### Scénario 2 : Opération Typique (2 Relais Actifs + RX)
-   **Relays**: 2x 40.61 = 1 W.
-   **Logique**: 0.1 W.
-   **Puissance Totale**: **~1.1 W**.

### Scénario 3 : Pire Cas (Tous Relais ON + TX)
-   **Relais**: 6.85 W.
-   **Logique (TX)**: 0.5 W / 0.85 (Eff) ≈ 0.6 W.
-   **Puissance Totale**: **~7.5 W**.

## 4. Recommandations
1.  **Dimensionnement PSU**: L'alimentation 12V amont doit être dimensionnée pour au moins **10W (ou ~1A)** pour gérer le pire cas et le courant d'appel.
2.  **Thermique**: 7.5W représente une chaleur significative. Assurez-vous que le boîtier permet une convection passive si l'état "Tout ON" est possible.
3.  **Optimisation Firmware**: Implémentez un "démarrage progressif" ou une activation séquencée des relais pour éviter que les pics de courant (appel) ne saturent le rail d'alimentation simultanément.
