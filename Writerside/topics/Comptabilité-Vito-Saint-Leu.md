# Comptabilité Vito Saint Leu

## Les exercices comptables ouverts
| Début       | Fin         | Statut |
|-------------|-------------|--------|
| 01-10-2020  | 30-09-2021  | ouvert |
| 01-10-2021  | 30-09-2022  | ouvert |
| 01-10-2022  | 30-09-2023  | ouvert |
| 01-10-2023  | 30-09-2024  | ouvert |
| 01-10-2024  | 30-09-2025  | ouvert |

> Créer le répertoire d'archives comptables
 - Le lettrage des comptes 44566 & 4181 est à revoir
Action : Plan comptable, pour ces comptes , les positionner à **Lettrable**
 - Comptabilité -> Lettrage 
 > Le fait de choisir le compte , va permettre de tout delettrer
 - supprimer le compte 5124 message le journal du compte 5124 doit être défini
 - supprimer le compte 512
 - supprimer le compte 5121

## Equilibre des journaux

<code-block lang="sql">
select jnl.oid,jnl.code,jnl.Libellé, E.Date, sum(E.Débit-E.Crédit)
from EcritureComptable E
inner join journal jnl on jnl.oid = E.journal
where e.Date >= '01/10/2020' and Date < '01/10/2021'
group by jnl.oid,jnl.code,jnl.Libellé, e.Date
having sum(E.Débit-E.Crédit) <> 0
</code-block>

> Le journal BQ1 a des écritures qui s'équilibrent sur deux dates 2024-04-17 & 2024-04-19
> Mettre les écritres à la même date, le mieux serait de cibler les écritures concernées

<code-block lang="SQL">
update EcritureComptable
set date = convert(datetime,'2021-04-19',120)
where journal = 'E66E0AFB-7555-45E4-B62F-BE03DFD75584'
and date = convert(datetime,'2021-04-17',120)
</code-block>

> Dans le cas du journal **Opérations diverses** avant une valeur de 90000 , rechercher sur le journal ODA, 
> les écritures qui pourraient représenter une valeur de 90 0000
> Comptabilité-> Liste des écritures 
> - filter sur le 3020-09-2021
> - code journal OD
> - Le débit est supérieur de 90000 , rechercher les écritures dont la somme des débits seraient proches de cette valeur
> - Ce sont des virements internes qui n'ont pas de contrepartie au crédit
> - **ACTION** : Saisie libre
> - Filtrer sur le code journal OD 
> - filtrer sur la date du 30-09-2021 , Afficher
> - Mettre les valeurs à zéro
> 

## Exercice 01-10-2021 au 30-09-2022

- Lettre des comptes 44566 - **5111** - 445662 - **4373** - 5112 - 58
- Comptes à supprimer 5111 - 4373 -58 (comptes impossibles à supprimer - contient des écritures)
- **Action** : Plan Comptable -> autoriser le lettrage de ces comptes
  Une fois ces opérations réalisées, la clôture de la comptabilité sera possible.

## Equilibre des journaux pour exercice 2021-2022

- Lettrage à corriger
- Journal Od automatique déséquilibre de 27720.00 € 
- Saisie libre filtre sur le journal OD Automatique au 01-10-2021
- Localiser le montant de 27720 au débit
> C'est une écriture pour le compte 4010047 - EDF en trop , mettre au crédit la valeur 0

## Equilibres des journaux pour exercice 2022-2023
- Journal OD automatique déséquilibré OD Automatique au 01-10-2022
- Localiser le mondant de 25050
> C'est une écriture pour le compte 4010047 - EDF en trop, metre au crédit la valeur 0

##Equilibre des jouranxu pour exercice 2023-2024
- Journal OD automatique déséquilibré OD Automatique au 01-10-2022
- Localiser le mondant de 25050
> C'est une écriture pour le compte 4010047 - EDF en trop, metre au crédit la valeur 0


## Vérification de l'exercice comptable 