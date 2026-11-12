# Rendu — Atelier Kubernetes

> Complétez ce fichier au fil de l'atelier, puis faites un commit et un push (voir la section « Rendu » du README).

**Nom / Prénom :** Blanchard Amandine
**Groupe :** ING2-APP-RS2
---

## Exercice 1 — Cluster

1. Combien de nœuds affiche `kubectl get nodes` ? Quel est le rôle de chacun ?
   > Il affiche 3 noeuds dont 2 noeuds qui sont sans rôle qui vont servir aux pods tandis que le k3d-lab-server-0 à le rôle de control_plane il va servir à gérer le cluster.
2. Que montre `docker ps` ? Qu'est-ce qu'un « nœud » dans k3d, concrètement ?
   > docker ps permet d'afficher tous les container docker donc k3d concrètement est un container docker qui fait tourner k3s.

## Exercice 2 — Premier déploiement

3. Quels objets Kubernetes ont été créés par `kubectl create deployment` ? (indice : `kubectl get all`)
   > Les objets Kubernetes qui ont créer sont un deployment qui créer lui même un REplicaSet qui lui même crér un pod
4. Vous avez supprimé le pod `web-...` : que s'est-il passé, et pourquoi ?
   > L'ancien Pod est passé en mode Terminiatting mais un nouveau pod apparait aussi tôt avec un autre nom.Replicat Set voit qu'il un pod donc il en recréer un.

## Exercice 3 — Modifier un conteneur en direct

5. Après suppression du pod, votre message « Bienvenue... » a-t-il survécu ? Pourquoi ?
   > Non le message bienvenu n'a pas survécu c'est le message du serveur Nginx qui est revenu car la modififacation a été effectué dans le système du conteneur donc cela est éphémère.
6. Pourquoi le `port-forward` a-t-il cessé de fonctionner après la suppression du pod ?
   > Quand le pod est supprimé la connexion se perd et le tunel ne bascule pas sur le nouveau pod on doit le relancer

## Exercice 4 — Déclaratif

7. Quelle différence entre `kubectl create` et `kubectl apply` ?
   > create : créer uniquement si l'objet n'existe pas. Tandis que apply va mettre à jour en fonction de ce que contiennent les .yaml 
8. À quoi sert le `selector` d'un Service ? Que se passe-t-il s'il ne correspond à aucun pod ?
   > Il indique au Service vers quels pods envoyer le trafic grâce à leurs labels. Si il ne correspond à aucun pod les requêtes vont échouer.

## Exercice 5 — Scaling & mises à jour

9. Sur quels nœuds se trouvent vos 3 réplicas ? Collez la sortie de `kubectl get pods -l app=site -o wide`.

   >Les 3 réplicas sont répartis sur les 3 nœuds. C'est le scheduler qui
   >répartit les pods pour équilibrer la charge dans k3s, le control plane peut aussi exécuter des pods

10. Avec l'image `nginx:9.99`, quel statut avait le pod ? Quelle commande vous a donné la cause ?
    >Le nouveau pod alternait entre ErrImagePull etImagePullBackOff sans jamais passer Running. La commande
    > montre la cause dans les Events car l'image nginx:9.99 n'existe pas sur le registre Docker Hub.
11. Pendant la panne, le site restait-il accessible ? Pourquoi ?
    > Oui car le rolling update ne supprime un ancien pod que quand le nouveau est running. Le pod en 9.99 n'étant jamais prêt
    > les 3 pods en 1.28 sont restés en place et ont continué à servir le site.

## Exercice 6 — Portainer & Mario

12. URL publique de votre Mario :
    > https://redesigned-adventure-x9v46x69j9xf9g6w-8585.app.github.dev/
13. Retrouvez le Deployment `mario` en ligne de commande. Portainer et `kubectl` parlent-ils au même composant ? Lequel ?
   
    > Le deployment est identique à ceux créés en ligne de commande. Il a seulement
    > en plus des labels que Portainer ajoute. Portainer et kubectl parlent tous les deux à l'API Server du control plane : ce sont deux clients de la même API.

## Bonus (facultatif)

14. Bonus réalisés et remarques :
    >

---

**Score obtenu avec `./scripts/check.sh` :** ... / ...
