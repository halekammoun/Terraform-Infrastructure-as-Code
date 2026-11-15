# 07 — Révision finale et préparation à Terraform Associate 004

## 7.1 Objectif du chapitre

Ce chapitre révise les objectifs officiels de **HashiCorp Certified: Terraform Associate (004)**. Il ne s'agit pas d'un projet de déploiement cloud complet : les activités sont centrées sur les connaissances et commandes évaluées.

L'examen teste Terraform **1.12**. Le format officiel est un examen en ligne surveillé d'une durée d'une heure. Les types de questions incluent vrai/faux, choix unique et réponses multiples. Vérifiez les informations d'inscription sur le site HashiCorp avant de réserver.

## 7.2 Checklist des huit domaines

- [ ] **1 — IaC avec Terraform** : définition, avantages, multi-cloud, hybrid cloud et workflows agnostiques des services.
- [ ] **2 — Fondamentaux** : installation et versionnement des providers, rôle des providers, plusieurs providers, State.
- [ ] **3 — Workflow** : `init`, `validate`, `plan`, `apply`, `destroy`, `fmt`.
- [ ] **4 — Configuration** : `resource`, `data`, références, variables, outputs, types complexes, expressions, fonctions, dépendances, conditions personnalisées et données sensibles.
- [ ] **5 — Modules** : sources, portée des variables, utilisation, versions.
- [ ] **6 — State management** : backend local, locking, backend distant, drift.
- [ ] **7 — Maintenance** : import, inspection du State avec la CLI, journalisation détaillée.
- [ ] **8 — HCP Terraform** : création d'infrastructure, collaboration, gouvernance, Projects, Workspaces et intégration.

## 7.3 Exercice de lecture de configuration

Considérez cette configuration :

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

variable "environment" {
  type    = string
  default = "dev"
}

locals {
  filename = "${var.environment}.txt"
}

resource "local_file" "example" {
  filename = local.filename
  content  = upper(var.environment)
}

output "created_file" {
  value = local_file.example.filename
}
```

Questions :
1. Quel provider est requis ?
2. Quel est le type de `environment` ?
3. Quelle valeur est utilisée si aucune valeur n'est fournie ?
4. À quoi sert `locals` ?
5. Quelle relation existe entre l'output et la ressource ?
6. Quelle commande initialise le répertoire ?
7. Quelle commande affiche le plan ?

### Correction

1. `hashicorp/local`.
2. `string`.
3. `dev`.
4. À définir une valeur locale réutilisable.
5. L'output référence un attribut de la ressource ; Terraform connaît donc cette dépendance.
6. `terraform init`.
7. `terraform plan`.

## 7.4 Exercice sur les commandes

| Besoin | Commande |
|---|---|
| Initialiser le répertoire | `terraform init` |
| Vérifier la cohérence de la configuration | `terraform validate` |
| Formater les fichiers | `terraform fmt` |
| Prévisualiser les changements | `terraform plan` |
| Appliquer les changements | `terraform apply` |
| Détruire les ressources gérées | `terraform destroy` |
| Lister les ressources du State | `terraform state list` |
| Afficher une ressource du State | `terraform state show` |
| Afficher le State | `terraform show` |

## 7.5 Questions de synthèse

1. Pourquoi Terraform utilise-t-il un State ?
2. Quelle différence entre un provider et un module ?
3. Quelle différence entre `resource` et `data` ?
4. Pourquoi utiliser `.terraform.lock.hcl` ?
5. Quelle différence entre `sensitive` et `ephemeral` ?
6. Quel est l'intérêt d'un backend distant ?
7. Quelle différence entre un workspace CLI et un workspace HCP Terraform ?
8. À quoi sert Sentinel dans HCP Terraform ?

### Éléments de réponse

1. Le State associe les ressources de la configuration aux objets réels et conserve les informations nécessaires aux opérations Terraform.
2. Un provider est un plugin d'intégration à une plateforme/API ; un module est un ensemble de configurations réutilisables.
3. `resource` gère un objet ; `data` lit des informations existantes.
4. Il conserve les versions sélectionnées des providers et leurs checksums pour des installations cohérentes.
5. `sensitive` masque l'affichage ; `ephemeral` vise à empêcher la persistance dans le State et les plans dans les contextes compatibles.
6. Il facilite le partage du State et la collaboration et peut fournir le verrouillage.
7. Le workspace CLI sépare des States locaux liés à une configuration ; le workspace HCP Terraform est un espace hébergé avec configuration, variables, State et runs.
8. Sentinel permet d'appliquer des politiques de gouvernance aux changements Terraform.

## 7.6 Conseils de préparation

- Étudier chaque sous-objectif officiel et vérifier que vous savez l'expliquer.
- S'entraîner à lire des extraits HCL et des plans Terraform.
- Comprendre les différences entre commandes et notions proches (`validate`/`plan`, `resource`/`data`, `sensitive`/`ephemeral`, `count`/`for_each`).
- Réviser State, backend, locking et drift.
- Ne pas consacrer un temps disproportionné aux détails d'un fournisseur cloud particulier : HashiCorp précise que la connaissance spécifique d'un provider n'est pas nécessaire.
- Réviser HCP Terraform, qui fait partie de la version 004.

## 7.7 Ressources officielles

- [Terraform Associate 004 — Exam Content List](https://docs.hashicorp.com/terraform/tutorials/certification-004/associate-review-004)
- [Terraform Associate 004 — Learning Path](https://developer.hashicorp.com/terraform/tutorials/certification-004/associate-study-004)
- [Terraform Associate — Informations sur la certification](https://docs.hashicorp.com/certifications/terraform-associate)
- [Terraform Associate 004 — Sample Questions](https://developer.hashicorp.com/terraform/tutorials/certification-004/associate-questions-004)

## Conclusion

La préparation doit rester centrée sur les huit domaines officiels de Terraform Associate 004 et sur les comportements de Terraform 1.12. Les projets cloud réels peuvent aider à comprendre les concepts, mais les détails propres à un service cloud ou à un outil externe ne doivent pas remplacer la révision des objectifs de certification.
