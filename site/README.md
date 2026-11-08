# Site vitrine — Protocole Égide

Fichier unique : `index.html` (Tailwind CSS + Lucide via CDN, JavaScript natif). Ouvrir dans un navigateur ou héberger tel quel (GitHub Pages, Netlify, Vercel…).

## Configuration

En haut du `<script>` final, l'objet `CONFIG` :

| Clé | Rôle |
|---|---|
| `discordInvite` | Lien d'invitation Discord (navbar, footer, règlement) |
| `webhookUrl` | URL du Webhook Discord qui reçoit les Dossiers de Transit |
| `cfxCode` | Code CFX du serveur pour afficher les joueurs réels (vide = chiffres de démo) |
| `maxPlayers` | Slots affichés par défaut |
| `crisisStart` | Date du Jour 0 RP (compteur « J+ ») |

## Relier le formulaire à Discord

1. Discord → salon privé staff → Paramètres → Intégrations → Webhooks → Nouveau webhook → Copier l'URL.
2. **Ne pas committer cette URL.** Toute personne qui la voit peut poster dans le salon.
3. Recommandé : la placer dans un petit relais serveur (Cloudflare Worker, fonction Netlify/Vercel) en variable d'environnement, et mettre l'URL du relais dans `webhookUrl`. Le relais transmet le JSON tel quel à Discord.
4. Solution rapide (moins sûre) : coller l'URL du webhook directement dans `webhookUrl` sur la version en ligne uniquement.

Le formulaire inclut déjà : champ piège anti-bot, délai de 10 min entre deux envois, blocage des mentions `@everyone`, champs tronqués aux limites Discord.
