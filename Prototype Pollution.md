# Prototype Pollution
<img src="https://media.discordapp.net/attachments/768928242467340328/772469748205748244/unknown.png"><br>
```HTML
<!DOCTYPE html>
<html>
<head>
</head>
<body style="background-color:black;color:red;">
<div style="font-size:18px;" id="maindiv">Fournissez un paramètre JSON valide {} !</div>
<script>
monjson=function(x)
{
    if(x)
    {
        return x;
    }
    else
    {
    return({"steins":"ijzosve","iwa":"trimardopsecblabla"});
    }
}
var parametres=location.toString().split("?");
if(parametres.length>1)
{
    parametres=unescape(parametres[1]);
    var tmp=false;
     if(parametres.split('{').length>2)
    {
        tmp=new monjson(JSON.parse('{'+parametres.split('{')[2].replace(/}$/,"")))
    }
     if(parametres.split('"')[1].length>7 && tmp)
    {    
        monjson[parametres.split('"')[1]]=tmp;
        if(monjson.steins)
        {
            document.getElementById("maindiv").innerHTML="Salut à  toi jeune "+monjson.steins;
        }
        else
        {
            document.getElementById("maindiv").innerHTML="Salut à  toi jeune "+new monjson().steins;
        }
    }
    else
    {
                document.getElementById("maindiv").innerHTML="<b style='color:red;'>Salut à  toi jeune "+new monjson().steins+"</b>";
    }
    ("test");
}    
</script>
</body>
</html>
```
## Contextualisation
- La pollution de prototype est une vuln spécifique au JS. Il découle d'un modèle d'héritage JS appelé **prototype-based inheritance** . Contrairement à PHP, C++ ou même Java, en JS vous n'avez pas besoin de définir une classe pour créer un objet. Il vous suffit d'utiliser la notation entre accolades et de définir les propriétés.
```JS
const obj = {
  prop1: 111,
  prop2: 222,
}
```
- Cet objet a deux propriétés, notamment **prop1** et **prop2**. Figurez-vous que ce ne sont pas les seules propriétés dont nous avons accès. Si on prend un exemple, un simple call de **obj.toString()** nous retourne **"[object Object]"**, ça provient du prototype. Tout objet en JS a un prototype (à l'exception qu'il soit null). Si le prototype n'est pas spécifié il aura comme prototype par défaut : **Object.prototype**.<br>
### On peut facilement voir la liste des propriétés de Object.prototype à l'aide de DevTools
<img src="https://media.discordapp.net/attachments/771819360109068328/772447460215881728/image-1024x351.png"><br><br>
- **On peut aussi trouver quel objet est le prototype d'un objet spécifié. Ici à l'occurrence c'est ``__proto__``.**<br><br>
<img src="https://media.discordapp.net/attachments/771819360109068328/772448129902575656/image-1.png"><br><br>
- **On peut aussi presset le prototype de l'objet en utilisant ``__proto__``.**<br><br>
<img src="https://media.discordapp.net/attachments/771819360109068328/772448426901110794/image-2.png"><br><br>
- **Dans l'exemple qui va suivre, obj1 a été créé en utilisant la méthode curly bracket de la notation JS, obj2 est créé avec JSON.parse.**<br><br>
<img src="https://media.discordapp.net/attachments/771819360109068328/772451369742368808/image-3-1024x219.png"><br>
## Exploitation
- URL : http://www.gob.com.pl/CTF/prototype%20pollution/?x={%22__proto__%22:{%22steins%22:%22%3Csvg/onload=alert(%27XSS%27)%3E%22}}
<img src="https://media.discordapp.net/attachments/768928242467340328/772471742022811688/unknown.png"><br>
### Manuel
- Itère à travers tous les prototypes dans l'objet "**monjson**". La seule propriété est ``__proto__``<br>
- Check si ``monjson.__proto__`` existe, là c'est le cas<br>
- Itère à travers toutes les propriétés dans ``steins.__proto__``. La seule propriété est "x"<br>
- Assigne ``monjson.__proto__.x`` = ``steins.__proto__.x`` parce-que ``monjson.__proto__`` pointe au **Object.prototype**, c'est là que le prototype est pollué.<br>
