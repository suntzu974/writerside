# Vito 11

Dans le journal OF - OD cabinet comptable
Il y a des écritures de comptes bancaires qui devraient être
affectées au journal de banque 50

| Code   | ID         |
|--------|------------|
| OF     | 159374     |
| 50     | 13292      |

Compte de banque

| ID       | COMPTE | JOURNAL |
|----------|--------|---------|
| 13293    |51250000| 13292   |
| 3035244  |51250001| 13292   | 


| ID       | COMPTE     | DEBIT   | CREDIT    |
|----------|------------|---------|-----------|
| 6982378  | 77100000   | 0       | 828.14    |
| 6982379  | 51250000   | 828.14  | 0         |
| 6982421  | 77100000   | 0       | 488.25    |
| 6982422  | 51250000   | 488.25  | 0         |
| 6982451  | 77100000   | 0       | 1506.91   |
| 6982452  | 51250000   | 1506.91 | 0         |
| 6982491  | 76800000   | 0       | 2037.75   |
| 6982492  | 51250001   | 2037.75 | 0         | 
| 6982503  | 7710000    | 0       | 683.16    |
| 6982504  | 51250000   | 683.16  | 0         |

# Action à réaliser sur la base de Vito11
<code-block lang="sql">
UPDATE COMPTABILITEGENERALE 
SET journal=13292
WHERE id IN (6982378,6982379,6982421,6982422,6982451,6982452,6982491,6982492,6982503,6982504)
</code-block>