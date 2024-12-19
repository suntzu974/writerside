
# Etat par défaut pour l&apos;envoi de mail

Configuration -> Documents -> Modèles de mail

> Actuellement pour Ola sainte suzanne , deux modèles crées :
> - Facture client
> - Relance client

Menu **Facture client** choix du controller **Envoyer couriel** 
![EnvoyerCouriel.png](EnvoyerCouriel.png)

Le propriété Etat n'est pas mis par défault. Comment y remédier ?

Les élements à prendre en compte, Nous avons :

 - Configuration -> Documents -> Etats imprimés par défaut

Le code du controller  associé à **EnvoiCouriel**

<code-block lang="C#">
namespace WilLow.Everest3.Module.Documents.Controllers
{
    /// <summary>
    /// Envoi d'un courriel pour un objet
    /// </summary>
    public partial class EnvoiCourrielObjetController : ViewController
    {
        #region Constructeurs

        public EnvoiCourrielObjetController()
        {
            InitializeComponent();
        }
    .....
}
</code-block>

Le code du paramètrage de **Envoicouriel**

<code-block lang="C#">
namespace WilLow.Everest3.Module.Documents
{
    [NonPersistent]
    [NavigationItem(false)]
    [CreatableItem(false)]
    public class EnvoiCourrielObjetParamètres : BaseObject
    {
        #region Constructeurs

        public EnvoiCourrielObjetParamètres(Session session) : base(session) { }

        public override void AfterConstruction()
        {
            base.AfterConstruction();

            GetModèlesMail();
            GetEtats();
        }
    ......
}
</code-block>