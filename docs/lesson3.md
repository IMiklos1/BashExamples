Lesson 3
===

In this lesson, we will learn new commands and how to combine them. Before we start combining commands, let's learn how to edit files in the terminal!

## nano (terminálos szövegszerkesztő)
A `nano` egyike a tömérdek terminálos szövegszerkeztőknek, amik elérhetőek szoktak lenni egy 
Unix rendszerre (ismertek még: emacs, vim). A nano egy olyan szövegszerkesztő, ami
teljesen a terminálban működik, és nem kell külön ablakot nyitnia, hogy használható legyen.
Az egér is szinte teljesen haszontalan.

A `nano` segítségével tudunk fájlokat szerkeszteni anélkül, hogy új ablakot kéne nyitnunk.
Ez abban az esetben jó, mikor csak pár sort be akarunk írni egy fájlba, vagy 
rendszergazdaként akarjuk szerkeszteni az adott fájlt.

Nyissunk egy terminált, és hozzunk létre az `szgyak` mappán belül egy `lesson3` mappát!
Lépjünk be ebbe a mappába, és nyissunk meg egy `tmp.txt` nevű fájlt a `nano` segítségével!

```bash
mkdir szgyak/lesson3
cd szgyak/lesson3
nano tmp.txt # command name and file path
```

Írjunk bele tetszőleges szöveget a fájlba! Például: "Blablabla; blablabla". A fájlt 
el tudjuk menteni a Ctrl + O billentyűkombinációval (O, mint Out). Ezt követően ki tudunk lépni
a Ctrl + X lenyomásával (X, mint eXit).

Ha ezután kiadjuk az `ls` parancsot, akkor látjuk, hogy a fájl létrejött. Ha a tartalmát is 
meg akarjuk nézni, akkor vagy megint megnyitjuk a `nano` szövegszerkesztővel, vagy pedig
csak kiíratjuk a tartalmát a terminálra. Ezt a `cat` parancs segítségével tudjuk megtenni.

```bash
cat tmp.txt # kiírja a fájl tartalmát a terminálra
```

## Parancsok kombinálása
Az Unix parancsok azt az elvet követik, hogy egy parancs **egyetlen** lényegi feladatot
hajtson végre. Ennek megfelelően, ha komplexebb feladatot akarunk végrehajtani, akkor 
valószínűleg nem fogunk olyan parancsot találni, ami megoldja nekünk azt. Helyette össze 
kell kombinálni meglévő parancsokat valamilyen módon. A parancsok kombinálására első körben
az alábbi módszereket tekintsük:

- `parancs1 && parancs2` -> ha parancs1 sikeresen végrehajtódott, akkor hajtódjon végre
parancs2 is.
- `parancs1 || parancs2` -> ha parancs1 elbukott, akkor hajtódjon végre parancs2
- `parancs1 | parancs2` -> parancs1 kimenetét parancs2 bemenetére irányítja
- `parancs1; parancs2` -> parancs1 után végrehajtja parancs2-t. Ez gyakorlatilag nem
kombinálás, mert csak kiadtunk egymás után két parancsot, annyi különbséggel, hogy 2 
sor helyett 1 sorban tettük meg.

Ezeken felül lehet még a parancs outputját valamilyen egyéb "folyamra" (stream) is irányítani.
Ez akkor hasznos például, ha fájlba szeretnénk írni, vagy csak "kukázni" akarjuk az 
outputot. A következő "irányításokat" tekintsük meg:

- `parancs > streamname` -> a parancs outputját a `streamname` nevű streamre irányítja.
Tipikus eset, hogy `streamname` egy fájl; ekkor az output felülírja a fájl tartalmát
(az eredeti tartalom törlődik).
- `parancs >> streamname` -> ugyanaz, mint az előző, csak a parancs outputja hozzáfűződik
a streamhez, nem pedig felülírja azt. Ez fájl esetén a látványos, ugyanis ilyenkor a fájl
eredeti tartalma nem törlődik, mint az előző esetben.

Vegyük észre, hogy alapból is egy streamre írunk, a *standard output* streamre (*stdout*, 1-es
azonosító). Ismert a *standard input* stream is (*stdin*, 0-s azonosító), továbbá a *standard
error* stream (*stderr*, 2-es azonosító). Ezekkel majd azután foglalkozunk, hogy megtanultunk
pár hasznos parancsot.

Az eddigiek szemléltetéséhez tanuljuk meg az `echo` parancsot (mint visszhang)!
Ez mindössze annyit csinál, hogy kiírja, amit írunk neki:

```bash
echo "Hello World!"
# kiírja, hogy Hello World! és rak egy új sor karaktert a végére
```

Tekintsük meg példákon keresztül a parancsok kombinálását!

## Példák

### 1. példa
Próbáljunk meg belépni egy `tmp` nevű jegyzékbe, és ha nem sikerül, akkor írjunk ki hibát!

```bash
cd tmp || echo "Error"
```

A fönti példában az első parancs a `cd tmp`. A két vonal jelentése, hogy ha az első
parancs elbukik, akkor hajtódjon végre a második parancs. A második parancs `echo "Error"`
Tehát ha `cd tmp` elbukik, akkor `echo "Error"` végrehajtódik, egyébként nem.

Hajtsuk végre a parancsot úgy, hogy a `tmp` mappa nem létezik, és nézzük meg az eredményt!
Ezután hozzuk létre a mappát, és adjuk ki úgy is a parancsot!

### 2. példa
Hozzuk létre a `dir_1` nevű mappát, és ha sikerül, lépjünk is bele (egyébként ne csináljunk
semmit)!

```bash
mkdir dir_1 && cd dir_1
```

A fönti az előző példához nagyon hasonló, csak itt a második parancs akkor hajtódik végre,
ha az első sikeres volt.

### 3. példa
Próbáljunk meg belépni egy mappába, és ha sikerül, akkor írjuk ki a working directory-t,
egyébként hozzuk létre a mappát! (working directory kiírása: `pwd`)

```bash
cd dir_2 && pwd || mkdir dir_2
```

A fönti parancs rövid magyarázatra szorulhat. 3 részből áll:
1. `cd dir_2` -- megpróbál belépni a mappába
2. `pwd` -- kiírja a jelenlegi working directory-t
3. `mkdir dir_2` -- létrehozza a mappát

Kétféleképpen történhez a végrehajtás, attól függően, hogy a mappába be lehet-e lépni.
1. **Az első parancs elbukik:** az && jel utáni parancs nem hajtódik végre (mert az
csak akkor hajtódik végre, ha az előtte lévő parancs sikeres), a || jel utáni viszont
igen, mert azelőtt hiba lépett fel. Tehát létrejön a directory.
2. **Az első parancs sikeresen lefut:** az && jel utáni parancs végrehajtódik, bizonyára
sikeresen, mert a `pwd` parancsnak nincs itt érdemi lehetősége elbukni. Ekkor az || jel
utáni parancs nem hajtódik végre, mert előtte nem volt hiba.

További lehetőségek lépnének fel, ha `pwd` elbukhatna.

Megjegyzendő, hogy alapesetben, ha kétszer egymás után adjuk ki a parancsot, akkor
elsőre hiba lesz, másodjára nem.

**Kérdés:** mi lesz, ha a `dir_2` mappa már létezik, de nincs rajta execute jogosultságunk?

### 4. példa
Próbáljunk meg belépni a `dir_3` mappába, és ha nem sikerül, hozzuk létre azt, majd 
lépjünk bele! Mindezt természetesen oldjuk meg egyetlen sor parancsban!

**Megoldás:**

```bash
cd dir_3 || mkdir dir_3 && cd dir_3
```

A fönti példákat tovább lehetne kombinálni, de nem érdemes. Már a három részből álló
kombinált parancsok is ritkák, ugyanis ránézésre nem a legjobban érthetők. Gyakrabban 
használjuk a stream-ek (folyamok) átvezetését egyikből másikba. Nézzünk erre is néhány
példát!

### 5. példa
Hozzunk létre egy fájlt xyz.txt néven, és írjuk bele `echo` segítségével a nevünket!

```bash
touch xyz.txt
echo "Johnny Bravo" > xyz.txt
```

Megjegyzendő, hogy a `touch` valójában felesleges, mert a második parancsnál, ha nem 
létezik a fájl, akkor a stream irányítás létrehozza.

```bash
echo "Johnny Bravo" > xyz.txt # Ha xyz.txt nem létezik,
                              # akkor itt létrejön
```

A fájl tartalmát megnézhetjük `nano` vagy `cat` segítségével is.

### 6. példa
Készítsünk egy `www.txt` nevű fájlt, és írjuk bele `echo` segítségével a nevünket 
és a neptun kódunkat! Ezután másoljuk be ennek a fájlnak a tartalmát az `xyz.txt`
fájl végére!

```bash
echo "Donald Duck" > www.txt
echo "QUACK1" >> www.txt
cat www.txt >> xyz.txt
# Nézzük is meg az xyz.txt tartalmát:
cat xyz.txt
```

A fönti parancsoknál elismételendő, hogy `>` kitörli a fájl eredeti tartalmát,
`>>` pedig hozzáír a fájl eddigi tartalmához.

### 7. példa
Kérdezzük le a jelenlegi mappa tartalmát, és írjuk bele a visszaadott szöveget
az `content.txt` fájlba!

```bash
ls > content.txt
```

Tanulság: bármely parancs outputját bele lehet irányítani egy fájlba.

### 8. példa
Megesik, hogy installálunk valamit a gépre, és az installálás közben olyan szövegek
íródnak ki, amikre nincs szükségünk, nem akarjuk látni őket. Szimuláljuk ezt az
`ls -la` paranccsal! Adjuk ki a parancsot, de ne jelenjen meg az outputja!
(Igen, ennek nincs sok értelme, de nem tanultunk még olyan parancsot, aminek az 
outputját van értelme ignorálni. Úgyhogy nézzük meg az output ignorálást, és 
felejtsük el, hogy erre a parancsra biztos nem használnánk!)

```bash
ls -la > /dev/null
```

Magyarázat: a `/dev/null` egy fájl a rendszeren (ami a gyökér mappán belül lévő `dev`
mappában van). Ennek a fájlnak az a különlegessége, hogy amit beleírunk, az eltűnik.
Konkrétan arra szolgál, hogy az ignorálandó streameket beleírányítsuk. Nem fogjuk
használni semmire, de ha látjuk valahol tutorialban, akkor ne lepődjünk meg rajta!
Ez egy speciális fájl, aminek nincs és nem is lesz tartalma.

### 9. példa
Próbáljuk meg ismét létrehozni `dir_3`-at, de úgy, hogy már létezik! A hibaüzenet
ne jelenjen meg!

Azt vesszük észre, hogy a következő megoldás nem működik:

```bash
mkdir dir_3 > /dev/null
```

Itt jön a képbe az, hogy a rendszeren három standard csatornát különböztetünk meg:
- **stdin:** *Standard input* rövidítése. A felhasználó által beírt szöveg megy rá.
Azonosító száma: 0.
- **stdout:** *Standard output* rövidítése. A programok által kiírt adatok mennek rá
(`echo`, `ls`, stb.). Eddig többnyire ezt a csatornát láttuk a terminálunkon
megjelenni.
- **stderr:** *Standard error* rövidítése. Ide mennek a programok által kiírt
hibaüzenetek. Ezek is megjelennek a terminálunkon, ezért nem látjuk a különbséget
közte és az *stdout* között.

A fönti megoldás azért nem működik, mert a `>` operátor csak az *stdout* csatornát 
irányítja bele a `/dev/null` fájlba, az *stderr* csatornát nem.

Megoldás:

```bash
mkdir dir_3 2> /dev/null
```

Ebben az esetben annyi történt, hogy odaírtuk a `>` elé annak a csatornának az 
azonosítóját, amit át szeretnénk irányítani. Itt megjegyzendő, hogy a következő
két parancs teljesen ugyanazt csinálja:

```bash
ls > random_something.txt # stdout csatornát beleírányítja a fájlba
ls 1> random_something.txt # szintén az stdout-ot irányítja bele,
                           # csak ki is írtuk
```

Alapértelmezettként `>` és `>>` operátorok az 1-es (standard output) csatornát 
irányítják át.

A csatorna irányításokra később vissza fogunk térni, mikor mi magunk is írunk
hibaüzeneteket.

## grep parancs
A harmadik féle parancskombinálás a `|` jel (amit bash nyelvben "csővezeték operátor"
néven is ismernek) által lehetséges. **Ezt használják leggyakrabban.** Mint azt 
korábban olvashattuk, ez az operátor **az egyik parancs kimenetét egy másik parancs
bemenetére irányítja**. Emiatt kicsit olyan, mintha egy csővel összekötné a kettőt
(ezért a "csővezeték operátor" elnevezés).

A leggyakrabban ezt az operátort a `grep` paranccsal kombinálva használják. Ez egy 
nagyon (nagyon-nagyon) hasznos parancs, úgyhogy sokat fogunk vele foglalkozni.
Gyakorlatilag ennyit csinál: **kiválasztja a bemenetéből azokat a sorokat, 
amelyek illeszkednek egy megadott mintára**.

Nézzük meg példákon keresztül! Először hozzunk létre egy fájlt `people.csv`
néven. A tartalma a következő legyen:

```
Name;Birthdate;Phone;Skill
Robert Bob;1997.09.12.;06201975555;IT
Zsuber Driver;1988.10.11.;06304445555;Driving
Hatori Hanso;1966.01.11.;06301234555;Smithing
Rinaldo Orson;2001.05.24.;06709330000;IT
Travis Camel;1970.10.01.;06301717171;Horses
Dagobert McChips;1956.08.31.;06700001111;Cooking
Bumfolt Rupor;1967.09.11.;06201112233;Marketing
```

Ebben a fájlban fogunk keresgetni a `grep` parancs segítségével.

### 10. példa
Írassuk ki azokat a sorokat, ahol a személy skillje az IT!

```bash
cat people.csv | grep "IT"
```

A `cat` paranccsal kiolvassuk a teljes `people.csv` fájlt, és a `|` operátorral
"belevezetjük" a `grep` parancsba. Emellett megadjuk, hogy mit keresünk az 
adott sorban: "IT". A `grep` kiírja azokat a sorokat, amiben benne van a 
megadott szöveg.

### 11. példa
Írassuk ki azokat a sorokat, ahol a telefonszám 30-as.

```bash
cat people.csv | grep "0630"
```

### 12. példa
Írassuk ki azokat a sorokat, ahol a személy 2000-ben vagy utána született.

```bash
cat people.csv | grep ";2"
```

### 13. példa
Írassuk ki azokat a sorokat, ahol a *Skill* megnevezése -*ing* végződésű.

```bash
cat people.csv | grep ";*ing"
```

Ez már a következő óra anyaga (reguláris kifejezések), de a csillagról
egyéb tanulmányainkból már tudhatjuk, hogy általában mindenre illeszkedik.
Sajnos sokkal bonyolultabb kereséseket nem tudunk megvalósítani reguláris 
kifejezések nélkül. Ezeket már a következő órára hagyjuk.

### 14. példa
Készítsünk egy `IT.txt` nevű fájlt az alábbi tartalommal:

```
People at IT:
***
```

Ahol `***` helyére rakjuk be a `people.csv`-ből az IT-s emberek sorait.

```bash
echo "People at IT:" > IT.txt
cat people.csv | grep "IT" >> IT.txt
```

A fönti sorokban kombináltuk a stream átirányító operátort a csővezeték operátorral.
Külön-külön már láttuk a működésüket, ami egyben sem bonyolultabb. *Mindössze balról
jobbra kell olvasni, hogy mi történik, és akkor egyértelmű lesz.*

## Feladatok
Az alábbi feladatokat csináljuk meg önállóan!

### 1. feladat
Gyűjtsük ki egy `voda.txt` nevű fájlba a `people.csv` fájlból azokat, akik 
70-es telefonszámmal rendelkeznek.

### 2. feladat
Próbáljunk meg belépni a `dir_4` nevű mappába, és ha sikerül, hozzunk létre benne egy
`im_tired.txt` nevű fájlt. Ha nem sikerül, hozzuk létre a `dir_4` mappát!

### 3. feladat
Próbáljuk meg kilistázni a `dir_5` nevű mappa tartalmát. Ha nem sikerül, ne írjunk 
hibát, hanem hozzuk létre a `dir_5` mappát!

### 4. feladat
Hozzuk létre az alábbi fájlokat egy `pictures` nevű directory-ban:

```
1.jpg
100.jpg
200.jpg
1.png
10.png
20.png
234.jpg
10.svg
```

Listázzuk ennek a mappának a tartalmát, és minden `png` fájlt írjunk bele egy
`sprites.list` nevű fájlba! (Tipp: a korábbiakban `cat people.csv` parancsot most
az `ls pictures` parancs fogja felváltani.)

## Összefoglalás
A következő parancsokat tanultuk:
- `cat` kiírja egy fájl tartalmát a standard outputra.
- `echo` kiírja a standard outputra a paraméterként megkapott szöveget.
- `grep` kiírja a megadott mintára illeszkedő sorokat.
