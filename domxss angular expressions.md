<h1>DOM XSS in AngularJS expression</h1><br/>
<h3>Le but ici va être d'employer des expressions du framework AngularJS (listées par les contrôleurs ES6) qui nous permettront d'exploiter des fonctions arbitraires, comme faire un appel à l'expression $window.alert(1) par exemple.</h3><br/>
<h1>Exploitation</h1><br/>
<h3>Ici je teste l'expression {{1+2}}. Si l'expression est bien prise en compte le navigateur nous renverra le résultat de l'équation "1+2", donc "3".</h3><br/>
<img src="https://image.noelshack.com/fichiers/2020/43/7/1603582102-ezgif-com-gif-maker.gif"/><br/>
<h3>On constate que l'expression a été prise en compte, maintenant on a plus qu'à construire une expression qui va pouvoir faire un appel au DOM.</h3><br/>
<h1>Exploitation $Window.alert(1)</h1><br/>
<h3>Pour ce faire on va construire notre payload en faisant appel à la fonction $window.alert(1) dans le DOM.</h3><br/>
<h3>Voici la payload : {{$on.constructor('alert(1)')()}}. On crée une expression avec $on.constructor()() qui fait un appel à l'attribut $window.alert(1).<h3><br/>
<img src="https://media.discordapp.net/attachments/742814768233971763/769710554063175720/ezgif.com-gif-maker_2.gif"/><br/>
<h3>Et hop on a pu interférer avec le DOM pour exploiter une XSS.</h3>
