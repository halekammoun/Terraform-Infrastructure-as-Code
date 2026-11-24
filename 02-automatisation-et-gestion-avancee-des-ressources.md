
# 02 — Workflow Terraform et gestion des ressources

## Objectifs de certification

Ce chapitre couvre les notions liées aux objectifs suivants de Terraform Associate 004 :

- **3 — Core Terraform Workflow**
- **2d — Understand the purpose of the dependency lock file**
- **4a — Understand Terraform configuration language fundamentals**
- **4b — Use resource blocks and data sources**

À la fin de ce chapitre, vous serez capable de comprendre le workflow Terraform, d'utiliser les principales commandes de la CLI, de lire un plan d'exécution et de gérer les dépendances entre ressources.

---

## 2.1 Comprendre le workflow Terraform

Le workflow fondamental de Terraform repose sur trois grandes étapes :

1. **Write** : écrire ou modifier les fichiers de configuration `.tf`.
2. **Plan** : examiner les changements que Terraform prévoit d'effectuer.
3. **Apply** : appliquer les changements après vérification.

Avant de pouvoir exécuter ce workflow, le répertoire doit être initialisé avec `terraform init`.

Terraform utilise les informations suivantes pour déterminer les actions nécessaires :

- La configuration Terraform.
- Le Terraform State.
- Les informations disponibles sur l'infrastructure distante.

## 2.2 Initialiser un projet — `terraform init`

La commande `terraform init` initialise le répertoire de travail Terraform.

Elle permet notamment de :

- Initialiser le backend configuré.
- Télécharger les providers nécessaires.
- Initialiser les modules utilisés.
- Créer ou mettre à jour le fichier `.terraform.lock.hcl`.

```bash
terraform init
```

### Options importantes

```bash
terraform init -upgrade
terraform init -reconfigure
```

| Option | Rôle |
|---|---|
| `-upgrade` | Recherche des versions admissibles plus récentes des providers et modules. |
| `-reconfigure` | Réinitialise la configuration du backend sans migrer le State existant. |

### Le fichier `.terraform.lock.hcl`

Ce fichier enregistre les sélections de versions des providers ainsi que leurs checksums.

Il permet de conserver des versions cohérentes des providers entre les différentes initialisations du projet.

**À retenir :** le fichier `.terraform.lock.hcl` doit généralement être versionné avec le code source.

## 2.3 Formater la configuration — `terraform fmt`

La commande `terraform fmt` applique le formatage canonique aux fichiers Terraform.

```bash
terraform fmt
```

### Options importantes

```bash
terraform fmt -recursive
terraform fmt -check
```

| Option | Rôle |
|---|---|
| `-recursive` | Formate également les fichiers Terraform présents dans les sous-répertoires. |
| `-check` | Vérifie le formatage sans modifier les fichiers. |

**À retenir :** `terraform fmt` améliore la lisibilité et garantit une présentation cohérente du code HCL.

## 2.4 Valider la configuration — `terraform validate`

La commande `terraform validate` vérifie la cohérence syntaxique et structurelle de la configuration Terraform.

```bash
terraform validate
```

Elle permet notamment de détecter :

- Les erreurs de syntaxe HCL.
- Les arguments ou blocs incorrectement définis.
- Les références invalides dans la configuration.

Cette commande ne garantit pas que les ressources pourront réellement être créées chez le fournisseur Cloud.

Elle ne remplace donc pas `terraform plan`.

**Différence importante :**

- `terraform validate` vérifie la cohérence de la configuration.
- `terraform plan` présente les changements prévus sur l'infrastructure.

## 2.5 Générer et lire un plan — `terraform plan`

La commande `terraform plan` présente les modifications que Terraform prévoit d'effectuer pour atteindre l'état souhaité.

```bash
terraform plan
```

Terraform analyse la configuration, le State et les informations disponibles sur l'infrastructure distante pour déterminer les opérations nécessaires.

### Sauvegarder un plan

Il est possible d'enregistrer un plan dans un fichier :

```bash
terraform plan -out=tfplan
```

Ce plan peut ensuite être appliqué directement :

```bash
terraform apply tfplan
```

### Lire les symboles d'un plan

Lorsqu'on examine un plan Terraform, plusieurs symboles permettent d'identifier les opérations prévues.

| Symbole | Signification |
|---|---|
| `+` | Création d'une ressource |
| `-` | Suppression d'une ressource |
| `~` | Modification en place |
| `-/+` | Destruction puis recréation |
| `+/-` | Création avant destruction, lorsque cela est possible |

La mention `(known after apply)` indique qu'une valeur ne pourra être déterminée qu'après l'application du plan.

**Bonne pratique :** toujours examiner le plan avant d'appliquer des modifications, particulièrement lorsqu'elles concernent des ressources de production.

## 2.6 Appliquer les changements — `terraform apply`

La commande `terraform apply` exécute les changements nécessaires pour atteindre l'état souhaité.

```bash
terraform apply
```

Par défaut, Terraform présente un plan et demande une confirmation avant d'appliquer les modifications.

### Option `-auto-approve`

```bash
terraform apply -auto-approve
```

Cette option permet d'appliquer les changements sans demander de confirmation interactive.

Elle doit être utilisée avec prudence, notamment dans les environnements de production.

Après une application réussie, Terraform met à jour le State pour refléter les changements effectués.

## 2.7 Détruire les ressources — `terraform destroy`

La commande `terraform destroy` propose la suppression des ressources gérées par Terraform dans le contexte de travail concerné.

```bash
terraform destroy
```

Terraform présente normalement les suppressions prévues et demande une confirmation avant leur exécution.

**Attention :** cette commande peut entraîner la suppression réelle de ressources Cloud. Vérifiez toujours le plan et l'environnement ciblé avant de confirmer.

## 2.8 Différencier `resource` et `data`

Terraform utilise principalement deux types de blocs pour interagir avec les objets d'infrastructure : `resource` et `data`.

### 2.8.1 Le bloc `resource`

Un bloc `resource` déclare une ressource que Terraform doit gérer.

Exemple avec le provider `local` :

```hcl
resource "local_file" "example" {
  filename = "example.txt"
  content  = "Terraform Associate 004"
}
```

Terraform est responsable de la création, de la modification et de la suppression de cette ressource.

### 2.8.2 Le bloc `data`

Un bloc `data` permet de lire des informations concernant un objet existant, sans déclarer sa création ou sa gestion.

Exemple :

```hcl
data "local_file" "existing" {
  filename = "example.txt"
}
```

On peut ensuite accéder au contenu du fichier avec :

```hcl
data.local_file.existing.content
```

### Comparaison

| `resource` | `data` |
|---|---|
| Déclare une ressource à gérer. | Lit les informations d'une ressource existante. |
| Peut créer, modifier ou supprimer l'objet. | Ne déclare pas la création ou la gestion de l'objet. |
| Utilisé pour gérer l'infrastructure. | Utilisé pour consulter des informations. |

**À retenir :** `resource` gère un objet ; `data` consulte des informations.

## 2.9 Références et dépendances entre ressources

Terraform utilise les références entre ressources pour déterminer les dépendances et construire un graphe d'exécution.

Lorsqu'une ressource utilise un attribut d'une autre ressource, Terraform crée généralement une **dépendance implicite**.

### Exemple

```hcl
resource "local_file" "source" {
  filename = "source.txt"
  content  = "Contenu source"
}

resource "local_file" "copy" {
  filename = "copy.txt"
  content  = local_file.source.content
}
```

Dans cet exemple :

- `local_file.source` crée le fichier `source.txt`.
- `local_file.copy` utilise le contenu de `local_file.source`.
- Terraform comprend automatiquement que `local_file.copy` dépend de `local_file.source`.

Il construit donc son graphe de dépendances afin de déterminer l'ordre d'exécution approprié.

## 2.10 Dépendance explicite et implicite:

### 2.10.1 Dépendance implicite :

Terraform découvre automatiquement la dépendance grâce à une référence.

```bash
resource "aws_eip" "public_ip" {
  vpc      = true
  instance = aws_instance.web_server.id
}
```

Ici :
aws_instance.web_server.id


La ressource aws_eip.public_ip dépend implicitement de aws_instance.web_server.

### 2.10.2 Dépendance explicite :

On déclare directement la dépendance avec depends_on.

```bash
resource "aws_instance" "web_server" {
  ami           = "ami-123456"
  instance_type = "t2.micro"

  depends_on = [
    aws_s3_bucket.company_data
  ]
}
```

Ici, Terraform doit respecter la dépendance déclarée envers le bucket S3.

À retenir
 Une référence à un attribut d'une autre ressource crée généralement une dépendance implicite. depends_on permet de déclarer explicitement une dépendance.

## 2.11 À retenir pour l'examen

- `terraform init` initialise le projet, le backend, les providers et les modules.
- `.terraform.lock.hcl` conserve les sélections de versions et les checksums des providers.
- `terraform fmt` applique le formatage canonique HCL.
- `terraform validate` vérifie la cohérence de la configuration.
- `terraform plan` prévisualise les changements.
- `terraform apply` exécute les changements.
- `terraform destroy` propose la suppression des ressources gérées.
- `resource` déclare un objet à gérer ; `data` lit des informations existantes.
- Les références entre ressources créent généralement des dépendances implicites.
- Un plan sauvegardé avec `-out` peut être appliqué avec `terraform apply`.

## 2.12 Quiz de révision

1. Quelle commande permet d'initialiser un répertoire Terraform ?
2. Quelle est la différence entre `terraform validate` et `terraform plan` ?
3. Quel est le rôle du fichier `.terraform.lock.hcl` ?
4. Que signifie le symbole `-/+` dans un plan Terraform ?
5. Quelle est la différence entre un bloc `resource` et un bloc `data` ?
6. Quelle option permet de sauvegarder un plan dans un fichier ?
7. Quelle commande permet d'appliquer un plan sauvegardé ?
8. Pourquoi Terraform peut-il déterminer automatiquement l'ordre de création de deux ressources ?

### Correction

1. `terraform init`.
2. `validate` vérifie la cohérence de la configuration, tandis que `plan` présente les changements prévus.
3. Il enregistre les versions sélectionnées et les checksums des providers.
4. Destruction de la ressource suivie de sa recréation.
5. `resource` déclare un objet à gérer ; `data` permet de lire des informations existantes.
6. `terraform plan -out=tfplan`.
7. `terraform apply tfplan`.
8. Grâce aux dépendances implicites créées par les références entre ressources.
