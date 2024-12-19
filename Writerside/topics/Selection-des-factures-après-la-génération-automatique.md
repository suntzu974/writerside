# Selection des factures après la génération automatique

- Après avoir généré les factures client automatiquement, 
il faut les sélectionner dans la liste en utilisant la propriété **ImpressionFacturationAutomatique** 
du client de chaque facture.

- Lorsque les factures viennent d'être générées, 
il faut remplir la liste FacturesAImprimer à l'aide de la propriété **ImpressionFacturationAutomatique** 
du client de chaque facture.
C'est à faire dans la méthode **FacturationClientAutomatique.GénèreFactures**

- S'assurer que la liste est bien mise à jour dans l'interface 
(seules les factures à imprimer sont sélectionnées)

Dans la méthode ImpressionFacturesAutomatiquesController.ImprimeFacturesClient, 
utiliser la propriété **FacturationClientAutomatique.FacturesAImprimer.**

- Il ne faut plus utiliser la requête Linq de cette méthode qui va chercher sur le client de la facture

Comme pour l'impression des factures, il faut utiliser la propriété FacturesAImprimer 
pour l'export PDF,l'envoi par mail

## Le process de génération des factures.

<code-block lang="Mermaid">
stateDiagram-v2
       [*] --> FacturationClientAutomatiqueController
       FCA: FacturationClientAutomatique
       FCAG: FacturationClientsAutomatiqueGénérer
       FG: FacturesGénérées
       NU: Need updates
       AC: Apply changes
       LGTM: All good
       RP: Ready to publish
       FacturationClientAutomatiqueController --> FCA
       FCA --> FCAG
       FCAG --> FG
       FG --> FacturesAImprimer
       FacturesAImprimer --> NU
       NU --> AC
       AC --> FacturesAImprimer
       FacturesAImprimer --> LGTM
       LGTM --> RP
       RP --> [*]
</code-block>

## Remise à zéro des factures générées 

<code-block lang="SQL">
select oid,NomComplet,DateDernièreFacturationAutomatique,ImpressionFacturationAutomatique
from client
where DateDernièreFacturationAutomatique >= convert(datetime,'2024-11-27',120)

-- Mettre à jour ImpressionFacturationAutomatique
update client
set ImpressionFacturationAutomatique = 1
where oid in (
'0DAC40E3-2B7F-4E69-86F8-00BDCD09C1B1'
,'0A66E581-E559-42B6-93C2-03A51B7F5169'
,'37FC8506-AB0A-4AE0-8B8B-066055DBB5EA'
,'70726914-6D06-498F-9D66-B26C5AD1FA2F'
,'38F888AD-6B6F-4FA6-90F6-CF77702D1BAB'
,'BE6B38AE-8F64-4324-93E0-C7629F297666')
-- Ecriture comptable
delete
from EcritureComptable
where FactureClient in (
select oid
from FactureClient
where client in (select oid
from client
where DateDernièreFacturationAutomatique >= convert(datetime,'2024-11-27',120))
and DateModification >= convert(datetime,'2024-11-27',120))


-- Vente
update vente
set factureclient = null
where factureclient in (
select oid
from FactureClient
where client in (select oid
from client
where DateDernièreFacturationAutomatique >= convert(datetime,'2024-11-27',120))
and DateModification >= convert(datetime,'2024-11-27',120))

-- Facture client
delete
from FactureClient
where client in (select oid
from client
where DateDernièreFacturationAutomatique >= convert(datetime,'2024-11-27',120))
and DateModification >= convert(datetime,'2024-11-27',120)


-- Client
update client
set DateDernièreFacturationAutomatique = null
where DateDernièreFacturationAutomatique >= convert(datetime,'2024-11-27',120)


</code-block>

![GénérationFactureAutomatique.png](GénérationFactureAutomatique.png)