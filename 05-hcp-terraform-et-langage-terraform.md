# 05 — Configuration Terraform et HCP Terraform

## Objectifs de certification

Objectifs **4e–4h** (expressions, fonctions, dépendances, conditions personnalisées et données sensibles) et **8** (HCP Terraform) de Terraform Associate 004.

## 5.1 Expressions et références

Les expressions calculent des valeurs et construisent une configuration dynamique.

```hcl
var.region
local.environment
aws_instance.web.id
module.network.subnet_id
```

Une référence à un attribut de ressource peut créer une dépendance implicite.

## 5.2 Fonctions intégrées

Terraform fournit des fonctions intégrées pour manipuler les valeurs. Il n'est pas possible de définir ses propres fonctions dans le langage Terraform.

```hcl
lower("PRODUCTION")
upper("terraform")
length(["dev", "test", "prod"])
join(",", ["a", "b", "c"])
split(",", "a,b,c")
lookup({dev = "small", prod = "large"}, "prod", "medium")
```

`terraform console` permet d'évaluer des expressions :

```bash
terraform console
```

## 5.3 Expressions conditionnelles, `for` et splat

```hcl
locals {
  instance_type = var.environment == "prod" ? "large" : "small"
  upper_names   = [for name in ["web", "api"] : upper(name)]
}
```

Expression splat :

```hcl
aws_instance.web[*].id
```

Elle récupère un attribut pour chaque élément d'une collection de ressources.

## 5.4 Meta-arguments `count` et `for_each`

`count` crée plusieurs instances indexées :

```hcl
resource "local_file" "example" {
  count    = 2
  filename = "file-${count.index}.txt"
  content  = "example"
}
```

`for_each` crée des instances identifiées par une clé :

```hcl
resource "local_file" "example" {
  for_each = toset(["dev", "prod"])
  filename = "${each.key}.txt"
  content  = each.key
}
```

`count` utilise un index numérique ; `for_each` utilise une clé ou valeur de collection. Le choix dépend de la structure et de la stabilité des éléments.

## 5.5 Dépendances : `depends_on`

Terraform déduit normalement les dépendances à partir des références. `depends_on` déclare une dépendance explicite lorsque la relation n'est pas visible dans les expressions.

```hcl
resource "local_file" "first" {
  filename = "first.txt"
  content  = "first"
}

resource "local_file" "second" {
  filename   = "second.txt"
  content    = "second"
  depends_on = [local_file.first]
}
```

Évitez les dépendances explicites inutiles : les références directes sont généralement préférables.

## 5.6 Cycle de vie des ressources

Le bloc `lifecycle` contrôle certains aspects du cycle de vie d'une ressource.

```hcl
lifecycle {
  create_before_destroy = true
}
```

- `create_before_destroy` : demande de créer le remplacement avant de détruire l'ancienne ressource, si le provider et les contraintes le permettent.

La certification 004 met explicitement l'accent sur `depends_on` et `create_before_destroy`.

## 5.7 Conditions personnalisées et checks

Une précondition valide une hypothèse avant une opération :

```hcl
resource "local_file" "example" {
  filename = "example.txt"
  content  = "example"

  lifecycle {
    precondition {
      condition     = length(var.environment) > 0
      error_message = "environment ne doit pas être vide."
    }
  }
}
```

Une postcondition vérifie une propriété après l'opération :

```hcl
output "file_name" {
  value = local_file.example.filename
  precondition {
    condition     = endswith(local_file.example.filename, ".txt")
    error_message = "Le fichier doit avoir l'extension .txt."
  }
}
```

Un bloc `check` définit des vérifications qui signalent des problèmes sans nécessairement bloquer l'opération comme une précondition ou une postcondition.

## 5.8 Données sensibles, valeurs éphémères et Vault

Marquer une variable `sensitive` masque sa valeur dans certains affichages, mais ne signifie pas qu'elle n'est pas enregistrée dans le State.

```hcl
variable "db_password" {
  type      = string
  sensitive = true
}
```

Les valeurs `ephemeral` sont conçues pour ne pas être conservées dans le State ou les fichiers de plan lorsqu'elles sont utilisées dans des contextes compatibles. Certains providers proposent aussi des arguments write-only qui évitent de stocker une valeur dans le State.

À distinguer :
- `sensitive` : contrôle principalement l'affichage ;
- `ephemeral` : évite la persistance dans le State/plan dans les contextes pris en charge.

HashiCorp Vault peut fournir des secrets à Terraform via le provider Vault. L'examen vise les bonnes pratiques et le rôle de Vault, pas son administration approfondie.

## 5.9 HCP Terraform : rôle et fonctionnalités

HCP Terraform est la plateforme hébergée de HashiCorp pour collaborer autour des configurations et exécutions Terraform. Elle propose notamment :
- stockage et gestion centralisée du State ;
- exécutions Terraform à distance ;
- collaboration entre équipes ;
- gestion des variables et credentials ;
- intégration avec les systèmes de contrôle de version ;
- politiques et gouvernance ;
- organisation avec Projects et Workspaces.

## 5.10 Workspaces et Projects HCP Terraform

Un **workspace HCP Terraform** regroupe une configuration, des variables, un State et l'historique des runs associés à un périmètre de travail.

Un **Project** permet d'organiser plusieurs workspaces dans une structure commune.

Ne pas confondre les workspaces HCP Terraform avec les workspaces CLI locaux : ce sont deux concepts distincts.

## 5.11 Workflows HCP Terraform

- **CLI-driven workflow** : l'utilisateur travaille avec Terraform CLI, connecté à HCP Terraform pour les opérations et/ou le stockage du State.
- **VCS-driven workflow** : un dépôt de contrôle de version est connecté à un workspace ; les changements du dépôt peuvent déclencher des runs.

La plateforme gère les runs, leur historique, les validations et les résultats selon les paramètres du workspace.

## 5.12 Collaboration et gouvernance

Notions à connaître :
- équipes et permissions d'accès ;
- partage et gestion des variables ;
- Private Registry pour partager des modules et providers privés ;
- Sentinel et application de politiques ;
- détection de dérive et gouvernance des changements ;
- Projects pour organiser les workspaces.

Sentinel est un framework de politiques qui permet de contrôler les changements selon les règles définies par l'organisation.

## 5.13 Intégration avec HCP Terraform

La CLI peut être connectée à HCP Terraform avec :

```bash
terraform login
```

Le backend `cloud` ou l'intégration HCP Terraform lie un projet local à des workspaces distants. La migration du State doit suivre la procédure prévue et être effectuée avec précaution.

## 5.14 À retenir pour l'examen

- Lire des expressions, fonctions, conditionnelles, `for` et splat.
- Distinguer `count` et `for_each`.
- Comprendre les dépendances implicites et `depends_on`.
- Connaître `create_before_destroy` et les principaux comportements `lifecycle`.
- Différencier validation, précondition, postcondition et check.
- Distinguer `sensitive` et `ephemeral` ; connaître le rôle de Vault.
- Comprendre les workflows, workspaces, projects, collaboration et gouvernance HCP Terraform.

## 5.15 Quiz

1. Quelle différence entre `count` et `for_each` ?
2. Quand utiliser `depends_on` ?
3. Quel est le rôle de `create_before_destroy` ?
4. Une variable `sensitive` est-elle automatiquement absente du State ?
5. Quel est le rôle d'un workspace HCP Terraform ?
6. Quelle différence entre CLI-driven et VCS-driven ?

### Correction

1. `count` utilise des index numériques ; `for_each` utilise des clés ou valeurs de collection.
2. Pour déclarer une dépendance qui n'est pas déduite d'une référence directe.
3. Créer le remplacement avant de détruire l'ancienne ressource, si possible.
4. Non. `sensitive` masque l'affichage mais ne garantit pas la non-persistance.
5. Regrouper configuration, variables, State et runs d'un périmètre de travail.
6. Le premier est piloté depuis la CLI ; le second est connecté à un dépôt VCS qui peut déclencher les runs.
