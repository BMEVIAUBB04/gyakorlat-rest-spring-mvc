# REST API fejlesztése Spring MVC technológiával

## Beugró kérdések
- 

A labor során egy apróhirdetési portál backendjét készítjük el, amely REST API-n képes fogadni a kéréseket
webalkalmazától, mobil apptól vagy akár vastagkliens-alkalmazástól. Kliensalkalmazást azonban most
nem készítünk, csupán a Postman programmal teszteljük a backendet.

## A feladatok

### Előkészületek

- Töltsük le, és csomagoljuk ki a projekt vázát a tantárgy weboldaláról.
- Indítsuk el az Eclipse fejlesztőkörnyezetet.
- Importáljuk a projektvázat Maven projektjént.
- A projektváz egy Spring Boot alkalmazás, amely induláskor beágyazott HSQL relációs adatbázist
indít a memóriában. Az alkalmazásban konfigurálva van a JPA, és van egy Note entitásunk,
valamint REST endpointok, hogy szöveges feljegyzéseket vigyünk fel, és tároljunk el az
adatbázisban. Ezek majd később törölhetők, egyelőre tanulási célokat szolgálnak, és a későbbi
feladathoz szolgálnak támpontul.
- Indítsuk el az alkalmazást!
- Indítsuk el a Postmant a start menüből! A laborvezetővel együtt vizsgáljuk meg a REST elvű
kommunikáció működését az adott Node entitással.

### 1. feladat

- Készítsd el a hirdetések kezeléséhez az `Ad` entitást! Tartalmazza az alábbi adatokat:
    - Generált azonosító
    - Cím
    - Leírás
    - Ár (int)
    - Létrehozás ideje – ez a backendben töltődjön ki
- Készíts egy `AdRepository` osztályt, és ebben valósítdk meg a mentés műveletét!
- Készíts egy `AdController` osztályt, és ebben a POST –kérést kezelő metódust, amellyel majd a
mentés REST hívással elvégezhető!
- A POST-kérés adja vissza a mentés utáni állapotot, a kitöltött azonosítóval és létrehozási idővel!
- Teszteld Postmannel a hirdetésfeladást!

### 2. feladat

- Készítsd el a keresés adatbázisműveletet az `AdRepository` osztályban, amely minimális
és maximális árak alapján adja vissza a találatokat.
- Készítsd el az `AdControllerben` a GET-kérést kezelő metódust! A paramétereket request-
paraméterekként vegyük át. Az árak megadása legyenopcionális.
A minimális ár alapértelmezett érteke 0, a maximálisé pedig 10 000 000.
- Teszteld Postmannel a keresést!

### 3. feladat

- A változtatást fogjuk megvalósítani, de bejelentkezés hiányában nem tudjuk, hogy ki adta fel a
hirdetést, tehát ki módosíthatja. Ezért először egy „titkos” kódot kell generálnunk a hirdetések
feladásakor. A hirdető ezt visszakapja, és ez alapján lesz majd képes később a hirdetést
módosítani.
- Vegyél fel egy új tagváltozót a kódnak az `Ad` entitásba!
- Mentéskor generálj egy hat karakter hosszú kódot, és a mentéskor ezt add vissza. Használhatod
az alábbi algoritmust:

```java
public class SecretGenerator {
    private static final char[] CHARS =
        "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789".toCharArray();
    private static final Random RND = new Random();

    public static String generate() {
        StringBuffer sb = new StringBuffer();
            for(int i = 0; i < 6; i++)
                sb.append(CHARS[RND.nextInt(CHARS.length)]);
        return sb.toString();
    }
}
```

- Módosítsd a keresést úgy, hogy az `AdControllerben` az eredményhalmaz visszaadása előtt, minden
találat kódját nullázd ki, hogy ne adjuk vissza.
- Készíts egy metódust az `AdRepository`-ban, amely a módosított hirdetést várja úgy, hogy a titkos
kód is ki van benne töltve. A metódus kérdezze le ID alapján a hirdetést, hasonlítsa össze a titkos
kódot, és csak akkor végezze el a módosítást, ha egyezik a kód. Visszatérési értékkel vagy
kivételdobással jelezheted, ha nem sikeres a módosítás.
- Az `AdController` metódusa PUT hívást fogadjon a módosított állapotot pedig a törzsben vegye át.
Ha sikeres a módosítás, akkor `OK`, ha nem jó a kód, akkor `FORBIDDEN` legyen a visszaadott HTTP
státusz!
- Teszteld Postmannel a módosítást!

### 4. feladat (önálló)

- Tegyük lehetővé, hogy a hirdetések tageket is kezelhessenek. Ezek tetszőleges szöveges címkék,
amelyek utalnak a cikk jellegére, pl. "bútor", és egy cikknek tetszőleges számú ilyen címke
megadható. Először egészítsük ki az `Ad` osztályt a megfelelő tagváltozóval, és alkalmazzuk rajta
a megfelelő annotációt.
- Vezessünk be egy `GET /api/ads/{tag}` endpointot, ahol `{tag}` helyére a megfelelő címkét lehet
írni, és csak azokat a cikkeket listázza, amelyek rendelkeznek a taggel. (Az útvonalból a tag értékét
a metódus paraméterként kapja meg, ezt `@PathVariable` annotációval kell ellátni.)
- Teszteld Postmannel a funkciót!

### 5. feladat (önálló)

- A hirdetéseknek lehessen lejáratot (dátum és idő) megadni. Ehhez először az `Ad` osztályt
egészítsük ki egy `LocalDateTime` típusú tagváltozóval. Ennek megadása ne legyen kötelező.
- A lejáratot úgy tudjuk kezelni, ha periodikusan (pl. percenként) futtatunk egy metódust,
amely átnézi, és törli az összes olyan hirdetést, amelynek a lejárati ideje korábbi, mint
a jelenlegi dátum és idő. Ehhez először az időzítő-szolgáltatásokat kell engedélyezni.
Az `Application` osztályra tegxük rá az `@EnableScheduling` annotációt.
- Valamelyik Spring komponensben készítsünk egy olyan metódust, amelyen a
`@Scheduled(fixedDelay= 6000)` annotációt. Ebben valósítsuk meg a már lejárt hirdetések törlését.
- Teszteljük a működést egy rövid lejáratú hirdetés felvitelével!