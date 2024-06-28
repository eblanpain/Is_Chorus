# Is_Chorus
reference to check if a company is registered in Chorus Pro (French govt)

voir http://www.granuloshop.com/devblog/blog-post-31.html

Chorus Pro est une plateforme de l'État français, servant à la réception et au traitement des factures pour toute commande émanant d'un organisme d'État ou affilié. En 2020, 54,6 millions de factures ont été traitées via cette plateforme. Is_Chorus est une ressource OpenSource développée pour simplifier la réponse à la question préalable: cette commande émane-t-elle d'un organisme inscrit à ce référentiel. Il permet à tous les utilisateurs, quel que soit leur niveau technique, de répondre à cette question tout en étant à jour . Il est optimisé pour une faible consommation de ressources techniques et de mémoire.

Pour accéder directement à l'annuaire de Chorus Pro, visitez [adresse web de l'annuaire]. Le référentiel original, de grande taille, est efficacement condensé par Is_Chorus. L'annuaire Chorus Pro est mis à jour une fois par semaine, Is-Chorus est mis à jour dans l'heure qui suit.
Les alternatives à Is_Chorus:

Alternativement à l'utilisation d'un référentiel stock, on peut envisager unne interrogation de la page portail ou l'utilisation de l'API de Chorus. Cette dernière n'est accessible qu'au prix d'un processus long et épuisant, sans commune mesure avec l'objectif poursuivi (à la différence de SIRENE)

Is_Chorus a l"avantage de ne pas dépendre de la disponibilité d'internet, de la vitesse de réponse et d'analyse. On le télécharge et basta!

Is_Chorus RAW (brut)
--------------------
Dans la base de données Chorus Pro, l'identifiant est le SIRET de l'entreprise, composé de 14 chiffres. Il nous suffit donc d'extraire la colonne 'Identifiant' de la première feuille du tableau Excel téléchargeable depuis le site Chorus Pro. Cette première colonne devrait suffire à notre bonheur.

Le SIRET est une suite de 14 chiffres et non pas un nombre. Or, dans le fichier excel original, il est traité comme un nombre ! Quand le SIRET commence par zéro (rare, mais légitime) ce premier zéro est alors tronqué alors que le SIRET est valide: l'entrée dans chorus Pro est invalide ! Heureusement cela ne concerne que deux ou trois cas que nous aurons pris soin de corriger.
image

Dans le SIRET français, le chiffre en position numéro 9 et le chiffre en position numéro 14 sont des clés de contrôle informatique. Ainsi, la première chose à faire avec un SIRET à tester, c'est de vérifier la validité algorithique. Sachant que les identifiants dans Chorus sont aussi valides, ces deux clés deviennent inutiles. L'identifiant court sera le SIRET amputé des 9e et 14e chiffres.
On cherchera cet identifiant court parmi les identifiants courts issu de la base Chorus: c'est Is_Chorus brut. Par exemple on peut entrer Is_Chorus dans une base de données et l'interroger. La performance sera alors celle de la base de données et de l'ordinateur.

2- Is_Chorus SEGMENTED (segmenté) (à venir...)
-------------------------------
Contrairement aux apparences, le SIRET n'est pas un chiffre aléatoire: ces données de registre qui sont incrémentales et segmentées par greffe de tribunal, tout comme les codes postaux le sont par département. Ainsi on peut segmenter la base de données Is_Chorus en autant de code 'greffe'. L'avantage sera une réduction drastique de la taille de la mémoire nécessaire au stockage des informations utiles.

Cette segmentation est possible par la construction même du SIREN, qui est identique au numéro de RCS, registre tenu par le greffe du tribunal, lequel imprime son code à 3 chiffres au tout début. Ainsi donc on peut segmenter de façon formelle inintelligente le SIRET de façon à constituer autant de petites bases qu'il existe de codes 'greffe'. Sur trois chiffres, cela fait 1000 codes 'greffe'. On constate toutefois que de nombreux codes greffes ne sont pas dans Chorus. Le nombre de segments est réduit à environ 500.

On constate qu'un seul code greffe (le numéro 200) concentre à lui tout seul 10 % de tous les SIRET concernés, donc on peut sur-segmenter le code 200 pour réduire la taille de segment.

3-SIRET Méthode heuristique: Short_ID
-------------------------

La règle de segmentation par code greffe est administrative, pas heuristique. Deux observations nous aident à réduire davantage: le 10e chiffre d'un SIRET est très majoritairement 0, de même que le 11e ! Les SIRET dont le 10e chiffre ou le 11e chiffre n'est pas zéro sont très rares.
Notre méthode heuristique consiste donc supprimer, en premier lieu, les 10e, 11e et 12chiffres du SIRET s'ils sont nuls.

On appelle Short_ID celui dont on aura retiré, pour la plus grande majorité des identifiants, le 9e 10e(*) 11e(*) 12e(*) et 14e chiffre. (*) = sous condition de nullité
Cela représente une économie d'environ 30 % sur la base de données.

Le SIRET est compose du SIREN (9 chiffres, dont une clé) et di NIC (5 chiffres, dont une clé). Le SIREN, est composé du Code Greffe (3 chiffres), du registre à lonugueur fixe (5 chiffres), donc plein de zéro, de la clé de contrôle
La soustraction des zero dans les position 10e(*) 11e(*) 12e(*) est une réduction du NIC depuis sa tête. 

De même on réduit le SIREN depuis sa tête, en tenant compte du biais specifique à Chorus d'avoir beaucoup de code dans les code greffe 200...

Si sa longeur est 9, on retire le chiffre 2 de la position 1 (le début). Puis...

Si sa longeur est 8, on retire le chiffre 0 de la position 4. Puis...

Si sa longeur est 7, on retire le chiffre 1 de la position 1 (le début). Puis...

Si sa longeur est 6, on retire le chiffre 0 de la position 1 (le début). Puis...

Si sa longeur est 5, on retire le chiffre 0 de la position 1 (le début). Puis...

Si sa longeur est 4, on retire le chiffre 0 de la position 1 (le début). Puis...


En définitive, la base segmentée des identifiants dans Chorus pèse 1 Mo, et, compressée, elle ne pèse que 410 Ko ! Le plus gros segment pèse environ 50 Ko ce qui est facilement gérable en mémoire même avec un script peu rapide comme VBS ou VBA sur Windows. Bien entendu, il faut segmenter l'identifiant à rechercher dans la base de la même façon. Il y a une donc une phase de pré-traitement algorithmique pour préparer la recherche.

Les deux réutilisations de la base chorus (brut et segmentée) sont à disposition de tous sur le répertoire github.


4-Maintenance YTD  (à venir...)
---------------------------

Chorus ne publie qu'une mise à jour par semaine. Le jour est variable...
Une mise à jour ne veut pas nécessairement dire que la liste des participants à Chorus ait changé. Ainsi on peut avoir des semaines sans que Is_Chorus ne constate la moindre modification pertinente des identifiants concernés.

Si on dispose d'une base de données, on constitue une table une première fois puis on ajuste à la marge avec notre YTD (Year to date), par exemple avec un driver/recordset ou une instruction SQL.

Sans base de données, en mode texte, on pourra aussi venir corriger le dossier des fichiers texte par une simple modification marginale par copier/coller automatisable. La modification marginale est proposée à partir de la dernière base chorus connue de l'année précédente ce qui a peu de chose près revient à dire que le point de départ est le 1er Janvier de l'année en cours. En pratique il suffit donc de télécharger Is_Chorus en mode RAW ou en Segmented puis de le mettre à jour régulièrement. Une fois l'an (par exmple au premier de l'an) on fait une remise à zéro. Outre les deux zip de base de Is_Chorus, on propose donc deux zip de simple mise à jour dite YTD (Year to date) 
