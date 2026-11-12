# Rendu — Atelier Kubernetes

> Complétez ce fichier au fil de l'atelier, puis faites un commit et un push (voir la section « Rendu » du README).

**Nom / Prénom :** Blanchard Amandine
**Groupe :** ING2-APP-RS2
---

## Exercice 1 — Cluster

1. Combien de nœuds affiche `kubectl get nodes` ? Quel est le rôle de chacun ?
   > Il affiche 3 nœuds dont 2 nœuds sans rôle qui vont servir à exécuter les pods tandis que k3d-lab-server-0 a le rôle de control-plane. Il va servir à gérer le cluster.
2. Que montre `docker ps` ? Qu'est-ce qu'un « nœud » dans k3d, concrètement ?
   > docker ps permet d'afficher tous les conteneurs Docker. Concrètement dans k3d chaque nœud est un conteneur Docker qui fait tourner k3s.

## Exercice 2 — Premier déploiement

3. Quels objets Kubernetes ont été créés par `kubectl create deployment` ? (indice : `kubectl get all`)
   > Les objets Kubernetes qui ont été créés sont un Deployment qui crée lui-même un ReplicaSet qui crée lui-même un Pod.
4. Vous avez supprimé le pod `web-...` : que s'est-il passé, et pourquoi ?
   > L'ancien pod est passé en mode Terminating mais un nouveau pod apparaît aussitôt avec un autre nom. Le ReplicaSet voit qu'il manque un pod, donc il en recrée un.

## Exercice 3 — Modifier un conteneur en direct

5. Après suppression du pod, votre message « Bienvenue... » a-t-il survécu ? Pourquoi ?
   > Non, le message « Bienvenue » n'a pas survécu c'est la page par défaut du serveur Nginx qui est revenue car la modification a été effectuée dans le système de fichiers du conteneur donc elle est éphémère.
6. Pourquoi le `port-forward` a-t-il cessé de fonctionner après la suppression du pod ?
   > Quand le pod est supprimé la connexion se perd et le tunnel ne bascule pas sur le nouveau pod.

## Exercice 4 — Déclaratif

7. Quelle différence entre `kubectl create` et `kubectl apply` ?
   > create : crée uniquement si l'objet n'existe pas tandis que apply va mettre à jour en fonction de ce que contiennent les fichiers .yaml.
8. À quoi sert le `selector` d'un Service ? Que se passe-t-il s'il ne correspond à aucun pod ?
   > Il indique au Service vers quels pods envoyer le trafic grâce à leurs labels. S'il ne correspond à aucun pod, les requêtes vont échouer.

## Exercice 5 — Scaling & mises à jour

9. Sur quels nœuds se trouvent vos 3 réplicas ? Collez la sortie de `kubectl get pods -l app=site -o wide`.

   > Les 3 réplicas sont répartis sur les 3 nœuds. C'est le scheduler qui répartit les pods pour équilibrer la charge dans k3s, le control plane peut aussi exécuter des pods.

10. Avec l'image `nginx:9.99`, quel statut avait le pod ? Quelle commande vous a donné la cause ?
    >Le nouveau pod alternait entre ErrImagePull et ImagePullBackOff sans jamais passer Running. La commande
    > kubectl describe pod -l app=sit montre la cause dans les Events.
11. Pendant la panne, le site restait-il accessible ? Pourquoi ?
    > Oui car le rolling update ne supprime un ancien pod que quand le nouveau est Running. Le pod en 9.99 n'étant jamais prêt
    > les 3 pods en 1.28 sont restés en place et ont continué à servir le site.

## Exercice 6 — Portainer & Mario

12. URL publique de votre Mario :
    > https://redesigned-adventure-x9v46x69j9xf9g6w-8585.app.github.dev/
13. Retrouvez le Deployment `mario` en ligne de commande. Portainer et `kubectl` parlent-ils au même composant ? Lequel ?
   
    > Le Deployment est identique à ceux créés en ligne de commande. Il a seulement en plus des labels que Portainer ajoute Portainer et kubectl parlent tous les deux à l'API Server du control plane.


## Bonus (facultatif)

14. Bonus réalisés et remarques :
    > Bonus A : j'ai complété ingress.yaml  et je l'ai appliqué. Le site est accessible sur le port 8081 sans port-forward,
    > via Traefik. Après kubectl delete pod -l app=site le site reste accessible : l'Ingress envoie le trafic au Service, qui suit automatiquement les nouveaux pods grâce aux labels, alors que le port-forward était lié à un pod précis.

    > Bonus B : j'ai ajouté une readinessProbe HTTP sur / et des requests/limits.Avec /nope nginx répond 404 donc la probe échoue : le nouveau pod reste en 0/1 Running et ne reçoit pas de trafic.
    > Le rolling update se bloque, mais les anciens pods continuent de servir le site

    > Bonus C :drain a marqué agent-0 en SchedulingDisabled et a expulsé ses pods, que les ReplicaSets ont recréés sur les autres nœuds. Après uncordon, les pods ne reviennent pas tout seuls : le nœud accepte à nouveau de nouveaux pods, mais Kubernetes ne déplace pas des pods qui tournent déjà. 

---

**Score obtenu avec `./scripts/check.sh` :** ... / ...
