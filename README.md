# Javascript-this-bind-call-et-apply-
Un résumé des mots clés bind(), call() et apply() dans javascript

En javascript, les méthodes bind(), call() et apply() sont en rapport à l’objet **this**.
**this** est un objet dont la valeur varie en fonction du contexte d’exécution.

Considérons le script suivant : app.js

```javascript
console.log(this); // l'objet "this" correspond ici à l'objet "window"

var country={
    name:'France',
    capitalCity:'Paris',
    getDescription:function(){
        console.log(this); // dans ce contexte, l'objet "this" correspond à l'objet "country"
        return 'Pays: '+ this.name + '.Capitale: '+ this.capitalCity
    }
}
```

Le premier **this** (dans console.log(this)), correspond à l’objet **window**.

Le second **this** (dans l'objet country), correspond à l’objet **country**.

Lorsque que **this** est utilisé dans un objet, il fait référence à l’objet lui même. Lorsqu’il est utilisé en dehors d’un objet, il fait référence à la variable globale (dans notre exemple, la variable **window**).
Créons une nouvelle méthode ***getMoreInfoAboutCountry*** en dehors de l’objet **country**. Cette méthode prend en paramétre 2 variables (continent et nombre de population) et logge la description du pays (via la méthode ***getDescription*** de l’objet **country**) plus les 2 informations passées en paramétres (continent et population).

```javascript
var getMoreInfoAboutCountry= function(continent, population){
    console.log(this.getDescription() + '.Continent: ' + continent+ '.Population :'+ population);
};
getMoreInfoAboutCountry('Europe','67 M');
```

L’appel de la fonction ***getMoreInfoAboutCountry('Europe','67 M')*** déclenche une erreur :

*this.getDescription is not a function at getMoreInfoAboutCountry...*

La raison de cette erreur est la suivante : le **this** (this.getDescription()) fait référence à l’objet **window** (car il est utilisé en dehors d’un objet).Dans ce cas, **this.getDescription()** est équivalent à **window.getDescription()**. La méthode ***getDescription*** n’étant pas une méthode de l’objet **window**, le moteur javascript affiche l’erreur *… is not a function*.

Cependant, on peut influencer le fonctionnement interne de javascript, pour lui indiquer de pointer **this** à l’objet **country** et non à l’objet **window**. Pour cela, il existe 3 méthodes :

1. bind(): est une méthode qui permet de copier la fonction avec laquelle elle est associée, et change le contexte de **this**

```javascript
/**bind() */
var logCountryInfo= getMoreInfoAboutCountry.bind(country); // bind crée une copie de la méthode getMoreInfoAboutCountry en référençant l'objet "this" à "country"
logCountryInfo('Europe','60 M');
```

Dans notre cas, le ***bind()*** copie la méthode ***getMoreInfoAboutCountry*** et change l’objet **this** qui fait maintenant référence à l’objet passé en paramétre (**country**) et non plus à l’objet **window**.
Ainsi, lorsqu’on appelle la fonction ***logCountryInfo***, celle-ci fait appelle à **this.getDescription()** qui correspond maintenant à **country.getDescription()**.
Il existe aussi une autre maniére de créer le bind, en l’invoquant directement à la définition de la fonction :

```javascript
var getMoreInfoAboutCountry= function(continent, population){
    console.log(this.getDescription() + '.Continent: ' + continent+ '.Population :'+ population);
}.bind(country);
```

2. call(): comme la méthode ***bind()***, permet de changer le contexte de **this**, mais avec une différence principale : la méthode ***call()*** exécute directement la fonction sans créer une copie de celle-ci comme c'est le cas avec ***bind()***

```javascript
/**call() */
getMoreInfoAboutCountry.apply(country, ['Europe', '60 M']); // éxécute directement la fonction getMoreInfoAboutCountry avec l'objet "country" comme référence de "this"
```

3. apply(): st identique à ***call()***, à la différence que tous les arguments en dehors de celui qui représente l’objet **this**, doivent être passés dans un array

```javascript
/**apply() */
//getMoreInfoAboutCountry.apply(country, 'Europe', '60 M'); // Error
getMoreInfoAboutCountry.apply(country, ['Europe', '60 M']); // éxécute directement la fonction getMoreInfoAboutCountry avec l'objet "country" comme référence de "this". apply() prend un array en paramétre
```

En résumé, les 3 méthodes permettent de changer la référence de **this**. bind() n’éxécute pas la fonction, mais crée une copie de celle-ci, contrairement à call() et apply() qui exécute la fonction.
