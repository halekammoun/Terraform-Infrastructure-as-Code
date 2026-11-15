# 06 — State, backends et maintenance de l'infrastructure

## Objectifs de certification

Objectif **6 — Terraform state management** et objectif **7 — Maintain infrastructure with Terraform** de Terraform Associate 004.

## 6.1 Comprendre le State

Le State associe les objets déclarés dans la configuration aux ressources réelles gérées par Terraform. Il conserve les identifiants et métadonnées nécessaires aux opérations suivantes.

Le fichier local par défaut est généralement `terraform.tfstate`. Il peut contenir des données sensibles : protégez-le et ne le publiez pas dans un dépôt Git.

## 6.2 Backend local

Par défaut, Terraform utilise le backend `local`. Le State est stocké localement dans le répertoire de travail.

Avantages : simplicité pour un usage individuel ou un laboratoire. Limites : partage, collaboration, sauvegarde et protection à organiser.

## 6.3 Backend distant

Un backend distant stocke le State dans un emplacement distant. Il facilite le partage et la collaboration et peut fournir des mécanismes de verrouillage.

La configuration exacte dépend du backend choisi. Ne stockez pas les credentials en clair dans la configuration ; utilisez les mécanismes d’authentification prévus.

Après une modification de backend :

```bash
terraform init
```

Selon le cas, Terraform peut proposer de migrer le State existant. Lisez les messages avant de confirmer.

## 6.4 State locking

Le verrouillage du State empêche plusieurs opérations concurrentes de modifier simultanément le même State lorsque le backend prend en charge cette fonctionnalité.

Il réduit les risques de corruption ou de conflits. Ne désactivez pas le verrouillage pour contourner un problème sans en comprendre la cause.

## 6.5 Resource drift

Le drift correspond à une différence entre l'état réel de l'infrastructure et l'état attendu par Terraform. Il peut apparaître lorsqu'une ressource est modifiée manuellement en dehors de Terraform.

`terraform plan` permet de détecter les différences connues. Le mode refresh-only met à jour le State à partir de l'infrastructure réelle sans appliquer les changements de configuration aux ressources :

```bash
terraform plan -refresh-only
terraform apply -refresh-only
```

Il faut comprendre les conséquences avant d'accepter la mise à jour du State.

## 6.6 Inspecter le State avec la CLI

```bash
terraform show
terraform state list
terraform state show <resource_address>
```

- `terraform show` affiche les informations du State ou d'un plan.
- `terraform state list` liste les adresses des ressources suivies.
- `terraform state show` affiche les attributs d'une ressource suivie.

Toute commande qui modifie le State doit être utilisée avec prudence.

## 6.7 Importer une ressource existante

L'import associe une ressource existante à une adresse Terraform.

Exemple avec un bloc `import` :

```hcl
import {
  to = local_file.example
  id = "example.txt"
}
```

Puis :

```bash
terraform plan
terraform apply
```

La commande CLI historique existe également :

```bash
terraform import local_file.example example.txt
```

L'identifiant dépend du provider et du type de ressource. L'import ne génère pas nécessairement toute la configuration : vérifiez et complétez le bloc `resource`.

## 6.8 À retenir pour l'examen

- Le State associe la configuration aux ressources réelles.
- Le backend `local` est le backend par défaut.
- Un backend distant facilite le partage et peut offrir le locking.
- Le State locking protège contre les modifications concurrentes.
- Le drift est une différence entre l'infrastructure réelle et l'état attendu.
- Le mode refresh-only met à jour le State à partir de l'infrastructure réelle.
- `terraform state list`, `terraform state show` et `terraform show` inspectent le State.
- L'import associe une ressource existante à une adresse Terraform ; la configuration doit être vérifiée.

## 6.9 Quiz

1. Quel backend est utilisé par défaut ?
2. Quel est le rôle du State locking ?
3. Qu'appelle-t-on resource drift ?
4. Quelle commande liste les ressources suivies dans le State ?
5. L'import génère-t-il nécessairement toute la configuration Terraform ?

### Correction

1. Le backend `local`.
2. Empêcher les modifications concurrentes du même State lorsque le backend prend en charge le verrouillage.
3. Une différence entre l'infrastructure réelle et l'état attendu/connu par Terraform.
4. `terraform state list`.
5. Non. Il faut vérifier et compléter la configuration de la ressource.
