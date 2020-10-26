# PHP deserialization with pre-built gadget chain
- En informatique, la sérialisation est le codage d'une information sous la forme d'une suite d'informations plus petites pour, par exemple, sa sauvegarde ou son transport sur le réseau. Ce qu'on va faire là c'est l'inverse de ce procédé. On va décoder des informations appartenant au système Symfonie.<br/>
## Exploitation
- En premier temps je lance Burpsuite et je le laisse tourner en arrière-plan pendant que je fais ma prochaine manipulation.<br/>
- Ensuite je me login pour générer des requêtes. L'énnonce nous indique que le login par défaut est "wiener" et "peter".<br/><br/>
<img src="https://media.discordapp.net/attachments/768928242467340328/770030149490966548/unknown.png"/><br/><br/>
- Je check l'historique des requêtes et je sélectionne l'index où le procédé du login a été sérialisé.<br/><br/>
<img src="https://media.discordapp.net/attachments/768928242467340328/770031231060017192/unknown.png?width=1195&height=890"/><br/><br/>
- Ici on constate que le token de la session "Wiener:Peter" a été encodé en URL. Par conséquent j'envoie la partie codée vers le décodeur de Burpsuite.<br/><br/>
<img src="https://media.discordapp.net/attachments/768928242467340328/770032331141677066/unknown.png"/><br/><br/>
- Je décode en URL et hop, la section est plus claire.<br/><br/>
<img src="https://media.discordapp.net/attachments/768928242467340328/770033158891503656/unknown.png"/><br/><br/>
- On voit qu'il y a une partie codée en base64, je la décode.<br/><br/>
<img src="https://media.discordapp.net/attachments/768928242467340328/770033590125199370/unknown.png"/><br/><br/>
- Passons, je vais garder tout ça pour plus tard. Ce qui m'intéresse maintenant c'est de savoir sous quel framework est utilisé par le serveur-web.<br/>
- Pour ce faire j'envoie la requête vers le repeater et je supprime la partie "7D" du % qui est en réalité une accolade mais encodé en URL pour par conséquent générer une erreur par le framework utilisé.<br/><br/>
<img src="https://media.discordapp.net/attachments/768928242467340328/770310427426684979/unknown.png"/><br/><br/>
- On sait maintenant que le framework Symfonie de la version 4.3.6 est utilisé.<br/>
- Maintenant on check les ressources du site-web pour trouver le fichier PHP de configuration.<br/><br/>
<img src="https://media.discordapp.net/attachments/768928242467340328/770311153867554927/unknown.png"/><br/><br/>
- Le fichier phpinfo.php existe donc je le renvoie vers le repeater.<br/>
- Il me faut le SECRET_KEY pour créer mon gadget donc je cherche la keyword et je tombe sur ça.<br/><br/>
<img src="https://media.discordapp.net/attachments/768928242467340328/770312787452493834/unknown.png?width=1095&height=630"/><br/><br/>
- Maintenant je passe au développement du gadget chain.<br/><br/>
<img src="https://media.discordapp.net/attachments/768928242467340328/770316142191181834/unknown.png"/><br/><br/>
- Pour créer l'object (RCE) qu'on voudra faire exécuter au serveur-web. Je fais l'usage de PHPGGC, mais qu'est-ce que c'est au juste ?<br/>
- PHPGGC est une bibliothèque de payloads unserialize() et un outil qui les génère. Lorsqu'il y a une désérialisation sur un site Web dont vous n'avez pas le code, ou simplement lorsque vous essayez de créer un exploit, cet outil vous permet de générer la payload sans avoir à passer par les étapes fastidieuses de recherche de gadgets et de combinaison. Il peut être vu comme l'équivalent de ysoserial ou même frohoff, mais ce pour le langage PHP. Actuellement, l'outil prend en charge les frameworks: CodeIgniter4, Doctrine, Drupal7, Guzzle, Laravel, Magento, Monolog, Phalcon, Podio, Slim, SwiftMailer, Symfony, Wordpress, Yii et ZendFramework.<br/>
- La source se trouve ici : https://github.com/ambionics/phpggc.<br/>
- En faisant attention, on a pu voir que le serveur-web utilisait le framework Symfony donc ça tombe bien vu que PHPGGC accepte ce framework.<br/>
- La version du framework était 4.3.6.<br/><br/>
<img src="https://media.discordapp.net/attachments/768928242467340328/770318095209725952/unknown.png"/><br/><br/>
- On a plus qu'à générer la payload avec la commande qu'on voudra faire exécuter au serveur-web.<br/>
- L'énoncé nous dit qu'on doit supprimer le fichier morale.txt qui se trouve dans le répertoire "/home/carlos".<br/>
- Je vais donc devoir générer une payload adéquate.<br/><br/>
<img src="https://media.discordapp.net/attachments/768928242467340328/770319551245582336/unknown.png"/><br/><br/>
