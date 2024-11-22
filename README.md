# Ohjelmointiteknologiat Projekti
Tavoitteena on tutustua funktionaalisen ohjelmoinnin filosofiaan sekä tarkastella sitä suhteessa Reactin hyviin käytänteisiin. Lisäksi tavoitteena on kartuttaa ymmärrystä Reactin peruselementeistä kuten tilanhallinnasta ja koukuista.

Aloitin etsimällä lähteitä. Halusin ensin tutustua ylipäätään funktionaaliseen ohjelmointiin, joten aloitin Kyle Simpsonin (2017) kirjasta Functional-Light JavaScript: Balanced, Pragmatic FP in JavaScript.

Alla on muistiinpanoni tästä kirjasta.

Funktionaalisessa ohjelmoinnissa halutaan käyttää **funktioita** samaan tapaan, miten olemme ne yläasteellakin oppineet: `y = f(x)`. On funktio, sen parametrit ja aina palautusarvo. Jos palautusarvo on undefined, se palautetaan eksplisiittisesti.  Tästä johtuen alla olevan kaltaisia **proseduureja ei käytetä** funktionaalisessa ohjelmoinnissa (Simpson 2017, ch2).

    let numerot = [1, 4, 5, 3]
    let summa = 0;
    function summaa (arvo) {
        summa = arvo + summa;
    }
    numerot.forEach(e => summaa (e))

Peruskäsitteitä funktiossa (Simpson 2017 ch2):

    function foo(x,y,z) { //parametrit
    }
    foo.length // arity --> 2
    const a, b = 3 // argumentit 
    foo(a, b);    // 2

Simpson painottaa, että ohjelmoinnissa olisi aina pyrittävä deklaratiivisuuteen ja siihen että asiat selittävät itse itsensä. Esimerkiksi on parempi tehdä

    function foo( [x,y,...args] = [] ) {
        console.log(x, y)
    }     
    function foo( args ) {
        const x, y = args[0], args[1]
        console.log(x, y)
    }   

Näin funktion määrittelyssä tulee selville, mitä funktio tekee (Simpson 2017, ch2). Ja nyt luulen että tuli ensimmäinen yhtymäkohta react -ohjelmointiin. Voisiko tämä selittää miksi Reactissa props (=args?) -parametri aina välitetään olentona? Katsotaan tätä kirjan esimerkkiä:

    function foo( {x,y} = {} ) {
        console.log( x, y );
    }

    foo( {
        y: 3
    } );                    // undefined 3

Javascriptista on parametrien nimeäminen muttei argumenttien nimeämistä. Jos funktio on jotain näistä, ylläolevan tuloksen saavuttaminen on vaikeaa ilman parametrien olentohajoittamista (parameter object destructuring):

- Funktiossa on useita parametrejä ja niillä ei ole oletusarvoa.
- Funktiota saatetaan käyttää joskus vähemmällä määrällä argumentteja kuin mitä funktiolla on parametreja TAI ne voivat olla vaihtelevassa järjestyksessä

Seuraavaksi käsiteltiin funktioiden palautusarvoja. Suosituksena on aina sisältää yksi `return` -lauseke eikä käyttää sitä funktion ennenaikaiseen lopettamiseen. Funktioista voi myös palauttaa useita arvoja, joskin sitäkin tulisi välttää jos mahdollista.

    const [tila = setTila] = useState()

Tärkeintä on kuitenkin pitää *funktio aitona* (**pure function**), niin että sillä ei ole **sivuvaikutuksia** eikä se muuta arvoja funktion ulkopuolelta.

Seuraavaksi laitetaan funktioita funktioiden. Funktio joka ottaa rvoksi toisen funktion, on korkeamman tason funktio, kuten `forEach()`. Simpson on erityisen pärinöissä siitä funtkioiden ominaisuudesta, että funktiot korkeamman tason funktioissa viittaavaat pysyvästi korkeamman tason argumentteihin (**closure**), vaikka niitä kutsuttaisiin myöhemmin toisessa paikassa (**scope**).

    function whatAnimalSays(voice) {
    return function makeVoice(){
        console.log(voice);
    };
    }

    var giraffe = whatAnimalSays( "Kahvilikööri" );

    giraffe();                 // Kahvilikööri
    giraffe();                 // Kahvilikööri    


# Lähteet
Alickovic, Alan 2024: Bulletproof React. https://github.com/alan2207/bulletproof-react
Hemanth, H.M. 2015: Functional Programming Jargon. https://github.com/hemanth/functional-programming-jargon
Simpson, Kyle 2017: Functional-Light JavaScript: Balanced, Pragmatic FP in JavaScript. https://github.com/getify/Functional-Light-JS?tab=readme-ov-file