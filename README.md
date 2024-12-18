# Ohjelmointiteknologiat Projekti

Tavoitteena on tutustua funktionaalisen ohjelmoinnin filosofiaan sekä tarkastella sitä suhteessa Reactin hyviin käytänteisiin. Lisäksi tavoitteena on kartuttaa ymmärrystä Reactin peruselementeistä kuten tilanhallinnasta ja koukuista.

Aloitin etsimällä lähteitä. Halusin ensin tutustua ylipäätään funktionaaliseen ohjelmointiin, joten aloitin Kyle Simpsonin (2017) kirjasta Functional-Light JavaScript: Balanced, Pragmatic FP in JavaScript.

Projektin riippuvuuden ja konfigurointitiedostot ovat [repositoriossa file](./konfiguaatioita/)

Projektin tämänhetkinen versio löytyy sivustolta https://commonearth.fi.   Verkkokauppaominaisuudet ovat salasanan takana. https://commonearth.fi/demo  
Raportin video-osa (nähtävissä haaga-helian käyttiksillä): https://haagahelia-my.sharepoint.com/:v:/g/personal/bgu135_myy_haaga-helia_fi/EUQHVCbfTSdImwYeIg2cTCUB9ebxvqdlx0p9uk4JOFzXlg?nav=eyJyZWZlcnJhbEluZm8iOnsicmVmZXJyYWxBcHAiOiJTdHJlYW1XZWJBcHAiLCJyZWZlcnJhbFZpZXciOiJTaGFyZURpYWxvZy1MaW5rIiwicmVmZXJyYWxBcHBQbGF0Zm9ybSI6IldlYiIsInJlZmVycmFsTW9kZSI6InZpZXcifX0%3D&e=j1Yhew  


**Table of Contents**

-   [Funktionaalisen ohjelmoinnin perusteita](#funktionaalisen-ohjelmoinnin-perusteita)
-   [Tapausesimerkki laadukkaasta React -ohjelmoinnista](#tapausesimerkki-laadukkaasta-react--ohjelmoinnista)
-   [Reaktin säännöt](#reaktin-säännöt)
-   [Webstore -projektin refaktorointi](#webstore--projektin-refaktorointi)

## Funktionaalisen ohjelmoinnin perusteita

Funktionaalinen ohjelmointi pyrkii koodin luettavuuteen eli siihen, että niin kehittäjä kuin muutkin voivat ymmärtää ja siten myös luottaa koodiin. Ohjelmointifilosofian luvataan myös tuottavan vähemmän bugeja. Tässä oleellisena osana on asioiden hyvä nimeäminen - kun näet jonkin funktion, esimerkiksi `map()`, tiedät mitä se tekee, mutta kun näet sanan `for`, joudut usein lukemaan mitä se oikeastaan tekee. Koodissa tämä näkyy myös deklaratiivisuuden lisääntymisenä, kun ohjelman osat kuvaavat lopputulosta, eivätkä prosessia.(Simpson 2017, ch1).

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
foo.length; // arity is 3 (käännöstä ei löytynyt)
foo(x, y, z); // argumentit
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

Näin funktion määrittelyssä tulee selville, mitä funktio tekee (Simpson 2017). Seuraavaksi Simpson esittelee parametrien purkamista tämä myös selittää reactin props -parametrin muotoa, sillä se aina välitetään olentona.

```ts
function foo({ x, y } = {}) {
    console.log(x, y);
}

foo({
    y: 3,
}); // undefined 3

//Vertaa:
function foo(args) {
    console.log(args[0], args[1]);
}
```

Javascriptista on parametrien nimeäminen muttei argumenttien nimeämistä. Parametrit kannattaa hajoittaa seuraaissa tilanteissa (**parameter object destructuring**) (Simpson 2017):

-   Funktiossa on useita parametrejä ja niillä ei ole oletusarvoja.
-   Funktiota saatetaan käyttää joskus vähemmällä määrällä argumentteja kuin mitä funktiolla on parametreja TAI ne voivat olla vaihtelevassa järjestyksessä

Seuraavaksi käsiteltiin funktioiden palautusarvoja. Suosituksena on aina sisältää yksi `return` -lauseke eikä käyttää sitä funktion ennenaikaiseen lopettamiseen. Funktioista voi myös palauttaa useita arvoja, joskin sitäkin tulisi välttää jos mahdollista. (Simpson 2017).

```ts
// Tässä esimerkki reactista, jossa on jouduttu palauttamaan kaksi arvoa.
const [tila = setTila] = useState();
```

Tärkeintä on kuitenkin pitää _funktio aitona_ (**pure function**), niin että sillä ei ole **sivuvaikutuksia** eikä se muuta arvoja funktion ulkopuolelta. Myös esimerkiksi `this` -käyttö nojaa implisiittisiin parametreihin, joten sitäkin olisi hyvä välttää. (Simpson 2017).

Funktio joka ottaa arvoksi toisen funktion, on korkeamman tason funktio, kuten `forEach()`. Simpson on erityisen pärinöissä siitä funtkioiden ominaisuudesta, että funktiot korkeamman tason funktioissa viittaavaat pysyvästi korkeamman tason argumentteihin (**closure**), vaikka niitä kutsuttaisiin myöhemmin toisessa paikassa (**scope**). (Simpson 2017).

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

Tämä on Simpsonin mukaan tyypillistä funktionaalisessa ohjelmoinnissa. Huomaa myös, että funktiolle sisällä on määritelty nimi `makeVoice()`, vaikkei sitä käytetä missään. Tämä kannattaa lähes aina, sillä se auttaa virhelogien läpikäymisessä. Simpson itse siksi välttääkin => -notaatiota, sillä se ei mahdollista funktioiden nimeämistä. Alla olevassa esimerkissä funktiolla kuitenkin on nimi, koska se sijoitetaan suoraan muuttujaan (**name inferencing**). (Simpson 2017).

```ts
const addExclamation = (str) => str + "!";
console.log(addExclamation.name); // addExclamation
```

Seuraavaksi otan tauon Simpsonin kirjasta, joka sisältää myös paljon työkaluja niille, jotka eivät pidä itse
funktionaalisesta ohjelmoinnista, mutta haluavat käyttää funktioita hyvin.

## Tapausesimerkki laadukkaasta React -ohjelmoinnista

Tutustun välissä Alan Alickovicin ja kumppaneiden (2024) kirjoittamaan Bulletproof React -repositorioon, joka sisältää kattavan kokoelman React-käytänteitä pienistä yksityiskohdista skaalautuvaan infrastruktuuriin. Repositoriolla on 28 tuhatta tykkäystä ja useampi yhteiskirjoittaja.

React projektissa on hyvä pitää huoli hyvistä kirjoitusstandardeista (Alickovic ym. 2024), sillä kuten Simpsonkin totesi, funktionaalisessa ohjelmoinnissakin pyritään hyvään kommunikaatioon (Simpson 2017, ch1). Tämän mahdollistaa projektikohtaiset asetustiedostot root -kansiossa (Alickovic 2024):

-   .eslintrc konfiguroi syntaksivirheiden korjaamisen ja siihen kannattaa lisätä myös nimeämiskäytänteet
-   .prettierrc säännönmukaistaa tyylittelyt
-   tsconfig.json poistaa javascriptin dynaamisuuden tuomat haasteet. Lisäksi se sisältää absoluuttiset `import` polut.
-   .husky/ sisältää konfiguroinnit eri git -komennoille

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

    //Nyt nämä kaksi ovat sama asia:
    import * from "../../../../lib/api.ts";
    import * from "@/lib/api.ts"
```

Ohjelmistoprojektissa käytämme Viteä ja tähän teknologiaan nojautuen Alickovic ja kumppanit ehdottavat seuraavanlaista tiedostorakennetta projektiin (kommentteja muokattu alkuperäisestä):

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

Suurin osa koodista suositellaan pitämään features -kansiossa ominaisuuksittain. Ominaisuus voi aina sisältää omat kansionsa api, assets, components, hooks, stores, types ja utils. Kansiot kannattaa luoda tarpeen niin vaatiessa. Ominaisuuksien välisiä riippuvuukisa `import` pitäisi ehdottomasti välttää. Tätä sääntöä voi vahvistaa esimerkiksi eslintin konfiguroimisesta siten, että riippuvuudet eri komponenttien välillä kielletään. (Alickovic 2024).

![alt text](image.png)  
KUVA 1. Yhteisesti jaettu src/ sisältää omat komponenttinsa ja tyyppinsä, mutta myös ominaisuudet sisältävät omansa.

### Komponentit

Komponentit lähtevät helposti kasvamaan isoiksi, jolloin ne pitää tietenkin pilkkoa (Alickovic 2024). Propsien määräkin kannattaa olla kohtuullinen ja esimerkkiprojektissa 7 oli esitelty isoksi määräksi, joskin niistäkin neljälle oli määritelty oletusarvo.

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

React sovelluksessa kannattaa olla yksi api -tason instanssi . Alickovic ja kumppanit käyttävät repositoriossaan Axiosia, joka konfiguroi valmiiksi joukon funktioita. (Alickovic 2024). Axios.create() -funktio sulkee (**closure**), API_URL -ympäristömuuttujan, jolloin funktioita on helppo käyttää.

```ts
//src/lib/api-client.ts
export const api = Axios.create({
    baseURL: env.API_URL,
});

//src/features/teams/api
api.get("/teams");
```

## Tilojen hallinnointi

Lähtökohtaisesti kannattaa Reactissa hyödyntää **komponenttitason** tiloja `useState()` tai `useReducer()`. Se on tehokasta. Jos se ei ole mahdollista, voi tilan ylentää **ohjelmistotason** tilaksi `useContext()`. Tätä tarvitaan kahdessa tilanteessa. (Alickovic 2024):

-   tilaa tarvitaan rinnakkaisessa komponentissa (Lifting state up)
-   propsia välitetään useampia komponenttikerroksia alas (Props drilling).

Konteksti kannattaa tällöin sijoittaa mahdollisimman lähelle sitä tarvitsevia komponentteja. Tila voidaan myös ylentää palvelinvarastotilaksi (**server cache state**), eli hakea tieto palvelimelta ja tallentaa se käyttäjän paikallisiin tiedostoihin. Alickovicin repositoriossa näin tehdään, kun käyttäjä vie hiiren napin päälle, joka veisi käyttäjän sisältösivustolle. Tieto haetaan heti, tallennetaan käyttäjän tietoihin ja sitä käytetään sitten jos käyttäjä päättää painaa nappia. Lomakkeissa on lisäksi omat tilanhallintatyökalunsa, joita en nyt käsittele tässä. (Alickovic 2024).

Tarkastelin repositoriota funktionaalisen ohjelmoinnin näkövinkkelistä. Esimerkiksi valitsin komponentin, jossa voi päivittää käyttäjän profiilitietoja. Tästä esimerkistä voi tunnistaa funktionaalisen ohjelmoinnin perusmuotoja, joskin jotkin asiat eroavat Simpsonin näkemyksistä.

-   Koodin osat ovat puhtaissa funktioissa (**pure function**).
-   `useNotifications()` -funktion palautusarvo sekä `useUpdateProfile()` ja `addNotificationin` argumentit on purettu (**parameter object destructuring**)
-   Alkuperäistä user -oliota ei mutatoida
-   `updateProfileMutation()` sulkee `onSuccess()` -argumentin niin, että siihen voidaan viitata myöhemmin (**closure**)

```js
import { Pen } from "lucide-react";

import { Button } from "@/components/ui/button";
import { Form, FormDrawer, Input, Textarea } from "@/components/ui/form";
import { useNotifications } from "@/components/ui/notifications";
import { useUser } from "@/lib/auth";

import { updateProfileInputSchema, useUpdateProfile } from "../api/update-profile";

export const UpdateProfile = () => {
    const user = useUser();
    const { addNotification } = useNotifications();
    const updateProfileMutation = useUpdateProfile({
        mutationConfig: {
            onSuccess: () => {
                addNotification({
                    type: "success",
                    title: "Profile Updated",
                });
            },
        },
    });

    return (
        <FormDrawer
            isDone={updateProfileMutation.isSuccess}
            triggerButton={
                <Button icon={<Pen className="size-4" />} size="sm">
                    Update Profile
                </Button>
            }
            title="Update Profile"
            submitButton={
                <Button
                    form="update-profile"
                    type="submit"
                    size="sm"
                    isLoading={updateProfileMutation.isPending}
                >
                    Submit
                </Button>
            }
        >
            <Form
                id="update-profile"
                onSubmit={(values) => {
                    updateProfileMutation.mutate({ data: values });
                }}
                options={{
                    defaultValues: {
                        firstName: user.data?.firstName ?? "",
                        lastName: user.data?.lastName ?? "",
                    },
                }}
                schema={updateProfileInputSchema}
            >
                {({ register, formState }) => (
                    <>
                        <Input
                            label="First Name"
                            error={formState.errors["firstName"]}
                            registration={register("firstName")}
                        />
                        <Input
                            label="Last Name"
                            error={formState.errors["lastName"]}
                            registration={register("lastName")}
                        />
                    </>
                )}
            </Form>
        </FormDrawer>
    );
};
```

Mukaillen Alickovic 2024. Muutama rivi poistettu tiiveyden vuoksi.

On koodissa toki eroavaisuuksiakin. Esimerkiksi funktioiden nuolinotaatiota käytetään koodissa myös niin, että funktioista tulee anonyymejä. Jos esimerkiksi laukaistaan lomakke `onSubmit()` ja funktiossa tapahtuisi virhe, näkyisi se virhelogeissa anonyymina funktiona.

## Reaktin säännöt

Reactissa on paljon erilaisia ominaisuuksia, joiden ymmärtäminen auttaa rakentamaan tehokkaan käyttöliittymän. Tässä projektissa minulla ei ole kuitenkaan aikaa tustua syvällisesit kaikkiin Reactin tarjoamiin hookkeihin, komponentteihin ja APIhin. Esittelen seuraavaksi mitä Reactin dokumentaatiolla on sanottavaa siitä, millaisia sääntöjä Reaktissa ylipäätään on. Tämän lisäksi perehdyn `useState()`ja `useEffect()` -hookeihin, joilla pärjää jo pitkälle.

Reaktin sääntöjen mukaan hookkien ja komponenttien tulee olla puhtaita funktioita, jolloin koodi on helpommin ymmärrettävissä. React on myös optimoitu siten, että **puhtaiden funktioiden käyttäminen tehostaa React -sovellusta**. (React 2024b). Dokumentaatio täydentää puhtaan funktion määritelmää Simpsonista vielä siten, että sen on oltava **idempotentti**, eli tuottaa aina sama tulos samoilla argumenteilla. React saattaa renderöidä näkymän monta kertaa, minkä vuoksi tämä on tärkeää. Reactin komponentit ja hookit eivät saa myöskään puhtaiden funktioiden tapaan tuottaa sivuvaikutuksia tai muokata funktion **scope** ulkopuolisia arvoja (React 2024b, 2024c).

Poikkeuksen tähän tuottaa ulkopuolisen järjestelmät. Jos käyttäjä päivittää tietojaan palvelimella, jonkin on muututtava, vaikka funktiot pysyisivät samana. Tähän tarkoitukseen onkin `useEffect()` -hook, jota tarvitaan usein vain jos haetaan tietoa ulkopuolisesta järjestelmästä (alias Effect). Sen palautusarvo on myös undefined, joka viittaa jo itsessään siihen, ettei kyseessä ole puhdas funktio. Komponentin renderöinti voi myös riippua siitä, klikkaako käyttäjä jotain nappia vai eikö ja silloin on käytettävä tapajhtumankäsittelijöitä kuten `handleSubmit()`.

React on deklaratiivinen järjestelmä sikäli, että sinun ei tarvitse tietää miten se tuo UI-elementit käyttäjälle näkyviin. Ohjelmoija vain määrittelee lopputulokset. React ensin renderöi eli laskee/rakentaa käyttöliittymän ja aivan viimeisenä kaikki `useEffect()` käydään läpi yksi kerrallaan. Jos `useEffect()` tulos on muuttunut, React päivittää vain ne käyttöliittymän osat, jotka muuttuvat tämän seurauksena.

Esimerkiksi alla oleva ei ole idempotentti komponentti, ja aikaa käsittelevä ominaisuus olisikin siirrettävä `useEffectin()` sisälle pois renderöinnistä (React 2024b). Jälimmäisessä koodissa on huolehdittu myös sivuvaikutusten poistamisesta: `setInterval()`, luoma olioon tuhotaan. `useEffect()` palautusfunktio onkin niin sanottu `cleanup` -funktio (React 2024b, W3schools 2024).

```js
//Ai, ai komponentti tuottaa aina eri tuloksen
function Clock() {
  const time = new Date();
  return <span>{time.toLocaleString()}</span>
}

//Hyvä hyvä!
function useTime() {
  const [time, setTime] = useState(() => new Date());
  useEffect(() => {
    const id = setInterval(() => {
      setTime(new Date()); /
    }, 1000);
    return () => clearInterval(id);
  }, []);
  return time;
}

export default function Clock() {
  const time = useTime();
  return <span>{time.toLocaleString()}</span>;
}
```

`useState()` on tärkeä, koska se informoi Reactille, että nyt on redneröitävä komponentti uudestaan. Kun hook on palauttanut jonkin arvon tai sille on annettu argumentiksi jokin arvo, sitä ei tulisi muuttaa, vaan tehdä kopio.

```js
style = useIconStyle(icon); // `style` is memoized based on `icon`
icon = { ...icon, enabled: false }; // Good: ✅ make a copy instead
style = useIconStyle(icon); // new value of `style` is calculated
```

Nopeana huomiona hookkeja ei saisi ikinä välittää propseina suoraan tai niiden sisältöä muokata, tämä estää Reactin omat optimaatiot. Ehkä tässä muuten on huomattukin että sana `use` viittaa hookiin, `hande` tapahtumankäsittelijään.

## Webstore -projektin refaktorointi

Projektin käytännöllisenä lopputuloksena kirjoitin 800 uutta riviä ja poistin 900 riviä koodia. Refaktoroin verkkokaupan koodia, jonka olimme kirjoittaneet Ohjelmointiprojekti -opintojaksolla (Petrov, Pinkkila, Puukko, Otronen, Ylanne, Majaluoma 2024).

![alt text](image-1.png)

Koitin koota joitakin tapausesimerkkejä siitä, mitä olin projektissa muuttanut. Isoin työ projektissa oli kapseloida fetch -pyynnöt yhteen api-client -funktioon. Ennen refaktorointia fetch -pyyntöjä oli joka komponentissa, joka hyödynsi rajapintaa. Alla näkyy vanha onSubmit -funktio, joka sisälsi fetch -pyynnön, jolla käyttäjän sähköposti lähetettiin palvelimelle. Siitä tuli hurjasti pienempi refaktoroinnin seurauksena.

./src/features/notificationEmail/NotificationEmailInput.tsx

```ts
//Ennen
const onSubmit = async (data: z.infer<typeof FormSchema>) => {
    const newNotificationEmail = {
        id: null,
        emailAddress: data.email,
    };
    try {
        const response = await fetch(VITE_NOTIFICATION_EMAILS, {
            method: "POST",
            headers: {
                "Content-Type": "application/json",
                "X-XSRF-TOKEN": csrfToken,
            },
            body: JSON.stringify(newNotificationEmail),
            credentials: "include",
        });

        if (!response.ok) {
            console.error("Fail when sending email");
            setAlertMessage({
                title: "Hups!",
                desc: "Sähköpostiosoitetta ei voitu lähettää. Tarkistatko sähköpostiosoitteesi ja kokeilet uudelleen.",
            });
            setAlert(true);
        } else {
            setAlertMessage({
                title: "Lähetys onnistui!",
                desc: "Ilmoitamme kun peliä on saatavilla.",
            });
            setAlert(true);

            form.reset();
            setChecked(false);
        }
    } catch (err) {
        console.error(err);
    }
};
```

```js
//Jälkeen
const handleSubmit = async ({ emailAddress }: CreateNotificationEmail) => {
    try {
        await createNotificationEmail({ emailAddress });
        setAlertMessage({
            title: `Lähetys onnistui!`,
            desc: "Ilmoitamme kun peliä on saatavilla.",
        });
        setAlert(true);
        form.reset();
        setChecked(false);
    } catch (err) {
        setAlertMessage({
            title: "Hups!",
            desc: "Sähköpostiosoitetta ei voitu lähettää. Tarkistatko sähköpostiosoitteesi ja kokeilet uudelleen.",
        });
        console.log(err);
    }
};
```

`CreateNotification` -funktio sitten kutsui itse api-client -funktioita.

./src/features//notificationEmail/api/createNotification.tsx

```ts
export default function createNotificationEmail({
    emailAddress,
}: CreateNotificationEmail): Promise<void> {
    return postNoResponse(VITE_NOTIFICATION_EMAILS, {
        body: { emailAddress },
    });
}
```

Ylläolevan funktio tuli myös uudelleennimettyä handle -tuliitteellä, koska kyseessä oli tapahtumankäsittelijä. Rajapinnassa oli erilaisia käytänteitä palauitusarvojen suhteen, joten api-clientin, piti osata käsitellä pyynnöt sekä palautusarvolla että ilman (`postNoResponse`). Tapahtumakäsittelijöiden ohjelmointi puhtaiksi funtkioiksi olikin mahdotonta, mutta voisin kuvitella, ettei se ole tarkoituskaa.

Tavoite oli myös muuttaa koodin osia, jotka kuvasivat sitä miten tehdään sellaiseksi jotka kuvaavat mitä tehdään (**deskriptiivisyys**). Alla on tästä esimerkki. Vielä jäi mysteeriksi milloin funktiot kannattaa määritellä toistensa sisään. Esimerkiksi `onEffect()` -hookkia käytettäessä, sen hyödyntämät funktiot kannattaa olla sisällä, jottei niitä rakenneta joka renderöinnissä uudestaan (riippuvuudet varmistavat tämän). Tästä huolimatta allaolevassa esimerkissä uudessa koodissa osa funktion logiikasta on otettu pois funktiosta.

```ts


//Vanha koodi
  const handleCartChange = (product: IProduct, delta: number) => {
    setBuyActionTriggered(true);
    /**
     * new cart when previous cart doesn't exits
     * delta is always 1 and negative is not possible.
     */
    if (cart === null) {
      const newCartRow: ICartRowNullId = {
        id: null,
        productQty: delta,
        rowPrice: product.price,
        productId: product.id,
      };
      const newCart = {
        id: null,
        cartPrice: product.price,
        cartRows: [newCartRow],
      };

      fetchPostCart(newCart).then(fetchGetCartSession);
      return;
    }

    /**
     * if same product is put again to the cart
     * delta can be + or -
     * if delta is + just adding
     * if delta is - then check if quantatity is higher than 1 or
     * then just reduction, otherwise removal from the line ( if
     * row.productQty === 1 or delta is lower than -1 then row will be filtered out)
     */
    if (cart.cartRows.some((row) => row.productId === product.id)) {
      let updatedCart = { ...cart };

      if (delta > 0) {
        updatedCart = {
          ...cart,
          cartPrice: cart.cartPrice + product.price,
          cartRows: cart.cartRows.map((row) => {
            if (row.productId === product.id) {
              return {
                ...row,
                productQty: row.productQty + delta,
                rowPrice: row.rowPrice + product.price,
              };
            }
            return row;
          }),
        };
      } else {
        updatedCart = {
          ...cart,
          cartPrice: cart.cartPrice + product.price * delta,
          cartRows: cart.cartRows
            .map((row) => {
              if (row.productId === product.id) {
                if (row.productQty > 1 && delta === -1) {
                  return {
                    ...row,
                    productQty: row.productQty - 1,
                    rowPrice: row.rowPrice - product.price,
                  };
                } else {
                  return null;
                }
              }
              return row;
            })
            .filter((row) => row !== null),
        };
      }

      fetchPutCart(updatedCart).then(fetchGetCartSession);
      return;
    }
```

```js
//Uusi

//Adds or substracts product quantity in cart
const modifyProductAmount = (
  cartRows: CreateCartRow[],
  product: Product,
  delta: number,
) : CreateCartRow[] => {
  const newRows = cartRows.map((row) => {
    if (row.productId === product.id && row.productQty + delta >= 0) {
      return {
        ...row,
        productQty: row.productQty + delta,
        rowPrice: row.rowPrice + delta * product.price,
      };
    } else {
      return row;
    }
  });

  return newRows.filter((row) => row.productQty > 0);
};

const handleCartChange = async (product: Product, delta: number) => {
  /**We have to inform other components taht buy action is treiggered. This allows
   * for flashing shopping cart for example
   *
   */
  setBuyActionTriggered(true);

  const originalCart = cart || {
    cartPrice: 0,
    cartRows: [],
  };
  if (cart === null) {
    await postCart(originalCart);
  }

  let cartMutation: CreateCart = { ...originalCart } as CreateCart;

  if (!cartMutation.cartRows.some((row) => row.productId === product.id)) {
    cartMutation.cartRows.push({
      productQty: 0,
      rowPrice: 0,
      productId: product.id,
    });
  }

  cartMutation = {
    ...cartMutation,
    cartPrice: originalCart.cartPrice + delta * product.price,
    cartRows: modifyProductAmount(originalCart.cartRows, product, delta),
  };

  await putCart(cartMutation);

  await getCart();
};

```

Pyrin pitämään komponentit sellaisina, että ne palauttavat aina saman arvon oletuksena. Esim alla cartIsEmpty oli siirretty tapahtumaan vasta renderöinnin jälkeen. Tässä ei tarvittu `cleanup` -funktiota.

```ts
//Vanha
export default function CartDrawer(props : CartDrawerProps) {
const { cart } = useCartContext();
const isCartEmpty = !cart || cart.cartRows.length === 0;
 return ({cartIsEmpty ? (
          <DrawerClose asChild>
            <Button>Takaisin sivustolle</Button>
          </DrawerClose>
 })
}

```

```ts
//Uusi
export default function CartDrawer(props : CartDrawerProps) {
  const { cart } = useCartContext();
useEffect (() => {
    setCartIsEmpty(!cart || cart.cartRows.length === 0);
  }, [cart])
 return ({cartIsEmpty ? (
          <DrawerClose asChild>
            <Button>Takaisin sivustolle</Button>
          </DrawerClose>
 })
}
```

Loppujen lopuksi funktionaalisen refaktorointi oli enemmän sitä, että hyödynsin Alickovicin oppeja yleisesti siitä, miten React projekti kannattaa organisoida. Mielestäni projektissamme oli aika vähän sellaisia rakenteita, jotka suoraan olisin tunnistanut funktionaalisen ohjelmoinnin vastaisiksi.

Ainoan poikkeuksen tekivät hande -tyyppiset tapahtumakäsittelijät, jotka kuitenkin olivat iso osa komponenttien funktiorepertuaaria. Nämä eivät olleet puhtaita funktioita sillä niillä oli usein sivuvaikutuksia ja ne saattoivat palauttaa eri arvoja.

Mielenkiintoni kuitenkin tätä aihetta kohtaan lisääntyi, ja vaikka päätinkin palauttaa tämän projektin nyt kurssia varten, jatkan varmaankin Simpsonin kirjaan tutustumista muulla ajalla.

# Lähteet

React 2024: Rules of React. https://react.dev/reference/rules (Luettu 23.11.2024b)  
Alickovic, Alan and many other collaborators 2024: Bulletproof React. https://github.com/alan2207/bulletproof-react (Luettu 22.11.2024)  
Hemanth, H.M. 2015: Functional Programming Jargon. https://github.com/hemanth/functional-programming-jargon (Luettu 22.11.2024)  
Simpson, Kyle 2017: Functional-Light JavaScript: Balanced, Pragmatic FP in JavaScript. https://github.com/getify/Functional-Light-JS?tab=readme-ov-file (Luettu 22.11.2024)  
Github Typicode 2024: Husky - Get started. https://typicode.github.io/husky/get-started.html (Luettu 22.11.2024)  
Eslint 2024 Find and fix problems in your JavaScript code. https://eslint.org/ (Luettu 22.11.2024)  
React 2024: Passing Data Deeply with Context. https://react.dev/learn/passing-data-deeply-with-context (Luettu 22.11.2024)  
React 2024c: Components and Hooks must be pure
. https://react.dev/learn/keeping-components-pure#side-effects-unintended-consequences (Luettu 23.11.2024)  
W3schools 2024: Window clearInterval(). https://www.w3schools.com/jsref/met_win_clearinterval.asp (Luettu 23.11.2024)
React 2024c: Reusing Logic with Custom Hooks. https://react.dev/learn/reusing-logic-with-custom-hooks#extracting-your-own-custom-hook-from-a-component (Luettu 23.11.2024)  
Mozilla 2024: EventTarget: addEventListener() method. https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener (Luettu 23.11.2024)  
Petrov, Pinkkila, Puukko, Otronen, Ylanne, Majaluoma 2024. Common earth webstore. Yksityinen repositorio.
