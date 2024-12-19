# Autorisation déplacer les sous caisses

<code-block lang="C#">
     public static bool UtilisateurPossèdeDroitsNavigationCaisse()
     {
         Utilisateur utilisateur = (Utilisateur)SecuritySystem.CurrentUser;
         return utilisateur.PossèdeDroits(new PermissionDemandée(typeof(Caisse), canNavigate: true));
     }
</code-block>

# Déplacement de sous caisse

Il peut arriver que l'utilsiateur clôture une sous caisse dans une caisse non appropriée
la date de clôture est au 01-MM, et elle l'a déplacé dans une caisse clôturée au 30-M-1
Dans ce cas , il faut déplacer la sous caisse vers la caisse en cours ,

<code-block lang="sql">
 String requête = String.Format("UPDATE \"Vente\" SET \"Caisse\" = '{0}' WHERE \"SousCaisse\" = '{1}'", caisse.Oid, souscaisse.Oid);
 session.ExecuteQuery(requête);
requête = String.Format("UPDATE \"MouvementCaisse\" SET \"Caisse\" = '{0}' WHERE \"SousCaisse\" = '{1}'", caisse.Oid, souscaisse.Oid);
session.ExecuteQuery(requête);
requête = String.Format("UPDATE \"MouvementStock\" SET \"Caisse\" = '{0}' WHERE \"SousCaisse\" = '{1}'", caisse.Oid, souscaisse.Oid);
session.ExecuteQuery(requête);
requête = String.Format("UPDATE \"RemiseEnCuve\" SET \"Caisse\" = '{0}' WHERE \"SousCaisse\" = '{1}'", caisse.Oid, souscaisse.Oid);
session.ExecuteQuery(requête);
requête = String.Format("UPDATE \"Réception\" SET \"Caisse\" = '{0}' WHERE \"SousCaisse\" = '{1}'", caisse.Oid, souscaisse.Oid);
session.ExecuteQuery(requête);
</code-block>