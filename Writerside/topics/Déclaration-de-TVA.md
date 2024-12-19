# Déclaration de TVA

controller **Nouvelle Déclaration**
<code-block lang="C#">
namespace WilLow.Everest3.Module.Comptabilité.Controllers
{
    /// <summary>
    /// Action permettant de créer une nouvelle déclaration de TVA
    /// </summary>
    public class DéclarationTvaController : ViewController<ListView>
    {
        #region Constructeurs

        public DéclarationTvaController()
        {
            TargetObjectType = typeof(DéclarationTva);
            TargetViewNesting = Nesting.Root;

            CréeAction();
        }

        #endregion

        #region Méthodes

        private void CréeAction()
        {
    .....
}

        private void NouvelleDéclarationAction_Execute(object sender, PopupWindowShowActionExecuteEventArgs e)
        {
            DéclarationTvaParamètres paramètres = (DéclarationTvaParamètres)e.PopupWindowViewCurrentObject;
            
            IObjectSpace objectSpace = Application.CreateObjectSpace(typeof(DéclarationTva));
            DéclarationTva déclaration = objectSpace.CreateObject<DéclarationTva>();
            déclaration.DateDébut = paramètres.DateDébut;
            déclaration.DateFin = paramètres.DateFin;

            déclaration.CréationCompteTVADéclaration();
            if (déclaration.Calcul())
            {
                déclaration.CréationFormulaireCA3();

                e.ShowViewParameters.CreatedView = Application.CreateDetailView(objectSpace, déclaration, true);
                objectSpace.Committed += ObjectSpaceDéclaration_Committed;
            }
        }
</code-block>

Le formulaire CA3

![Formulaire CA3](Formulaire CA3)

Les compte
<code-block lang="C#">
TotalTVADéductibleSurBS = 10280393 
CompteTVADéductibleBS.Solde  10280393
+ CompteTVARécupérableProrata.Solde -10279606.88
+ (configuration.UtilisationProrataTVA ? -MontantNonRécupérable : Decimal.Zero);
</code-block>

CompteTVADéductibleBS => 44566
44566 -> TVA sur autres biens et services
445662 -> TVA récupérable au prorata