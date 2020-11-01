# Prototype Pollution
<img src="https://media.discordapp.net/attachments/768928242467340328/772469748205748244/unknown.png"><br/>
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
