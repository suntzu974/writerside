# Report 
## Il manque le commentaire de fin de contrôle de sous caisse

![Report-ContôleSousCaisse.png](Report-ContôleSousCaisse.png)

Se placer le rpxxxDetail
- Add sub band
> dans notre exemple c'est le sbCommentaire 
- name the Sub band (sbCommentaire) icon tools (symbole de la clé)
![sbCommentaire.png](sbCommentaire.png)
- décocher Visible
- Dans **Expression Editor**
- Ajouter **[Contrôle].[Commentaire] **
- 
Dans **scripts**
-   lbCommentaireBilletageValidé.Text = billetageValidé.Commentaire;

