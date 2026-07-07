# CLAUDE.md — Meridia RP (serveur FiveM / QBCore)

> **Mode d'emploi :** place ce fichier tel quel à la racine de ton dossier serveur (à côté de `server.cfg`) sous le nom `CLAUDE.md`. Claude Code le lit automatiquement à chaque session — c'est la mémoire permanente du projet. Le prompt de démarrage à coller pour la première session se trouve tout en bas.

---

## 1. Le projet

Meridia RP est un serveur GTA V roleplay **FiveM** sous framework **QBCore**, en **français**, dont le concept casse le RP urbain classique : l'île est coupée en deux par une frontière militarisée façon guerre froide. Deux nations, deux économies, deux forces de l'ordre, une zone démilitarisée au centre de la map (désert + grand lac) où règnent la contrebande et le non-droit.

## 2. L'univers en résumé opérationnel

- **L'île : Meridia.** Ex-république unie, fracturée par une guerre civile (2017–2021) déclenchée par la découverte de l'**Azurium**, un minerai stratégique sous le lac central. Armistice signé en 2021 à Valdero. Nous sommes en 2026 : paix froide, tension maximale.
- **Sud : République Fédérale de Solvera.** Capitale San Verano (ex-Los Santos). Capitalisme dérégulé dirigé en coulisses par le Directoire (corporations). Monnaie : le **Sol**. Ambiance néons, inégalités, tout s'achète.
- **Nord : République Populaire de Kaldera.** Capitale Kalvik (ex-Paleto Bay). État-parti militarisé dirigé par la Maréchale Iska Varn. Monnaie : le **Kard**. Rationnement, propagande, marché noir, couvre-feu.
- **La Cicatrice.** Bande démilitarisée de 8 km traversant le désert de Cendra et la Mer Cendrée (ex-Alamo Sea). Mines, miradors, gisement d'Azurium gelé par traité. Un seul passage légal : le **Poste de Concordia**. Au centre, **Valdero** (ex-Sandy Shores) : ville sans loi, plaque tournante de la contrebande.
- **Règle d'ambiance :** aucun camp n'est « le bon » ; la menace permanente, c'est la reprise de la guerre.

## 3. Nommage obligatoire (à utiliser partout : blips, locales, zones, dialogues)

| Ancien nom GTA V | Nom Meridia | Territoire |
|---|---|---|
| San Andreas (l'île) | Meridia | — |
| Los Santos | San Verano | Sud |
| Vinewood | Aurora | Sud |
| Fort Zancudo | Le Verrou | Sud |
| Grand Senora Desert | Désert de Cendra | Zone neutre |
| Sandy Shores | Valdero | Zone neutre |
| Alamo Sea | La Mer Cendrée | Zone neutre |
| Route 68 / Senora Freeway | L'Axe de Concordia | Frontière |
| Grapeseed | Grenval | Nord |
| Paleto Bay | Kalvik | Nord |
| Mount Chiliad | Mont Kaldera | Nord |

**Jobs (table QBCore `name` → `label`) :**

| name | label | Camp | Rôle |
|---|---|---|---|
| pmsv | Police Métropolitaine de San Verano | Sud | Police publique |
| sentinel | Sentinel Corp | Sud | Sécurité privée du Directoire |
| fas | Forces Armées Solveriennes | Sud | Armée, tient le Verrou et la frontière sud |
| apk | Armée Populaire de Kaldera | Nord | Armée + maintien de l'ordre, frontière nord |
| vigie | La Vigie | Nord | Police secrète (whitelist strict) |
| blouses | Les Blouses Grises | Neutre | EMS/médecins tolérés des deux côtés |
| mica | MICA | Neutre | Observateurs internationaux (staff/events) |

**Gangs (table `gangs`) :** `filscicatrice` (Les Fils de la Cicatrice, cartel de Valdero), `loupscendre` (Les Loups de Cendre, déserteurs-pillards), `aube` (La Brigade de l'Aube, jusqu'au-boutistes nordistes), `enfants` (Les Enfants de Meridia, réunificationnistes clandestins).

## 4. Stack technique

- **Serveur :** FiveM (fxserver, dernière recette), géré via txAdmin.
- **Framework :** QBCore (`qb-core` + ressources standards `qb-*`).
- **Base de données :** MySQL/MariaDB via `oxmysql`.
- **Langages :** Lua (logique), JS/HTML/CSS (NUI).
- **Libs autorisées :** `ox_lib`, `PolyZone` (zones), et toute ressource **open source** publiée sur GitHub.

## 5. Arborescence des ressources

- Toutes les ressources custom du projet vont dans `resources/[meridia]/` et sont préfixées `meridia-` (ex. `meridia-border`, `meridia-zones`, `meridia-citizenship`).
- Les ressources QBCore d'origine restent dans `resources/[qb]/` et ne sont **jamais** modifiées directement : on passe par leurs `config.lua`, ou on crée une ressource séparée qui les surcharge proprement.

## 6. Conventions de code

- **Langue :** tout texte visible par le joueur est en français, centralisé dans des fichiers de locales — jamais en dur dans la logique.
- **Config-driven :** chaque ressource expose un `config.lua` (coordonnées, prix, timings, items) pour que tout soit ajustable sans toucher au code.
- **Performances :** viser 0.00 ms au repos ; pas de boucle `Citizen.Wait(0)` permanente — utiliser des zones (PolyZone/ox_lib), des events et des distances checks espacés.
- **Sécurité :** toute logique sensible (argent, items, franchissement de frontière) se valide **côté serveur** ; ne jamais faire confiance à un event client ; ajouter des garde-fous anti-trigger.
- **Git :** une branche par fonctionnalité, commits en français (`feat: poste-frontière de Concordia`, `fix: ...`). Un `.gitignore` doit exclure `server.cfg`, les clés de licence, les identifiants base de données et les dumps SQL.

## 7. Roadmap par priorités

**P1 — Fondations (à faire en premier)**
1. `meridia-zones` : définition des trois territoires (Solvera / Kaldera / Cicatrice) avec PolyZones, affichage du territoire courant, règles par zone (la Cicatrice est une zone à risque).
2. `meridia-citizenship` : à la création du personnage, choix de la citoyenneté (Solvera, Kaldera, ou apatride) stocké en metadata joueur ; spawn correspondant (San Verano / Kalvik / Valdero).
3. Renommage de la map : blips personnalisés avec les noms Meridia, suppression/remplacement des blips par défaut.
4. Mise à jour des jobs et gangs dans `qb-core/shared/jobs.lua` et `gangs.lua` selon la table ci-dessus.

**P2 — La frontière**
5. `meridia-border` : Poste de Concordia — file d'attente, contrôle des papiers par la douane (joueurs FAS/APK ou PNJ en heures creuses), fouille de véhicule, amendes/arrestation, franchissement illégal détecté = alerte.
6. Items d'identité : `passeport_solvera`, `passeport_kaldera`, `visa_sud`, `visa_nord`, `papiers_falsifies` (avec chance de détection).

**P3 — Économie de guerre froide**
7. Double monnaie : le Sol reste la monnaie QBCore par défaut au Sud ; le Kard existe côté Nord (item-monnaie ou compte séparé — proposer la meilleure implémentation), avec bureau de change officiel à Concordia et taux au noir fluctuant à Valdero.
8. `meridia-contrebande` : missions de passage (médicaments vers le Nord, Azurium vers le Sud) via tunnels et bateaux de nuit sur la Mer Cendrée.
9. Minage illégal d'Azurium dans la Cicatrice (haut risque, haut rendement).

**P4 — Ambiance et systèmes vivants**
10. Rationnement au Nord (prix/stocks différents), annonces de propagande périodiques, couvre-feu RP à Kalvik.
11. Variable serveur de « tension » (0–100) alimentée par les incidents frontaliers, déclenchant des événements (fermeture de Concordia, patrouilles renforcées, événements MICA).

## 8. Méthode de travail attendue

- Commencer chaque grosse tâche par un **plan** court et le faire valider avant d'écrire du code.
- Poser une question quand un choix de design manque plutôt que de supposer.
- Travailler par petites étapes : une ressource à la fois, testable immédiatement.
- Après chaque fonctionnalité, fournir la **procédure de test en jeu** (commande, `restart <ressource>`, ce qu'on doit voir).

## 9. Interdits

- Ne jamais lire, afficher, demander ou committer : `sv_licenseKey`, identifiants MySQL, tokens, webhooks Discord.
- Ne jamais exécuter de modification destructive en base sans backup préalable.
- N'installer que des ressources open source officielles (GitHub) — jamais de scripts payants « leakés ».
