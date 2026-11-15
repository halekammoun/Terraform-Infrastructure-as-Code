# Guide complet sur l'Infrastructure as Code

Bienvenue dans ce guide complet consacré à l'Infrastructure as Code avec Terraform.

Ce repository propose une approche progressive et pratique de l'Infrastructure as Code, depuis les principes fondamentaux jusqu'au provisionnement et à la gestion des infrastructures Cloud avec Terraform.

L'objectif est de comprendre non seulement comment utiliser Terraform, mais surtout comment il fonctionne et comment il permet d'automatiser la gestion d'une infrastructure.

---

## 🎯 Objectifs

À la fin de ce guide, vous serez capable de :

- Comprendre les principes de l'Infrastructure as Code (IaC)
- Comprendre le rôle et le fonctionnement de Terraform
- Écrire des fichiers de configuration Terraform avec HCL
- Utiliser le workflow Terraform : init, plan, apply et destroy
- Provisionner des ressources sur AWS avec Terraform
- Comprendre et gérer le Terraform State
- Utiliser les variables, outputs et modules
- Configurer et utiliser différents providers
- Diagnostiquer les erreurs et analyser les logs Terraform
- Gérer les environnements et les backends distants
- Utiliser HCP Terraform
- Préparer la certification *HashiCorp Certified: Terraform Associate*

---

## 📚 Prérequis

Ce guide suppose des connaissances de base en :

- Linux / utilisation du terminal
- Administration système
- Réseaux informatiques
- Virtualisation et Cloud Computing
- Notions AWS (recommandé)

---

# 📖 Sommaire
- [Chapitre 01: Introduction à l'Infrastructure as Code et premier déploiement](01-introduction-et-premier-deploiement.md) — IaC, fondamentaux Terraform, providers, State et premières commandes. `Séance 1`
- [Chapitre 02: Workflow Terraform et gestion des ressources](02-automatisation-et-gestion-avancee-des-ressources.md) — workflow `init`, `validate`, `fmt`, `plan`, `apply`, `destroy`, ressources, références et dépendances. `Séance 2` >> **mock exams**
- [Chapitre 03 Variables et modules Terraform](03-variables-et-modules-terraform.md) — variables, outputs, types, expressions et modules. `Séance 3`
- [Chapitre 04 Debug, logging et providers Terraform](04-debug-logging-et-providers-terraform.md) — providers, versions, lock file et journalisation détaillée. `Séance 4` >> **mock exams**
- [Chapitre 05 HCP Terraform et langage Terraform](05-hcp-terraform-et-langage-terraform.md) — expressions, fonctions et fonctionnalités HCP Terraform. `Séance 5`
- [Chapitre 06 Gestion avancée des infrastructures](06-gestion-avancee-des-infrastructures.md) — State, backends, drift, import, workspaces et maintenance. `Séance 6` >> **mock exams**
- Examen TP `Séance 7`

---

## 🧰 Environnement de travail

- Terraform CLI
- Visual Studio Code
- Système Linux
- Compte AWS avec les permissions nécessaires
- Git et GitHub

> **Remarque :** les déploiements AWS dépendent des permissions et des quotas du compte académique. Vérifiez les ressources autorisées et les éventuels coûts avant chaque TP.

---

## 📚 Références

- [Terraform Registry Doc](https://registry.terraform.io/namespaces/hashicorp)
- [Terraform Associate (004) — Certification et objectifs officiels](https://docs.hashicorp.com/certifications/terraform-associate)
- [Terraform Associate (004) — Exam Content List](https://docs.hashicorp.com/terraform/tutorials/certification-004/associate-review-004)
- [Terraform Associate (004) — Learning Path](https://developer.hashicorp.com/terraform/tutorials/certification-004/associate-study-004)

