# Calcul frais d'approche

## Lien entre frais d'approche et prix unitaire moyen pondéré

Le **frais d'approche** et le **prix unitaire moyen pondéré (PUMP)** sont liés dans le cadre de la gestion des coûts et des stocks. Voici le lien entre ces deux notions :

### 1. **Définition des frais d'approche**
Les frais d'approche incluent tous les coûts supplémentaires nécessaires pour que des marchandises ou des matières premières soient disponibles à leur emplacement d'utilisation. Cela peut inclure :
- Les frais de transport
- Les droits de douane
- Les assurances
- Les coûts de manutention, etc.

Ces frais s'ajoutent au coût d'achat pour obtenir le coût total d'acquisition.

### 2. **Prix unitaire moyen pondéré (PUMP)**
Le PUMP est une méthode de valorisation des stocks. Il s'agit du coût moyen d'une unité de produit en fonction du coût total des marchandises disponibles (coût d'achat + frais d'approche) et de la quantité totale.

La formule est la suivante :
<code-block lang="tex">
\text{PUMP} = \frac{\text{Valeur totale du stock (achats + frais d'approche)}}{\text{Quantité totale en stock}}
</code-block>

### 3. **Lien entre les deux**
Les frais d'approche sont inclus dans la valeur totale du stock et donc dans le calcul du PUMP. Plus les frais d'approche sont élevés, plus le PUMP augmente. En d'autres termes :
- **Augmentation des frais d'approche** → **Augmentation du coût total des marchandises** → **Augmentation du PUMP**.
- **Réduction des frais d'approche** → **Réduction du coût total des marchandises** → **Diminution du PUMP**.

### Exemple concret
- Achat de 100 unités à 10 € chacune (coût initial : 1 000 €).
- Frais d'approche : 200 €.
- Quantité totale : 100 unités.

Le PUMP sera :
<code-block lang="TeX">
\text{PUMP} = \frac{\text{1000 + 300}}{\text{100}}= \text{13€}
</code-block>
<code-block lang="tex">
\text{PUMP} = \frac{1 000 \, (\text{coût d'achat}) + 200 \, (\text{frais d'approche})}{100 \, (\text{quantité totale})} = 12 € {par unité}.
</code-block>

Si les frais d'approche augmentent à 300 €, le PUMP deviendra :
<code-block lang="tex">
\text{PUMP} = \frac{1 000 + 300}{100} = 13 \, € \, \text{par unité}.
</code-block>

Ainsi, la gestion des frais d'approche a un impact direct sur la valorisation des stocks via le PUMP.

## Calcum du pump version Legend

Etape 1
<code-block lang="c#">
            article.Pump = PumpUtils.GetPump(article, DateTime.Now, forceCalcul);
</code-block>
Etape 2
<code-block lang="C#">
        private static decimal GetPumpMouvementsStock(Article article, DateTime date)
        {
            decimal pump = Decimal.Zero, stock = Decimal.Zero;

            IQueryable<MouvementStock> mouvementsStock = GetMouvementsStock(article, date);
            foreach (MouvementStock mouvementStock in mouvementsStock)
            {
                if (mouvementStock.TypeDocument == TypeDocument.Réception)
                {
                    pump = CalculPump(stock,
                                      pump,
                                      mouvementStock.Quantité,
                                      mouvementStock.ValeurUnitaire,
                                      GetFraisApproche(mouvementStock));
                }
                stock += mouvementStock.Quantité;
            }

            return pump;
        }
</code-block>

Etape 3

<code-block lang="C#">
        private static decimal CalculPump(decimal ancienStock, decimal ancienPump, decimal quantitéReçue, decimal prixAchat, decimal fraisApproche)
        {
            decimal coûtRevientUnitaire = prixAchat * (1 + fraisApproche);

            if (ancienStock <= Decimal.Zero || ancienPump <= Decimal.Zero)
                return coûtRevientUnitaire;

            decimal nouveauStock = ancienStock + quantitéReçue;
            if (nouveauStock <= Decimal.Zero)
                return coûtRevientUnitaire;

            decimal pump = ((ancienPump * ancienStock) + (coûtRevientUnitaire * quantitéReçue)) / nouveauStock;
            return pump > Decimal.Zero ? pump : Decimal.Zero;
        }
</code-block>