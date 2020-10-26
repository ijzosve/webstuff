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
- Le fichier phpinfo.php existe, donc je le renvoie vers le repeater.   
