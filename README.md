# Botnet C&C Simple

## 1. Introduction à un botnet
Un **botnet** est un réseau d'ordinateurs capables de recevoir des commandes à distance et d’exécuter des actions localement. Dans ce cas, l’objectif est de créer une structure de type C&C (*Command and Control*), où un serveur maître envoie des commandes aux nœuds esclaves, qui les exécutent et renvoient les résultats. Ce type de botnet est souvent utilisé dans des attaques DDoS ou pour déployer des logiciels malveillants à grande échelle.

## 2. Mise en place du serveur maître
Pour le serveur maître, nous utilisons **Netcat**, un outil open-source permettant de créer facilement des connexions TCP pour envoyer et recevoir des paquets texte. Le serveur maître, déjà précompilé en binaire sous le nom de `master`, ne nécessite aucune modification.

## 3. Création des nœuds esclaves
L'implémentation du nœud esclave est plus complexe, car il doit pouvoir :

- Recevoir des commandes du maître.
- Exécuter ces commandes localement.
- Retourner les résultats au maître.

### Étapes importantes pour la mise en place des esclaves :

### a. Initialisation du nœud esclave
Dans le fichier `bot.c`, l’esclave commence par récupérer le nom d’utilisateur du système en utilisant la fonction `getenv("USER")` pour s’identifier auprès du maître. Ensuite, il tente de se connecter au maître en utilisant l'adresse IP locale `127.0.0.1` et le port `9999`, spécifié par le maître.

La fonction `init_socket()` (qui doit être implémentée dans `connect.c`) est utilisée pour établir cette connexion. Elle se base sur les fonctions réseau en C telles que `inet_addr()` et `socket()` pour convertir l’adresse IP et créer un canal de communication entre l’esclave et le maître.

### b. Écoute des commandes du maître
Une fois connecté, l’esclave entre dans une boucle infinie pour écouter les commandes du maître. Les fonctions `recieve()` et `parse()`, définies dans `utils.c`, sont utilisées pour recevoir et interpréter ces commandes. 

- La fonction `recieve()` lit les messages du maître.
- La fonction `parse()` vérifie que le message est destiné à cet esclave et l'envoie à la fonction `execute()` pour exécution.

### c. Exécution des commandes
La fonction `execute()` exécute les commandes reçues en utilisant `popen()` pour les rediriger vers le terminal de l’esclave. Tout résultat est ensuite renvoyé au maître via la fonction `respond()`.

## 4. Dissimulation du malware
Une astuce pour dissimuler le malware consiste à le déguiser en image. En modifiant le nom du fichier exécutable avec un caractère Unicode (par exemple, `panda⒈png`), le programme ressemble à une image. Cette technique utilise des astuces d'encodage pour tromper l'utilisateur.

## 5. Extensions possibles
Le botnet peut être amélioré de plusieurs manières :

- **Persistance** : Transformer l’esclave en un processus *daemon* qui se relance automatiquement au démarrage de l’ordinateur infecté.
- **Réseau peer-to-peer** : Réorganiser l'architecture en un réseau P2P pour répartir les droits d’administration entre plusieurs nœuds.
- **Mise en œuvre de protocoles réseau sécurisés** : Utiliser des protocoles comme IRC pour renforcer la sécurité des communications.

## Conclusion
Ce guide montre comment créer un botnet simple de type C&C. L'utilisation des sockets et des fonctions réseau en C est au cœur de l'implémentation. Les extensions proposées offrent des pistes pour rendre le botnet plus sophistiqué et persistant. Ce projet, bien que simple, met en évidence les concepts clés de la création d’un botnet tout en avertissant contre une utilisation malveillante de ces connaissances.
