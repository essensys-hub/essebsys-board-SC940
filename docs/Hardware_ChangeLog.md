# Historique des Modifications Matérielles (SC940D)

Ce document retrace l'historique des révisions du PCB SC940D tel qu'extrait des fichiers de conception.

| Version | Description | Criticité |
| :--- | :--- | :--- |
| **Release 16.0** | **Version Actuelle**. Ajout des valeurs de tolérance pour la taille des trous de Pads/Vias. | **Caution** |
| **Release 15.1** | Support du texte PCB multi-lignes ajouté. Note : vérifier le rendu du texte sur les anciens logiciels CAM. | Caution |
| **Release 15.0** | Support des masques de soudure séparés pour le dessus et le dessous des pads ajouté. | Caution |

## Modifications Anciennes / Non Vérifiées
> [!NOTE]
> Les anciennes révisions (Pré-15.0) ne sont pas détaillées dans l'export actuel. Veuillez vous référer aux archives dans `SC940D/_Archives` si disponibles.

## Problèmes Connus (Design Actuel)
-   **Manque de Documentation**: Le module "Cœur" (`SC943`) manque de schéma détaillé dans l'arborescence principale du projet.
-   **Saut de Désignateurs**: Les désignateurs des composants sautent des valeurs (ex: Relais K1-K17, puis un trou ?), typique des conceptions ayant évolué.
