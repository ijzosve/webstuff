# DOM XSS in AngularJS expression
- Le but ici va être d'employer des expressions du framework AngularJS (listées par les contrôleurs ES6) qui nous permettront d'exploiter des fonctions arbitraires, comme faire un appel à l'expression $window.alert(1) par exemple.<br/>
## Reconnaissance
- Ici je teste l'expression {{1+2}}. Si l'expression est bien prise en compte le navigateur nous renverra le résultat de l'équation "1+2", donc "3".<br/><br/>
<img src="https://image.noelshack.com/fichiers/2020/43/7/1603582102-ezgif-com-gif-maker.gif"/><br/><br/>
(On constate que l'expression a été prise en compte, maintenant on a plus qu'à construire une expression qui fait un appel au DOM.<br/>
## Exploitation
- Pour ce faire on va construire notre payload en faisant appel à la fonction $window.alert(1) dans le DOM.<br/>
- Voici la payload : {{$on.constructor('alert(1)')()}}. On crée une expression avec $on.constructor()() qui fait un appel à l'attribut $window.alert(1).<br/><br/>
<img src="https://media.discordapp.net/attachments/742814768233971763/769710554063175720/ezgif.com-gif-maker_2.gif"/><br/><br/>
- Et hop on a pu interférer avec le DOM pour exploiter une XSS.<br/>
