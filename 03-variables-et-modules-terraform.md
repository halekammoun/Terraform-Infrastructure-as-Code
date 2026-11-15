# 03 — Variables, outputs et modules Terraform

## Objectifs de certification

Objectif **4c–4d** (variables, outputs et types complexes) et objectif **5** (modules) de Terraform Associate 004.

## 3.1 Variables d'entrée

Les variables rendent une configuration réutilisable.

```hcl
variable "instance_name" {
  description = "Nom de la ressource"
  type        = string
  default     = "web-server"
}

resource "local_file" "example" {
  filename = "${var.instance_name}.txt"
  content  = "Terraform"
}
```

Une variable est référencée avec `var.<nom>`.

## 3.2 Types de données

| Type | Exemple |
|---|---|
| `string` | `"web"` |
| `number` | `3` |
| `bool` | `true` |
| `list(string)` | `["dev", "test"]` |
| `set(string)` | `toset(["dev", "test"])` |
| `map(string)` | `{ env = "dev" }` |
| `object({...})` | Objet avec attributs typés |
| `tuple([...])` | Collection ordonnée de types définis |

Exemple :

```hcl
variable "tags" {
  type = map(string)
  default = {
    Environment = "dev"
    Team        = "platform"
  }
}
```

Il faut reconnaître les types primitifs et complexes, comprendre leurs contraintes et choisir le type adapté.

## 3.3 Fournir des valeurs aux variables

Les valeurs peuvent être définies par `default`, dans un fichier de variables, sur la ligne de commande ou via des variables d'environnement.

```bash
terraform plan -var="instance_name=api"
terraform plan -var-file="dev.tfvars"
export TF_VAR_instance_name="api"
terraform plan
```

Exemple `terraform.tfvars` :

```hcl
instance_name = "api"
```

Les fichiers `*.auto.tfvars` sont également chargés automatiquement. Il faut connaître ces mécanismes et savoir que Terraform applique un ordre de priorité aux différentes sources. Les options explicites de la ligne de commande ont priorité sur les fichiers auto-chargés et les valeurs par défaut.

## 3.4 Outputs

Les outputs exposent des valeurs calculées par la configuration ou par un module.

```hcl
output "file_path" {
  description = "Chemin du fichier créé"
  value       = local_file.example.filename
}
```

```bash
terraform output
terraform output file_path
```

Un output d'un module enfant est référencé depuis le parent avec `module.<nom>.<output>`.

## 3.5 Validation des variables

Un bloc `validation` vérifie une valeur d'entrée :

```hcl
variable "environment" {
  type = string
  validation {
    condition     = contains(["dev", "test", "prod"], var.environment)
    error_message = "La valeur doit être dev, test ou prod."
  }
}
```

Les conditions personnalisées sur les ressources et outputs sont détaillées au chapitre 05.

## 3.6 Définition et intérêt des modules

Un module est un ensemble de fichiers Terraform regroupés dans un répertoire. Le répertoire courant est le **module racine** ; un module appelé depuis celui-ci est un **module enfant**.

Les modules permettent de réutiliser, organiser et composer des configurations.

## 3.7 Module local

Structure :

```text
project/
├── main.tf
└── modules/
    └── file/
        ├── main.tf
        ├── variables.tf
        └── outputs.tf
```

`modules/file/variables.tf` :

```hcl
variable "filename" {
  type = string
}
```

`modules/file/main.tf` :

```hcl
resource "local_file" "this" {
  filename = var.filename
  content  = "Created by a child module"
}
```

`modules/file/outputs.tf` :

```hcl
output "path" {
  value = local_file.this.filename
}
```

Dans le module racine :

```hcl
module "example_file" {
  source   = "./modules/file"
  filename = "module-example.txt"
}

output "created_path" {
  value = module.example_file.path
}
```

Le module enfant ne lit pas directement les variables du parent : les valeurs lui sont transmises comme arguments du bloc `module`.

## 3.8 Sources de modules

Les modules peuvent provenir d'un chemin local, du Terraform Registry ou d'un dépôt Git.

Exemple Registry :

```hcl
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "5.8.1"
}
```

La version est illustrative ; elle doit être compatible avec le module utilisé.

## 3.9 Versionnement et initialisation

L'argument `version` contraint la version d'un module provenant du Registry. Il ne s'utilise pas de la même manière pour les modules locaux.

Après l'ajout ou la modification d'une source de module :

```bash
terraform init
terraform init -upgrade
```

## 3.10 À retenir pour l'examen

- Déclarer une variable avec `variable` et la référencer avec `var.<nom>`.
- Reconnaître les types primitifs et complexes.
- Distinguer variables d'entrée et outputs.
- Comprendre les principales sources de valeurs.
- Distinguer module racine et module enfant.
- Comprendre la portée des variables et outputs entre modules.
- Reconnaître les sources de modules et l'argument `version`.

## 3.11 Quiz

1. Comment référencer une variable `region` ?
2. Quel type convient à une collection de chaînes indexée par clé ?
3. Quel est le rôle d'un output ?
4. Comment référencer l'output `id` du module `network` ?
5. Quel argument indique la source d'un module ?

### Correction

1. `var.region`.
2. `map(string)`.
3. Exposer une valeur calculée ou une information utile.
4. `module.network.id`.
5. `source`.
