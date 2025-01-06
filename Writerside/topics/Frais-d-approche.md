# Frais d'approche

Les frais d'approche sont l'ensemble des coûts supplémentaires qui s'ajouternt au prix d'achat initial
d'une marchandises pour la rendre disponible à son lieu de vente.

Quel est l'importance des frais d'approche ?
- Ils permettent de déterminer le coût complet d'acquisition
- Ils sont essentiels pour calculer le **prix de revient réel**
- ils influences directement la marge commerciale.

En comptabilité, les frais d'approche sont généralement incorporés 
au coût d'entrée des marchandises dans le stock

Ces frais comprennent généralement :
1. Les frais de transport
   - Transport maritime, aérien ou terrestre
   - Frais de manutention
   - Assurance transport
2. Les frais de douane et taxes
   - Droits de douane
   - Tva à l'importatation
   - Redevances diverses (Octroi de mer,...)
3. Les Frais logistiques
   - Frais de stockage temporaires
   - Frais de documenations 
4. Autres frais annexes
   - Commissions d'intermédiaires
   - Frais de change

# Schéma global du flux

<code-block lang="mermaid">
flowchart TB
subgraph Commande["Phase Commande"]
A[Commande Fournisseur] --> B[Prix d'achat initial]
end

    subgraph Frais["Accumulation des Frais d'Approche"]
        C1[Frais de Transport]
        C2[Frais de Douane]
        C3[Frais Logistiques]
        C4[Frais Annexes]
    end

    subgraph Compta["Traitement Comptable"]
        D1[Facture Fournisseur] --> D3[Comptabilisation classe 6]
        D3 --> D4[Incorporation au stock]
    end

    subgraph Calcul["Calcul des Coûts"]
        E1[Calcul du coût d'acquisition]
        E2[Calcul du prix de revient]
        E3[Détermination marge]
    end

    B --> C1 & C2 & C3 & C4
    C1 & C2 & C3 & C4 --> D1
    D4 --> E1
    E1 --> E2
    E2 --> E3

    style Commande fill:#f9f9f9,stroke:#333
    style Frais fill:#e6f3ff,stroke:#333
    style Compta fill:#fff2e6,stroke:#333
    style Calcul fill:#e6ffe6,stroke:#333
</code-block>

# Exemple d'implémentation en c# 

<code-block lang="c#">
using System;
using System.Collections.Generic;
using System.Linq;

namespace GestionFraisApproche
{
public class Article
{
public int Id { get; set; }
public string Reference { get; set; }
public string Designation { get; set; }
public decimal PrixAchat { get; set; }
public decimal Poids { get; set; }
public decimal Volume { get; set; }
public Dictionary<string, decimal> FraisApproche { get; set; } = new Dictionary<string, decimal>();
public decimal CoutTotal { get; set; }

        public Article(string reference, string designation, decimal prixAchat)
        {
            Reference = reference;
            Designation = designation;
            PrixAchat = prixAchat;
            CoutTotal = prixAchat;
        }
    }

    public class FraisApproche
    {
        public decimal Transport { get; set; }
        public decimal Douane { get; set; }
        public decimal Logistique { get; set; }
        public decimal Autres { get; set; }
        public Dictionary<string, decimal> Details { get; set; } = new Dictionary<string, decimal>();

        public decimal CalculerTotalFrais() => Transport + Douane + Logistique + Autres;
    }

    public enum CleRepartition
    {
        Valeur,
        Poids,
        Volume
    }

    public class Commande
    {
        public string Numero { get; set; }
        public DateTime Date { get; set; }
        public List<Article> Articles { get; set; } = new List<Article>();
        public FraisApproche FraisGlobaux { get; set; } = new FraisApproche();
        public CleRepartition CleRepartition { get; set; } = CleRepartition.Valeur;

        public Commande(string numero, DateTime date)
        {
            Numero = numero;
            Date = date;
        }
    }

    public class EcritureComptable
    {
        public string Journal { get; set; }
        public DateTime Date { get; set; }
        public string CompteDebit { get; set; }
        public string CompteCredit { get; set; }
        public decimal Montant { get; set; }
        public string Libelle { get; set; }
    }

    public class GestionnairesFraisApproche
    {
        private Dictionary<string, Commande> _commandes = new Dictionary<string, Commande>();
        private const decimal TVA = 0.20m;

        public Commande CreerCommande(string numero, DateTime date)
        {
            var commande = new Commande(numero, date);
            _commandes[numero] = commande;
            return commande;
        }

        public void AjouterArticle(string numeroCommande, Article article)
        {
            if (_commandes.TryGetValue(numeroCommande, out var commande))
            {
                commande.Articles.Add(article);
            }
            else
            {
                throw new KeyNotFoundException($"Commande {numeroCommande} non trouvée");
            }
        }

        public void AjouterFrais(string numeroCommande, string typeFrais, decimal montant, string description)
        {
            if (!_commandes.TryGetValue(numeroCommande, out var commande))
                throw new KeyNotFoundException($"Commande {numeroCommande} non trouvée");

            switch (typeFrais.ToLower())
            {
                case "transport":
                    commande.FraisGlobaux.Transport += montant;
                    break;
                case "douane":
                    commande.FraisGlobaux.Douane += montant;
                    break;
                case "logistique":
                    commande.FraisGlobaux.Logistique += montant;
                    break;
                default:
                    commande.FraisGlobaux.Autres += montant;
                    break;
            }

            commande.FraisGlobaux.Details[description] = montant;
        }

        public void RepartirFrais(string numeroCommande)
        {
            if (!_commandes.TryGetValue(numeroCommande, out var commande))
                throw new KeyNotFoundException($"Commande {numeroCommande} non trouvée");

            decimal baseRepartition = 0;
            var coefficients = new List<decimal>();

            // Calcul de la base de répartition
            switch (commande.CleRepartition)
            {
                case CleRepartition.Valeur:
                    baseRepartition = commande.Articles.Sum(a => a.PrixAchat);
                    coefficients = commande.Articles.Select(a => a.PrixAchat / baseRepartition).ToList();
                    break;
                case CleRepartition.Poids:
                    baseRepartition = commande.Articles.Sum(a => a.Poids);
                    coefficients = commande.Articles.Select(a => a.Poids / baseRepartition).ToList();
                    break;
                case CleRepartition.Volume:
                    baseRepartition = commande.Articles.Sum(a => a.Volume);
                    coefficients = commande.Articles.Select(a => a.Volume / baseRepartition).ToList();
                    break;
            }

            // Application des frais
            for (int i = 0; i < commande.Articles.Count; i++)
            {
                var article = commande.Articles[i];
                var coefficient = coefficients[i];

                article.FraisApproche["transport"] = commande.FraisGlobaux.Transport * coefficient;
                article.FraisApproche["douane"] = commande.FraisGlobaux.Douane * coefficient;
                article.FraisApproche["logistique"] = commande.FraisGlobaux.Logistique * coefficient;
                article.FraisApproche["autres"] = commande.FraisGlobaux.Autres * coefficient;

                article.CoutTotal = article.PrixAchat + article.FraisApproche.Values.Sum();
            }
        }

        public List<EcritureComptable> GenererEcrituresComptables(string numeroCommande)
        {
            if (!_commandes.TryGetValue(numeroCommande, out var commande))
                throw new KeyNotFoundException($"Commande {numeroCommande} non trouvée");

            var ecritures = new List<EcritureComptable>();

            // Écriture principale d'achat
            decimal totalAchat = commande.Articles.Sum(a => a.PrixAchat);
            ecritures.Add(new EcritureComptable
            {
                Journal = "AC",
                Date = commande.Date,
                CompteDebit = "607000",
                CompteCredit = "401000",
                Montant = totalAchat,
                Libelle = $"Facture {commande.Numero}"
            });

            // Écritures des frais d'approche
            if (commande.FraisGlobaux.Transport > 0)
            {
                ecritures.Add(new EcritureComptable
                {
                    Journal = "AC",
                    Date = commande.Date,
                    CompteDebit = "608100",
                    CompteCredit = "401000",
                    Montant = commande.FraisGlobaux.Transport,
                    Libelle = $"Transport {commande.Numero}"
                });
            }

            // Autres frais similaires...

            return ecritures;
        }

        public decimal CalculerPrixRevient(Article article, decimal margeStandard)
        {
            return article.CoutTotal * (1 + margeStandard);
        }
    }

    // Extension pour faciliter les tests et l'utilisation
    public static class Exemple
    {
        public static void DemonstrationUtilisation()
        {
            var gestionnaire = new GestionnairesFraisApproche();
            
            // Création d'une commande
            var commande = gestionnaire.CreerCommande("CMD001", DateTime.Now);
            
            // Ajout d'articles
            var article1 = new Article("REF001", "Article 1", 1000m) { Poids = 10m };
            var article2 = new Article("REF002", "Article 2", 2000m) { Poids = 20m };
            
            gestionnaire.AjouterArticle("CMD001", article1);
            gestionnaire.AjouterArticle("CMD001", article2);
            
            // Ajout des frais
            gestionnaire.AjouterFrais("CMD001", "transport", 500m, "Transport maritime");
            gestionnaire.AjouterFrais("CMD001", "douane", 150m, "Droits de douane");
            gestionnaire.AjouterFrais("CMD001", "logistique", 200m, "Stockage temporaire");
            
            // Répartition des frais
            gestionnaire.RepartirFrais("CMD001");
            
            // Génération des écritures
            var ecritures = gestionnaire.GenererEcrituresComptables("CMD001");
        }
    }
}
</code-block>