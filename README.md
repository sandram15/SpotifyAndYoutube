# Spotify and YouTube - predikcija popularnosti pesama

## Pregled projekta

Ovaj projekat predstavlja seminarski rad iz predmeta _Uvod u nauku o podacima_, čiji je cilj analiza podataka i izgradnja regresionih modela za predikciju popularnosti pesama, merene brojem stream-ova na Spotify-u (_Stream_). Rad obuhvata kompletan proces rada sa podacima, od osnovnog opisa skupa i eksplorativne analize, preko čišćenja i pretprocesiranja podataka i feature engineering-a, do kreiranja i poređenja linearnih i naprednih modela mašinskog učenja.

Korišćen je [Spotify and Youtube](https://www.kaggle.com/datasets/salvatorerastelli/spotify-and-youtube) skup podataka, preuzet sa Kaggle platforme, koji se nalazi u fajlu [Spotify_Youtube.csv](Spotify_Youtube.csv). Skup sadrži preko 20000 redova (po 10 top pesama za svakog izvođača) i 28 kolona, koje obuhvataju audio karakteristike pesme, metrike popularnosti na Spotify-u i karakteristike pratećeg YouTube videa.

Kompletan tok analize, od učitavanja sirovih podataka do finalnog poređenja modela, nalazi se u jedinstvenom fajlu [analiza_kompletna.Rmd](analiza_kompletna.Rmd), koji predstavlja glavni izvor informacija o projektu. Fajlovi u tematskim folderima ([Univarijantna analiza](Univarijantna%20analiza), [Bivarijantna analiza](Bivarijantna%20analiza), [Multivarijantna analiza](Multivarijantna%20analiza), [Ciscenje podataka](Ciscenje%20podataka), [Feature engineering](Feature%20engineering)) prikazuju kako je projekat postepeno prolazio kroz faze razvoja, dok je [analiza_kompletna.Rmd](analiza_kompletna.Rmd) njihov spoj u jedinstvenu, finalnu i najažurniju verziju analize.

## Opis podataka

U uvodnom delu analize dat je osnovni pregled skupa podataka i njegovih atributa - opisane su numeričke i kategorijske promenljive, kao i značenje i tip svakog atributa (audio karakteristike poput `Danceability`, `Energy`, `Valence`; Spotify metrike poput `Stream`; YouTube metrike poput `Views`, `Likes`, `Comments`). Kako skup sadrži dva potencijalna kandidata za ciljnu promenljivu (`Stream` i `Views`), odluka o tome koja će se koristiti doneta je tek nakon detaljne eksplorativne analize - obrazloženje izbora `Stream`-a kao ciljne promenljive dato je u posebnoj sekciji analize.

## Eksplorativna analiza podataka (EDA)

Eksplorativna analiza sprovedena je u više koraka, dokumentovanih kroz fajlove u folderima [Univarijantna analiza](Univarijantna%20analiza), [Bivarijantna analiza](Bivarijantna%20analiza) i [Multivarijantna analiza](Multivarijantna%20analiza):

- **univarijantna analiza** - raspodele numeričkih i kategorijskih promenljivih, identifikacija najpopularnijih pesama, izvođača i kanala;
- **bivarijantna analiza** - odnosi kategorijska - kategorijska, kategorijska - ciljna promenljiva i numerička - numerička promenljiva, sprovedena posebno za `Stream` i posebno za `Views` kako bi se odabrala ciljna promenljiva;
- **multivarijantna analiza** - provera multikolinearnosti prediktora (VIF), osnovni multipli regresioni modeli, dijagnostika modela i provera sinergije (interakcionih efekata) između prediktora.

Analiza uključuje deskriptivnu statistiku, statističke testove (npr. Shapiro-Wilk test normalnosti sa bootstrap pristupom) i odgovarajuće grafičke prikaze radi identifikacije obrazaca i zavisnosti u podacima.

## Čišćenje podataka

Faza čišćenja podataka, dokumentovana u folderu [Ciscenje podataka](Ciscenje%20podataka), obuhvata:

- proveru i obradu duplikata,
- detekciju anomalija (npr. u koloni `Loudness`, `Duration_ms`, audio karakteristikama),
- obradu nedostajućih vrednosti (audio karakteristike, `Views`, `Description`, `Likes`, `Comments`, `Stream`),
- podelu podataka na trening i test skup.

## Feature engineering

U folderu [Feature engineering](Feature%20engineering) kreirani su novi atributi na osnovu postojećih, sa ciljem poboljšanja moći predikcije modela:

- kodiranje izvođača (`Artist`) leave-one-out encoding-om,
- `Engagement rate` na osnovu `Likes` i `Comments`,
- `Mood kvadrant` na osnovu `Valence` i `Energy`,
- `Party index` na osnovu `Danceability` i `Energy`,
- binarna klasifikacija audio karakteristika prema Spotify pragovima,
- transformacija tonaliteta (`Key`) u kategorijsku promenljivu,
- indikator kolaboracije (prisustvo "feat." u nazivu pesme),
- kombinovana kategorija na osnovu `Licensed` i `Official_video`.

## Pretprocesiranje podataka i priprema za modele

Nakon feature engineering-a sprovedeno je pretprocesiranje neophodno za modeliranje - transformacije i skaliranje numeričkih promenljivih (uključujući logaritamsku transformaciju ciljne promenljive `Stream`, zbog njene izrazito desno asimetrične raspodele) i usklađivanje tipova podataka. Za linearne modele pesme bez pratećeg YouTube spota su izuzete iz skupa, dok su modeli zasnovani na stablima trenirani nad celim skupom, uz `Has_youtube` indikator - odluka koja je detaljno obrazložena u [analiza_kompletna.Rmd](analiza_kompletna.Rmd).

## Izbor modela

U završnoj fazi rada kreirani su i evaluirani sledeći modeli za predikciju `Stream`-a:

- **linearni modeli** - obična linearna regresija (uz stepwise selekciju prediktora), Lasso i Ridge regresija;
- **napredni modeli mašinskog učenja** - Random Forest, XGBoost i LightGBM, uz pretragu hiperparametara i k-fold unakrsnu validaciju.

Modeli su upoređeni na osnovu RMSE, MAE i R² metrika, izračunatih na log-transformisanoj skali ciljne promenljive, uz analizu važnosti prediktora za svaki od modela.

## Struktura repozitorijuma

| Fajl / folder                                        | Opis                                                                                                 |
| ---------------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| [analiza_kompletna.Rmd](analiza_kompletna.Rmd)       | **Glavni izvor informacija** - kompletna, spojena analiza od učitavanja podataka do poređenja modela |
| [Spotify_Youtube.csv](Spotify_Youtube.csv)           | Sirovi skup podataka                                                                                 |
| [Univarijantna analiza](Univarijantna%20analiza)     | Faza razvoja - univarijantna analiza promenljivih                                                    |
| [Bivarijantna analiza](Bivarijantna%20analiza)       | Faza razvoja - bivarijantna analiza nad `Stream` i `Views`                                           |
| [Multivarijantna analiza](Multivarijantna%20analiza) | Faza razvoja - multivarijantna analiza i provera multikolinearnosti                                  |
| [Ciscenje podataka](Ciscenje%20podataka)             | Faza razvoja - čišćenje podataka (duplikati, anomalije, nedostajuće vrednosti)                       |
| [Feature engineering](Feature%20engineering)         | Faza razvoja - kreiranje novih atributa                                                              |
| [Podaci_za_model](Podaci_za_model)                   | Trening i test skup pripremljeni za modeliranje                                                      |
| [Analize](Analize)                                   | Ranije, međuverzije analize nastale spajanjem prethodnih faza                                        |

Fajlovi u fazama razvoja nisu međusobno usklađivani nakon što bi njihov sadržaj bio prenet u [analiza_kompletna.Rmd](analiza_kompletna.Rmd), pa za tačan i finalan opis analize treba koristiti isključivo taj fajl.

## Autori

- Sandra Miladinović
- Katarina Dimitrijević
