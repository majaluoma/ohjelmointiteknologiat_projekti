# Ohjelmointiteknologiat Projekti

Tavoitteena on tutustua funktionaalisen ohjelmoinnin filosofiaan sekä tarkastella sitä suhteessa Reactin hyviin käytänteisiin. Lisäksi tavoitteena on kartuttaa ymmärrystä Reactin peruselementeistä kuten tilanhallinnasta ja koukuista.

Aloitin etsimällä lähteitä. Halusin ensin tutustua ylipäätään funktionaaliseen ohjelmointiin, joten aloitin Kyle Simpsonin (2017) kirjasta Functional-Light JavaScript: Balanced, Pragmatic FP in JavaScript.


## Funktionaalisen ohjelmoinnin perusteita
Funktionaalinen ohjelmointi pyrkii koodin luettavuuteen, siihen että niin kehittäjä kuin muutkin voivat ymmärtää ja sitne myös luottaa koodiin. Ohjelmointifilosofian omaksumisen luvataan myös tutotavan vähemmän bugeja. Tässä oleellisena osana on asioiden nimeäminen, kun näet jonkin funktion, esimerkiksi `map()`, tiedät mitä se tekee, mutta kun näet sanan `for`, joudut usein lukemaan mitä se oikeastaan tekee. Koodissa tämä näkyy myös deklaratiivisuuden lisääntymisenä, kun ohjelman osat kuvaavat lopputulosta, eikä prosessia.(Simpson 2017, ch1). 

Funktionaalisessa ohjelmoinnissa halutaan käyttää **funktioita** samaan tapaan, miten olemme ne yläasteellakin oppineet: `y = f(x)`. On funktio, sen parametrit ja aina palautusarvo. Jos palautusarvo on undefined, se palautetaan eksplisiittisesti. Tästä johtuen alla olevan kaltaisia **proseduureja ei käytetä** funktionaalisessa ohjelmoinnissa (Simpson 2017).

```ts
let numerot = [1, 4, 5, 3];
let summa = 0;
function summaa(arvo) {
    summa = arvo + summa;
}
numerot.forEach((e) => summaa(e));
```

Peruskäsitteitä funktiossa (Simpson 2017):

```ts
function foo(x, y, z) {
    //parametrit
}
foo.length; // arity --> 2
const a,
    b = 3; // argumentit
foo(a, b); // 2
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

Näin funktion määrittelyssä tulee selville, mitä funktio tekee (Simpson 2017). Ja nyt luulen että tuli ensimmäinen yhtymäkohta react -ohjelmointiin. Voisiko tämä selittää miksi Reactissa props (=args?) -parametri aina välitetään olentona? Katsotaan tätä kirjan esimerkkiä:

```ts
function foo({ x, y } = {}) {
    console.log(x, y);
}

foo({
    y: 3,
}); // undefined 3
```

Javascriptista on parametrien nimeäminen muttei argumenttien nimeämistä. Jos funktio on jotain näistä, ylläolevan tuloksen saavuttaminen on vaikeaa ilman parametrien olentohajoittamista (**parameter object destructuring**) (Simpson 2017):

-   Funktiossa on useita parametrejä ja niillä ei ole oletusarvoa.
-   Funktiota saatetaan käyttää joskus vähemmällä määrällä argumentteja kuin mitä funktiolla on parametreja TAI ne voivat olla vaihtelevassa järjestyksessä

Seuraavaksi käsiteltiin funktioiden palautusarvoja. Suosituksena on aina sisältää yksi `return` -lauseke eikä käyttää sitä funktion ennenaikaiseen lopettamiseen. Funktioista voi myös palauttaa useita arvoja, joskin sitäkin tulisi välttää jos mahdollista. (Simpson 2017).

```ts
const [tila = setTila] = useState();
```

Tärkeintä on kuitenkin pitää _funktio aitona_ (**pure function**), niin että sillä ei ole **sivuvaikutuksia** eikä se muuta arvoja funktion ulkopuolelta. Myös esimerkiksi `this` -käyttö nojaa implisiittisiin parametreihin, joten sitäkin olisi hyvä välttää. (Simpson 2017).

Seuraavaksi laitetaan funktioita funktioiden sisään. Funktio joka ottaa arvoksi  toisen funktion, on korkeamman tason funktio, kuten `forEach()`. Simpson on erityisen pärinöissä siitä funtkioiden ominaisuudesta, että funktiot korkeamman tason funktioissa viittaavaat pysyvästi korkeamman tason argumentteihin (**closure**), vaikka niitä kutsuttaisiin myöhemmin toisessa paikassa (**scope**). (Simpson 2017).

```ts
function whatAnimalSays(voice) {
    return function makeVoice(addition = "") {
        console.log(voice + addition);
    };
}

var giraffe = whatAnimalSays("Kahvilikööri");

giraffe(); // Kahvilikööri
giraffe("!"); // Kahvilikööri!
```

Samalla esittelen ylläolevassa koodissa sen, miten tietoja voidaan määritellä lisää myös myöhemmissa kutsuissa. 

Tämä on Simpsonin mukaan tyypillistä funktionaalisessa ohjelmoinnissa. Huomaa myös, että funktiolle sisällä on määritelty nimi, vaikkei sitä käytetä missään. Tämä kannattaa lähes aina, sillä se auttaa virhelogien läpikäymisessä. Simpson itse siksi välttääkin => -notaatiota, sillä se ei mahdollista funktioiden nimeämistä. Alla olevassa esimerkissä funktiolla kuitenkin on nimi (name inferencing), koska se sijoitetaan muuttujaan. (Simpson 2017).

```ts
const addExclamation = (str) => str + "!";
console.log(addExclamation.name); // addExclamation
```

Seuraavaksi otan tauon Simpsonin kirjasta, joka sisältää myös paljon työkaluja niille, jotka eivät pidä itse 
funktionaalisesta ohjelmoinnista, mutta haluavat käyttää funktioita hyvin. 

## Tapausesimerkki laadukkaasta React -ohjelmoinnista.
Tutustun välissä Alan Alickovicin ja kumppaneiden (2024) kirjoittamaan Bulletproof React -repositorioon, joka sisältää kattavan kuvauksen reactista pienistä yksityiskohdista skaalautuvaan infrastruktuuriin. Repositoriolla on 28 tuhatta tykkäystä ja useampi yhteiskirjoittaja. 


React projektissa on hyvä pitää huoli hyvistä kirjoitusstandardeista (Alickovic ym. 2024), sillä kuten Simpsonkin totesi, funktionaalisessa ohjelmoinnissakin pyritään hyvään kommunikaatioon (Simpson 2017, ch1). Tämän mahdollistaa projektikohtaiset asetustiedostot root -kansiossa (Alickovic 2024):

- .eslintrc kopnfiguroi syntaksivirheiden korjaamisen ja siihen kannattaa lisätä myös nimeämiskäytänteet
- .prettierrc säännönmukaistaa tyylittelyt
- tsconfig.json poistaa javascriptin dynaamisuuden tuomat haasteet. Lisäksi se sisältää absoluuttiset `import` polut.
- .husky/ sisältää konfiguroinnit eri git -komennoille

Erimerkki tiedostojen nimeämiskäytänteiden lisäämisestä .eslintrc -asetuksiin (Alickovic 2024). 
```json
'check-file/filename-naming-convention': [
          'error',
          {
            '**/*.{ts,tsx}': 'KEBAB_CASE',
          },
          {
            ignoreMiddleExtensions: true,
          },
```
Esimerkki absoluuttisen import -polun määrittelystä (Alickovic 2024)
```json
"paths": {
      "@/*": ["./src/*"]
    }
```

Ohjelmistoprojektissa käytämme Viteä ja tähän teknologiaankin nojautuen Alickovic ja kumppanit ehdottavat seuraavanlaista tiedostorakennetta projektiin (kommentteja muokattu alkuperäisestä):

```sh
src
|
+-- app               # application layer:
|   |                 
|   +-- routes        # can also be pages
|   +-- app.tsx       # main application component
|   +-- provider.tsx  # application provider differ based on meta framework 
|   +-- router.tsx    # application router configuration
+-- assets            # assets folder can contain all the static files such as images, fonts, etc.
|
+-- components        # components used across the entire application
|
+-- config            # global configurations, exported env variables etc.
|
+-- features          # feature based modules
|
+-- hooks             # shared hooks used across the entire application
|
+-- lib               # reusable libraries preconfigured for the application
|
+-- stores            # global state stores
|
+-- testing           # test utilities and mocks
|
+-- types             # shared types used across the application
|
+-- utils             # shared utility functions
```
(Mukaillen Alickovic 2024).

Suurin osa koodista suositellaan pitämään features -kansiossa ominaisuuksittain. Ominaisuus voi aina sisältää omat kansionsa api, assets, components, hooks, stores, types ja utils. Kansiot kannattaa aina luoda tarpeen niin vaatiessa. Ominaisuuksien välisiä importointeja pitäisi ehdottomasti välttää. Projektin rakennetta käsittelevässä kappaleessa annetaan suoria vinkkejä esimerkiksi eslintin konfiguroimisesta siten, että ylläolevaa myös säädellään koodina. (Alickovic 2024).

![alt text](image.png)  
KUVA 1. Yhteisesti jaettu src/ sisältää omat komponenttinsa ja tyyppinsä, mutta myös ominaisuudet sisältävät omansa.

### Komponentit
Komponentit lähtevät helposti kasvamaan jolloin ne pitää tietenkin pilkkoa. Propsien määräkin kannattaa olla kohtuullinen ja esimerkkiprojektissa 7 oli esitelty isoksi määräksi, joskin niistäkin neljälle oli määritelty oletusarvo. (Alickovic 2024). 

Kolmannen osapuolen komponentit kannattaa kapseloida omiksi komponenteikseen, jotta niiden vaihtaminen tarvittaessa on helpompaa (Alickovic 2024).

Jaetut komponentit kannattaa järjestellä isommiksi kokonaisuuksiksi. Esimerkiksi polku `src/components/ui/button` sisältää komponentin `button.tsx`. Alickovicin repositoriossa näitä komponenttien "abstrahointeja" on neljä, mutta näiden tulee vaihdella aina projektin tarpeiden mukaan. (Alickovic 2024).
```sh
src
|
+-- components               # application layer containing:
.   |                 
.   +-- errors        # virhesivu
.   +-- layouts       # perusasettelut kirjautumissivulle, sisältösivulle ja ohjauspaneelille
    +-- seo  # hakukoneoptimointikomponentti
    +-- ui # Sisältää uudelleenkäytettäviä peruskomponentteja kuten napin, dialogin, taulukon jne.
```
(Mukaillen Alickovic 2024)
## API -taso
React sovelluksessa kannattaa olla yksi api tason instanssi . He käyttävät repositoriossaan Axiosia, joka konfiguroi valmiiksi setin funktioita. Tässä on kyse parametrien määrittelystä myöhemmin, sillä Axios.create() -funktio sulkee (**closure**), API_URL -ympäristömuuttujan. (Alickovic 2024).

```ts
//src/lib/api-client.ts
export const api = Axios.create({
  baseURL: env.API_URL,
});

//src/features/teams/api
api.get('/teams')
```

Jokaisen API -kutsun tulisi sisältää lisäksi seuraavat asiat:
- tyypityykset ja validaatiot pyyntö- ja vastausdatalle
- itse fetch -funktion, joka kutsuu rajapintaa.
- hookin, joka käsittelee vastaus ja lähetysdatan käsittelyn, tämä määrittyy sen mukaan, millaisen kirjaston on valinnut.

(Alickovic 2024)
## Tilojen hallinnointi 
Lähtökohtaisesti kannattaa Reactissa hyödyntää **komponenttitason** tiloja `useState()` tai `useReducer()`. Se on tehokasta. Jos se ei ole mahdollista, voi tilan ylentää **ohjelmistotason** tilaksi `useContext()`. Tätä tarvitaan kahdessa tilanteessa. (Alickovic 2024):  

- tilaa tarvitaan rinnakkaisessa komponentissa (Lifting state up)
- propsia välitetään useampia komponenttikerroksia alas (Props drilling).   

Konteksti kannattaa tällöin sijoittaa mahdollisimman lähelle sitä tarvitsevia komponentteja. Tila voidaan myös ylentää palvelinvarastotilaksi (**server cache state**), eli hakea tieto palvelimelta ja tallentaa se käyttäjän paikallisiin tiedostoihin. Alickovicin repositoriossa näin tehdään, kun käyttäjä vie hiiren napin päälle. Tieto haetaan heti, tallennetaan käyttäjän tietoihin ja sitä käytetään sitten jos käyttäjä päättää painaa nappia. Lomakkeissa on lisäksi omat tilanhallintatyökalunsa, joita en nyt käsittele tässä. (Alickovic 2024).

# Lähteet

Alickovic, Alan and many other collaborators 2024: Bulletproof React. https://github.com/alan2207/bulletproof-react
Hemanth, H.M. 2015: Functional Programming Jargon. https://github.com/hemanth/functional-programming-jargon
Simpson, Kyle 2017: Functional-Light JavaScript: Balanced, Pragmatic FP in JavaScript. https://github.com/getify/Functional-Light-JS?tab=readme-ov-file
Github Typicode 2024: Husky - Get started. https://typicode.github.io/husky/get-started.html
Eslint 2024 Find and fix problems in your JavaScript code.  https://eslint.org/
React 2024: Passing Data Deeply with Context.  https://react.dev/learn/passing-data-deeply-with-context