# 04 — Providers, versions et journalisation Terraform

## Objectifs de certification

Objectifs **2a–2c** (installation, versionnement et utilisation des providers, configuration de plusieurs providers) et **7c** (journalisation détaillée) de Terraform Associate 004.

## 4.1 Rôle d'un provider

Un provider est un plugin qui permet à Terraform d'interagir avec une plateforme ou une API. Il expose des ressources et des sources de données utilisables dans les configurations.

Exemples de plateformes : AWS, Azure, Google Cloud et VMware. L'examen porte sur le fonctionnement général des providers ; la maîtrise détaillée des services propres à chaque plateforme n'est pas nécessaire.

## 4.2 Déclarer les exigences de providers

```hcl
terraform {
  required_version = ">= 1.12.0"

  required_providers {
    local = {
      source  = "hashicorp/local"
      version = "~> 2.5"
    }
  }
}
```

- `source` identifie le provider, généralement sous la forme `namespace/type`.
- `version` définit la contrainte de version.
- `required_version` définit les versions de Terraform CLI acceptées.

Opérateurs à reconnaître : `=`, `!=`, `>`, `>=`, `<`, `<=`, `~>`.

## 4.3 Configurer un provider

```hcl
provider "local" {}
```

Certains providers nécessitent des paramètres tels qu'une région ou un projet. Les identifiants ne doivent pas être codés en dur dans les fichiers versionnés. Utilisez les mécanismes d'authentification recommandés par la plateforme.

## 4.4 Plusieurs providers et alias

Une configuration peut utiliser plusieurs providers ou plusieurs configurations du même provider.

```hcl
provider "aws" {
  region = "us-east-1"
}

provider "aws" {
  alias  = "secondary"
  region = "eu-west-1"
}
```

Une ressource peut sélectionner la configuration aliasée :

```hcl
resource "aws_instance" "example" {
  provider = aws.secondary
  # autres arguments de la ressource
}
```

L'alias distingue plusieurs configurations d'un même provider.

## 4.5 Fichier de verrouillage des providers

`.terraform.lock.hcl` enregistre les versions sélectionnées et les checksums des providers. Il aide à retrouver des dépendances cohérentes lors des initialisations ultérieures.

```bash
terraform init
terraform init -upgrade
terraform providers
```

- `terraform init` installe les versions compatibles.
- `terraform init -upgrade` recherche des versions plus récentes qui respectent les contraintes.
- `terraform providers` affiche les providers requis.

Le lock file doit généralement être conservé dans le contrôle de version du projet.

## 4.6 Vérifier une erreur de provider

En cas d'erreur, vérifier :
1. `required_providers` et la source du provider.
2. Les contraintes de version et la version de Terraform CLI.
3. L'exécution de `terraform init`.
4. Le bloc `provider` et ses paramètres.
5. Les permissions et l'authentification.
6. Les messages affichés pendant `plan` ou `apply`.

Les exemples détaillés propres à AWS, Azure, GCP, VMware ou Docker ne sont pas des objectifs autonomes de l'examen Associate 004.

## 4.7 Journalisation détaillée

Terraform peut produire des journaux de diagnostic.

Niveaux courants de `TF_LOG` : `TRACE`, `DEBUG`, `INFO`, `WARN`, `ERROR`.

```bash
export TF_LOG=TRACE
terraform plan
```

Pour écrire les journaux dans un fichier :

```bash
export TF_LOG=DEBUG
export TF_LOG_PATH=terraform.log
terraform plan
```

Désactiver la journalisation :

```bash
unset TF_LOG
unset TF_LOG_PATH
```

Les journaux peuvent contenir des informations sensibles. Examinez-les et masquez les données confidentielles avant de les partager.

## 4.8 À retenir pour l'examen

- Un provider est un plugin qui expose des ressources et des data sources.
- Les exigences de providers sont déclarées dans `required_providers`.
- Savoir lire les contraintes de version.
- Le bloc `provider` configure une instance du provider.
- Plusieurs configurations peuvent être distinguées avec `alias`.
- `.terraform.lock.hcl` conserve les versions sélectionnées et checksums.
- `TF_LOG` et `TF_LOG_PATH` servent au diagnostic détaillé.

## 4.9 Quiz

1. Quel est le rôle d'un provider ?
2. Où déclare-t-on la source et la version d'un provider ?
3. À quoi sert `.terraform.lock.hcl` ?
4. Quelle commande recherche des versions plus récentes compatibles ?
5. Quelle variable active les journaux Terraform ?

### Correction

1. Permettre à Terraform d'interagir avec une plateforme/API et exposer des ressources/data sources.
2. Dans `required_providers`, à l'intérieur du bloc `terraform`.
3. Enregistrer les versions sélectionnées et les checksums des providers.
4. `terraform init -upgrade`.
5. `TF_LOG`.
