Parfait 👍
Voici la documentation complète en Markdown, optimisée pour Confluence, prête à être copiée/collée telle quelle.
J’ai utilisé :

des titres compatibles Confluence

des tableaux Markdown propres

une structuration “page de référence projet”



---

📘 Modèle de données – Application Octa

1. Présentation générale

Le modèle de données de l’application Octa est conçu pour gérer la facturation assurantielle, depuis la génération des factures jusqu’au stockage des fichiers, en passant par le détail financier ligne par ligne.

Il est structuré autour de 4 grands domaines :

Facturation

Assureurs

Utilisateurs & Sécurité

Documents et traçabilité



---

2. Domaine Facturation

🧾 Table invoice

Description

La table invoice représente une facture métier générée par l’application Octa pour un assureur donné.

Elle constitue l’entité centrale du modèle.

Structure

Colonne	Type	Description

id	bigint	Identifiant technique
invoice_version	integer	Version de la facture
invoice_number	varchar(255)	Numéro de facture métier
issuer_key	varchar(255)	Clé émetteur
insurer_name	varchar(255)	Nom de l’assureur
invoice_date	varchar(255)	Date de facturation
product_type	varchar(255)	Type de produit
processed	boolean	Facture traitée
processing_success	boolean	Succès du traitement


Relations

1 → N avec invoice_line

1 → 1 / N avec invoice_file

N → 1 avec insurer


Règles métier

Une facture doit contenir au moins une ligne

Le traitement peut être partiel ou en échec

La version permet la réémission de factures



---

📄 Table invoice_line

Description

La table invoice_line contient le détail financier et métier d’une facture.

Chaque ligne correspond à un regroupement contractuel et financier.

Structure – Informations métier

Colonne	Description

id	Identifiant technique
invoice_id	Référence vers la facture
financial_company	Société financière
group_contract_number	Numéro de contrat groupe
sub_convention_type	Type de sous-convention
endorsement_number	Numéro d’avenant
mandate	Mandat
insurance_option_or_risk	Option ou risque
product_category	Catégorie produit
product_code	Code produit


Structure – Données financières

Colonne	Description

total_premium_including_tax	Prime TTC
total_premium_excluding_tax	Prime HT
total_vat	TVA
number_of_files	Nombre de dossiers
unit_commission_per_file	Commission unitaire
management_commission_rate	Taux commission gestion
brokerage_commission_rate	Taux commission courtage
broker_fee	Frais de courtage
company_due	Montant dû à la compagnie
broker_due	Montant dû au courtier


Structure – Sinistres

Colonne	Description

settled_claims_death	Sinistres décès
settled_claims_disability	Sinistres invalidité
settled_claims_unemployment	Sinistres chômage
settled_claims_others	Autres sinistres
total_claims	Total des sinistres


Relations

N → 1 avec invoice


Règles métier

Les montants sont agrégés au niveau facture

Les sinistres sont historisés par ligne

Une ligne est toujours rattachée à une facture



---

📁 Table invoice_file

Description

La table invoice_file représente le fichier physique associé à une facture (PDF, Excel, etc.).

Elle permet la traçabilité documentaire.

Structure

Colonne	Description

id	Identifiant
invoice_id	Référence facture
invoice_version	Version
invoice_number	Numéro facture
issuer_key	Émetteur
insurer_name	Assureur
invoice_date	Date facture
product_type	Type produit
invoice_file_name	Nom du fichier
invoice_file_path	Chemin de stockage
created_date	Date de création
last_modified_date	Dernière modification


Relations

1 → 1 / N avec invoice


Règles métier

Les fichiers sont versionnés

Le stockage peut être externe (S3, COS, etc.)



---

3. Domaine Assureurs

🏦 Table insurer

Description

La table insurer stocke les compagnies d’assurance partenaires.

Structure

Colonne	Description

id	Identifiant
full_name	Nom complet
legal_form	Forme juridique
address	Adresse
siren_number	Numéro SIREN
vat_number	Numéro TVA
starting_date	Début relation
ending_date	Fin relation


Relations

1 → N avec invoice


Règles métier

Un assureur peut avoir plusieurs factures

Les dates permettent la gestion du cycle de vie



---

4. Domaine Utilisateurs & Sécurité

👤 Table octa_user

Description

La table octa_user représente les utilisateurs applicatifs.

Structure

Colonne	Description

id	Identifiant
first_name	Prénom
last_name	Nom
email	Email
user_id	Identifiant technique
created_date	Date création
last_modified_date	Date modification


Relations

N ↔ N avec authority



---

🔐 Table authority

Description

La table authority définit les rôles applicatifs.

Structure

Colonne	Description

id	Identifiant
name	Nom du rôle



---

🔗 Table user_authority

Description

Table de jointure entre utilisateurs et rôles.

Structure

Colonne	Description

user_id	Référence utilisateur
authority_name	Référence rôle



---

5. Synthèse des relations

Table source	Cardinalité	Table cible

invoice	1 → N	invoice_line
invoice	1 → 1 / N	invoice_file
insurer	1 → N	invoice
octa_user	N ↔ N	authority



---

6. Bonnes pratiques

Centraliser les calculs financiers

Vérifier la cohérence facture / lignes

Versionner les documents

Séparer données métier et sécurité

Prévoir l’audit des traitements



---

7. Évolutions possibles

Historisation des statuts de facture

Table de paiements

Audit fonctionnel détaillé

Normalisation des produits et garanties



---

✅ Prochaines améliorations possibles

Si tu veux, je peux :

découper ça en 1 page Confluence par table

générer un diagramme Mermaid

aligner la doc avec tes entités JPA exactes

produire une version orientée métier (PO / Auditeur)

