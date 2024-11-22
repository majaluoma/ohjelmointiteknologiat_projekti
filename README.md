# Ohjelmointiteknologiat Projekti
Tavoitteena on tutustua funktionaalisen ohjelmoinnin filosofiaan sekä tarkastella sitä suhteessa Reactin hyviin käytänteisiin. Lisäksi tavoitteena on kartuttaa ymmärrystä Reactin peruselementeistä kuten tilanhallinnasta ja koukuista.

Aloitin etsimällä lähteitä. Halusin ensin tutustua ylipäätään funktionaaliseen ohjelmointiin, joten aloitin Kyle Simpsonin (2017) kirjasta Functional-Light JavaScript: Balanced, Pragmatic FP in JavaScript.

Alla on muistiinpanoni tästä kirjasta.

Funktionaalinen ohjelmointi pyrkii koodin luettavuuteen, siihen että niin kehittäjä kuin muutkin voivat ymmärtää ja sitne myös luottaa koodiin. Ohjelmointifilosofian omaksumisen luvataan myös tutotavan vähemmän bugeja. Tässä oleellisena osana on asioiden nimeäminen, kun näet jonkin funktion, esimerkiksi `map()`, tiedät mitä se tekee, mutta kun näet sanan `for`, joudut usein lukemaan mitä se oikeastaan tekee. (Sampson 2017, ch1). Koodissa tämä näkyy myös deklaratiivisuuden lisääntymisenä, kun ohjelman osat kuvaavat lopputulosta, eikä prosessia.

Funktionaalisessa ohjelmoinnissa halutaan käyttää **funktioita** samaan tapaan, miten olemme ne yläasteellakin oppineet: `y = f(x)`. On funktio, sen parametrit ja aina palautusarvo. Jos palautusarvo on undefined, se palautetaan eksplisiittisesti.  Tästä johtuen alla olevan kaltaisia **proseduureja ei käytetä** funktionaalisessa ohjelmoinnissa (Simpson 2017, ch2).

```ts
let numerot = [1, 4, 5, 3]
let summa = 0;
function summaa (arvo) {
    summa = arvo + summa;
}
numerot.forEach(e => summaa (e))
```
Peruskäsitteitä funktiossa (Simpson 2017 ch2):
```ts
function foo(x,y,z) { //parametrit
}
foo.length // arity --> 2
const a, b = 3 // argumentit 
foo(a, b);    // 2
```
Simpson painottaa, että ohjelmoinnissa olisi aina pyrittävä deklaratiivisuuteen ja siihen että asiat selittävät itse itsensä. Esimerkiksi on parempi tehdä
```ts
function foo( [x,y,...args] = [] ) {
    console.log(x, y)
}     
function foo( args ) {
    const x, y = args[0], args[1]
    console.log(x, y)
}
```   

Näin funktion määrittelyssä tulee selville, mitä funktio tekee (Simpson 2017, ch2). Ja nyt luulen että tuli ensimmäinen yhtymäkohta react -ohjelmointiin. Voisiko tämä selittää miksi Reactissa props (=args?) -parametri aina välitetään olentona? Katsotaan tätä kirjan esimerkkiä:
```ts
function foo( {x,y} = {} ) {
    console.log( x, y );
}

foo( {
    y: 3
} );                    // undefined 3
```
Javascriptista on parametrien nimeäminen muttei argumenttien nimeämistä. Jos funktio on jotain näistä, ylläolevan tuloksen saavuttaminen on vaikeaa ilman parametrien olentohajoittamista (parameter object destructuring):

- Funktiossa on useita parametrejä ja niillä ei ole oletusarvoa.
- Funktiota saatetaan käyttää joskus vähemmällä määrällä argumentteja kuin mitä funktiolla on parametreja TAI ne voivat olla vaihtelevassa järjestyksessä

Seuraavaksi käsiteltiin funktioiden palautusarvoja. Suosituksena on aina sisältää yksi `return` -lauseke eikä käyttää sitä funktion ennenaikaiseen lopettamiseen. Funktioista voi myös palauttaa useita arvoja, joskin sitäkin tulisi välttää jos mahdollista.
```ts
   const [tila = setTila] = useState()
```
Tärkeintä on kuitenkin pitää *funktio aitona* (**pure function**), niin että sillä ei ole **sivuvaikutuksia** eikä se muuta arvoja funktion ulkopuolelta. Myös esimerkiksi `this` -käyttö nojaa implisiittisiin parametreihin, joten sitäkin olisi hyvä välttää.

Seuraavaksi laitetaan funktioita funktioiden. Funktio joka ottaa rvoksi toisen funktion, on korkeamman tason funktio, kuten `forEach()`. Simpson on erityisen pärinöissä siitä funtkioiden ominaisuudesta, että funktiot korkeamman tason funktioissa viittaavaat pysyvästi korkeamman tason argumentteihin (**closure**), vaikka niitä kutsuttaisiin myöhemmin toisessa paikassa (**scope**).

```ts
function whatAnimalSays(voice) {
return function makeVoice(addition = ""){
    console.log(voice + addition);
};
}

var giraffe = whatAnimalSays( "Kahvilikööri" );

giraffe();                 // Kahvilikööri
giraffe("!");                 // Kahvilikööri!    
```

Samalla esittelin ylläolevassa koodissa sen, miten tietoja voidaan määritellä lisää myös myöhemmissa kutsuissa. Tämä on Simpsonin mukaan tyypillistä funktionaalisessa ohjelmoinnissa. Huomaa myös, että funktiolle sisällä on määritelty nimi, vaikkei sitä käytetä missään. Tämä kannattaa lähes aina, sillä se auttaa virhelogien läpikäymisessä. Kirjoittaja itse siksi välttääkin => -notaatiota, sillä se ei mahdollista funktioiden nimeämistä. Alla olevassa esimerkissä funktiolla kuitenkin on nimi (name inferencing), koska se sijoitetaan muuttujaan:
```ts
const addExclamation = str => str+"!"
console.log (addExclamation.name) // addExclamation
```

Seuraavaksi otan tauon Simpsonin kirjasta, joka sisältää myös paljon työkaluja niille, jotka eivät pide itse funktionaalisesta ohjelmoinnista, mutta haluavat käyttää funktioita hyvin. Tutustun välissä Alan Alickovicin ja kumppaneiden (2024) kirjoittamaan Bulletproof React -repositorioon, joka sisältää kattavan kuvauksen reactista pienistä yksityiskohdista skaalautuvaan infrastruktuuriin. Repositoriolla on 28 tuhatta tykkäystä  



# Lähteet
Alickovic, Alan 2024: Bulletproof React. https://github.com/alan2207/bulletproof-react
Hemanth, H.M. 2015: Functional Programming Jargon. https://github.com/hemanth/functional-programming-jargon
Simpson, Kyle 2017: Functional-Light JavaScript: Balanced, Pragmatic FP in JavaScript. https://github.com/getify/Functional-Light-JS?tab=readme-ov-file