# Modèle de vente - Cloner

La vente par **Abonnement** permet de facturer un client, selon un **ModèleVente** qui contient **LigneModèleVente**
Abonnement d'un client.

En fin de mois, la génération des ventes pour le client va facturer les produits auxquels il a souscrit
Exemple :

![AbonnementClient.png](AbonnementClient.png)


![ModèleVenteClient.png](ModèleVenteClient.png)


Exemple de code

<code-block lang="C#">
private int loc;
</code-block>

<code-block lang="C#">
    using DevExpress.ExpressApp;
    using DevExpress.ExpressApp.Actions;
    using DevExpress.ExpressApp.SystemModule;
    using System.Collections.Generic;
    public class CloneListViewController : ObjectViewController&lt;ListView,LigneModèleVente&gt; {
        public CloneListViewController() {
        // Créer une action de clonage pour la ListView
        var cloneAction = new SimpleAction(
        this, "CloneObjects", DevExpress.Persistent.Base.PredefinedCategory.Edit) {
        Caption = "Clone Selected",
        ConfirmationMessage = "Are you sure you want to clone the selected objects?",
        ImageName = "Action_Copy", // Icône de l'action (facultatif)
        SelectionDependencyType = SelectionDependencyType.RequireMultipleObjects // Nécessite une sélection
        };
        cloneAction.Execute += CloneAction_Execute;
        Actions.Add(cloneAction);
    }
    private void CloneAction_Execute(object sender, SimpleActionExecuteEventArgs e) {
        // Récupérer les objets sélectionnés dans la ListView
        var selectedObjects = View.SelectedObjects;
        var clonedObjects = new List&lt;object&gt;();
        if (selectedObjects != null) {
            foreach (var selectedObject in selectedObjects) {
                if (selectedObject is ICloneable cloneable) {
                    // Cloner l'objet et l'ajouter à l'espace d'objet
                    var clonedObject = cloneable.Clone();
                    var persistentClone = ObjectSpace.GetObject(clonedObject); // Sauvegarde persistante
                    clonedObjects.Add(persistentClone);
                }
            }
            // Sauvegarder les modifications
            ObjectSpace.CommitChanges();
            // Actualiser la vue
            View.Refresh();
            // Facultatif : afficher les clones dans une nouvelle vue ou un message
            Application.ShowViewStrategy.ShowMessage($"Cloned {clonedObjects.Count} objects.", InformationType.Success);
        }
    }
}

</code-block>

