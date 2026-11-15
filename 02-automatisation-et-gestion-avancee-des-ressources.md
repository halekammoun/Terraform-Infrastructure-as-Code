# 02 — Workflow Terraform et gestion des ressources

## Objectifs de certification

Ce chapitre couvre les objectifs **3 — Core Terraform workflow**, **2d** et **4a–4b** de Terraform Associate 004.

## 2.1 Workflow fondamental

Le workflow Terraform suit trois étapes :
1. **Write** : écrire ou modifier les fichiers `.tf`.
2. **Plan** : initialiser, valider et examiner les changements proposés.
3. **Apply** : appliquer les changements après vérification.

Terraform compare la configuration, le State et l'infrastructure réelle pour déterminer les actions nécessaires.

## 2.2 Initialiser — `terraform init`

```bash
terraform init
terraform init -upgrade
terraform init -reconfigure
```

`init` prépare le répertoire, initialise le backend, télécharge les providers requis et initialise les modules. Il crée ou met à jour `.terraform.lock.hcl`, qui enregistre les versions sélectionnées et les checksums des providers.

`-upgrade` recherche des versions admissibles plus récentes. `-reconfigure` réinitialise la configuration du backend sans migrer l'état.

## 2.3 Valider — `terraform validate`

```bash
terraform validate
```

Vérifie la cohérence syntaxique et interne de la configuration. Ne garantit pas que les ressources pourront être créées sur la plateforme cible et ne remplace pas `terraform plan`.

## 2.4 Formater — `terraform fmt`

```bash
terraform fmt
terraform fmt -recursive
terraform fmt -check
```

Applique le formatage HCL canonique. `-recursive` traite les sous-répertoires ; `-check` vérifie sans modifier.

## 2.5 Générer et lire un plan — `terraform plan`

```bash
terraform plan
terraform plan -out=tfplan
terraform apply tfplan
```

Le plan présente les opérations prévues :

| Symbole | Signification |
|---|---|
| `+` | Création |
| `-` | Suppression |
| `~` | Modification en place |
| `-/+` | Destruction puis recréation |
| `+/-` | Création avant destruction, si possible |

`(known after apply)` signifie que la valeur ne sera connue qu'après l'application. Examiner le plan avant l'application est essentiel.

## 2.6 Appliquer — `terraform apply`

```bash
terraform apply
terraform apply -auto-approve
```

Terraform présente normalement un plan et demande une confirmation. Après l'application, le State est mis à jour. `-auto-approve` supprime la confirmation interactive et doit être utilisé avec prudence.

## 2.7 Différencier `resource` et `data`

Un bloc `resource` déclare un objet que Terraform doit gérer :

```hcl
resource "local_file" "example" {
  filename = "example.txt"
  content  = "Terraform Associate 004"
}
```

Un bloc `data` lit des informations existantes sans déclarer leur création ou leur gestion :

```hcl
data "local_file" "existing" {
  filename = "example.txt"
}
```

À retenir : `resource` gère ; `data` consulte.

## 2.8 Références et dépendances

Une référence à un attribut de ressource crée généralement une dépendance implicite. Terraform construit un graphe de dépendances pour déterminer l'ordre d'exécution.

```hcl
resource "local_file" "source" {
  filename = "source.txt"
  content  = "contenu"
}

resource "local_file" "copy" {
  filename = "copy.txt"
  content  = local_file.source.content
}
```

`local_file.copy` dépend de `local_file.source`. La dépendance explicite `depends_on` est présentée au chapitre 05.

## 2.9 Détruire — `terraform destroy`

```bash
terraform destroy
```

Propose la suppression des ressources gérées par Terraform et enregistrées dans le State. Vérifiez toujours les actions proposées avant de confirmer.

## 2.10 À retenir pour l'examen

- `init` prépare le répertoire et installe les providers/modules.
- `validate` vérifie la configuration ; `plan` prévisualise les changements.
- `apply` exécute ; `destroy` supprime les ressources gérées.
- `fmt` applique le formatage HCL.
- `resource` gère une ressource ; `data` lit des informations existantes.
- Les références entre ressources créent des dépendances implicites.

## 2.11 Quiz

1. Quelle commande initialise le répertoire ?
2. Quelle différence entre `validate` et `plan` ?
3. Que signifie `-/+` ?
4. Quelle différence entre `resource` et `data` ?
5. Comment appliquer un plan sauvegardé ?

### Correction

1. `terraform init`.
2. `validate` vérifie la cohérence ; `plan` calcule les changements prévus.
3. Destruction puis recréation.
4. `resource` gère un objet ; `data` lit des informations existantes.
5. `terraform apply <fichier-du-plan>`.
