# Săptămâna mea

Aplicație web instalabilă (PWA) pentru task-uri care se repetă săptămânal.
Ce bifezi trece la finalul zilei; ce nu bifezi se mută singur pe ziua următoare.
Când ai bifat tot, lista se reia de la capăt, fiecare task pe ziua lui.

**Link:** https://alinamadalina.github.io/Saptamana-mea/

## Instalare pe telefon

1. Deschide linkul aplicației în **Chrome** (Android) sau **Safari** (iPhone).
2. Android: meniul `⋮` → *Instalează aplicația* / *Adaugă la ecranul principal*.
   iPhone: butonul *Share* → *Add to Home Screen*.
3. Pornește pe tot ecranul, fără bara browserului, și merge și fără internet.

## Ce face

| Tip de task | Cum se comportă |
|---|---|
| **pe o zi** | ține minte ziua lui din săptămână; nebifat, se mută pe ziua următoare |
| **🔒 blocat** | rămâne în ziua lui, bifat sau nu |
| **zilnic** | apare în fiecare zi, primul în lista de azi; a doua zi apare iar nebifat |
| **săptămânal** | apare în lista de azi până îl bifezi; lunea se debifează toate |
| **lunar** | la fiecare 7 zile intră în săptămână primul din listă; bifat, trece la coadă |

Când toate task-urile de pe zile sunt bifate, runda se reia: fiecare task se întoarce
pe ziua lui din săptămâna următoare. Resetarea se poate anula din bara care apare jos.

### Cititul: câte un task pe zi, care se adună

În fiecare zi apare un task **Citit**, cu numărul de pagini egal cu ziua din lună: pe 27
„Citit 27 de pagini”, pe 28 „28 de pagini”, iar pe întâi ale lunii o ia de la „o pagină”.

Dacă nu îl bifezi, **rămâne și se adună** peste cel de a doua zi. Pe 28 ai amândouă —
unul de 26 de pagini și unul de 27 — iar când sunt mai multe apare și totalul de citit.
Fiecare își păstrează numărul zilei lui: cel rămas din 27 scrie tot 27, nu 28.

Cum e făcut:

- fiecare intrare e un task obișnuit cu `t.rd` = ziua pentru care e; numărul de pagini se
  calculează din `t.rd`, nu din ziua de azi;
- fiind task-uri obișnuite, se reportează singure prin `rollover()` și se strâng în lista
  de azi exact ca orice restanță, cu eticheta „mutat din …”;
- `t.rd` le ține în afara rundei săptămânale (`isRec`), deci nu intră la socoteala
  „Runda: x din y” și nu blochează reluarea listei;
- cele bifate din zilele trecute se șterg la trecerea zilei, ca să nu se adune degeaba.

Într-un task zilnic obișnuit merg și șabloanele `{zi}` (numărul zilei) și `{pagini}`

#### Cărțile mele

Pui cărțile în ordinea în care vrei să le citești, fiecare cu numele și câte pagini are.
Toate la rând formează o singură numărătoare: pagina 1 a cărții a doua vine imediat după
ultima pagină a primei. `st.readPage` e poziția în numărătoarea asta, iar `bookAt()` o
traduce înapoi în „cartea X, pagina Y”. Așa planul rămâne un lanț simplu, indiferent câte
cărți ai pus, iar o zi poate să termine o carte și să înceapă următoarea — rândul din plan
le arată pe amândouă.

Dacă ai început prima carte, scrie în **În prima carte sunt deja la pagina**.

#### Slider pe fiecare zi

Fiecare task de citit are sub el un slider peste intervalul lui. Îl tragi și spui până
unde ai ajuns; când atingi capătul, task-ul se bifează singur și, cu „Ascunde ce am
făcut” pornit, dispare din listă. Ce n-ai atins rămâne și se adună cu ziua următoare.

Modelul s-a schimbat: **pagina e sursa adevărului**, nu bifa. Fiecare task își ține
intervalul (`t.de`, `t.pana`), fixat când e creat — capătul zilei precedente e începutul
celei noi. `syncReadTasks()` deduce bifele: un task e făcut dacă pagina curentă a trecut
de capătul lui. De-aceea merge în ambele sensuri — dacă scrii o pagină mai mică, bifele
se desfac la loc.

Bifatul de mână încă merge: mută pagina la capătul intervalului, respectiv înapoi la
început. Task-urile de citit dintr-o zi se afișează în ordinea zilelor lor, nu în ordinea
în care au fost mutate.

În timpul tragerii sliderului nu se redesenează nimic, ca să nu scape de sub deget —
abia la ridicare. La fel, căsuța „Sunt la pagina” reașază lista abia când termini de scris.

Paginile citite se țin într-un contor propriu (`st.readDone`), nu se numără din task-uri:
cele bifate din zilele trecute se șterg la trecerea zilei, deci istoricul s-ar pierde.
Contorul crește la bifare și scade la debifare, fără să poată trece sub zero.

**Am început altă carte** aduce contorul la zero și golește numărul de pagini și pagina de start. Nu umblă la
bifele existente, deci dacă debifezi apoi un task bifat înainte de resetare, contorul nu
scade sub zero — cel mult numără puțin în minus.

#### Planul de citit

Secțiunea **📅 Planul de citit**, la finalul paginii, arată zi cu zi: câte pagini ai de
citit și din ce carte. Unde ajungi azi e de unde începi mâine. Ziua de azi e marcată.

Planul merge **până pe 31 decembrie**. Dacă până atunci nu încap toate cărțile, îți spune
câte pagini rămân. Nu e nimic de resetat la anul: planul se calculează din data de azi, așa
că pe 1 ianuarie se reface singur, de la capăt, cu ce a mai rămas de citit.

Zilele care au deja task își păstrează intervalul fixat; restul se proiectează înainte.
Ultima zi se taie exact la ultima pagină, deci poate cere mai puțin decât numărul zilei.

## Ordinea pe ecran

Listele de task-uri care se repetă (zilnice, săptămânale, lunare) stau **sub** zile, nu
deasupra: le deschizi doar când adaugi sau ștergi ceva.

În săptămâna curentă, zilele se afișează **începând cu ziua de azi**, apoi mâine,
poimâine și așa mai departe; zilele deja trecute din săptămână rămân la coadă, sub o
linie care le desparte. Așa vezi ce ai de făcut imediat ce deschizi aplicația, fără să
derulezi. În alte săptămâni decât cea curentă ordinea rămâne luni–duminică.

## Sincronizare

Cardul **Sincronizare telefon și laptop**, la finalul paginii. Te conectezi cu
**același cont ca la [Croșetat](https://github.com/AlinaMadalina/Crosetat)** — același
proiect Firebase (`crosetat-270da`), aceeași parolă. Aplicațiile sunt separate și scriu
în locuri diferite: asta la `users/<uid>/apps/saptamana`, cealaltă la
`users/<uid>/state/current`. Regulile existente le acoperă pe amândouă, nu e nevoie de
nimic nou în consolă.

Cum funcționează:

- toată starea stă într-un singur document Firestore, serializată în câmpul `json`;
- salvările locale se grupează la 1,5 s, iar `onSnapshot` aduce imediat ce s-a schimbat
  pe celălalt dispozitiv;
- cache-ul persistent Firestore face ca totul să meargă offline: ce scrii fără internet
  pleacă singur la următorul semnal.

Starea care vine din cloud trece prin `SaptApp.setState`, care rulează întâi întreținerea
(`rollover`, `ensureMonthly`, `ensureWeekly`, `ensureDaily`) și abia apoi o arată. Fără
asta, o stare salvată ieri readucea bifele de ieri peste resetul zilnic care tocmai
avusese loc. Dacă aducerea la zi chiar a schimbat ceva, `sync.js` trimite înapoi
versiunea corectată, ca toate dispozitivele să arate la fel.

Un detaliu care contează: întreținerea pe care aplicația o face singură la pornire
(mutarea task-urilor restante, resetul zilnic și cel săptămânal) rulează **înainte** ca
`sync.js` să se încarce, deci nu trece prin `window.SaptSync` și nu marchează starea ca
schimbată local. Altfel un dispozitiv închis câteva zile ar părea mereu „mai nou” la
reconectare și ar suprascrie modificările reale făcute pe celălalt.

## Cum ajunge pe telefon un update făcut pe laptop

1. Modifici fișierele aici.
2. **Crești `VERSION` în [`sw.js`](sw.js)** (ex. `v1.0.0` → `v1.0.1`). Pasul ăsta e obligatoriu —
   fără el telefonul servește versiunea din cache.
3. `git add -A && git commit -m "..." && git push`
4. Workflow-ul [`.github/workflows/pages.yml`](.github/workflows/pages.yml) publică automat;
   durează 1–2 minute. La prima rulare activează singur GitHub Pages.
5. La următoarea deschidere a aplicației pe telefon:
   - dacă n-ai nimic scris într-o căsuță, se actualizează singură și se reîncarcă;
   - altfel apare bara „Versiune nouă” și se aplică la apăsare.

## Fișiere

| Fișier | Ce face |
|---|---|
| `index.html` | toată aplicația |
| `manifest.json` | numele, iconițele și modul `standalone` (instalare) |
| `sw.js` | cache offline + mecanismul de update; `VERSION` se crește la fiecare deploy |
| `sync.js` | sincronizarea peste Firestore |
| `firebase-config.js` | configurația proiectului Firebase |
| `icon-*.png` | iconițele de pe ecranul principal |

## Date

Totul se ține în `localStorage`, cheia `saptamana-mea-v1`, pe fiecare dispozitiv separat.

`index.html` expune `window.SaptApp` (`getState()`, `setState()`) și apelează
`window.SaptSync.onLocalSave(st)` la fiecare salvare — punctele prin care se poate adăuga
sincronizarea între telefon și laptop, peste același proiect Firebase folosit de
[Crosetat](https://github.com/AlinaMadalina/Crosetat).

## Dezvoltare locală

Service worker-ul are nevoie de `http://localhost`, nu merge cu `file://`.

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File scripts\serve.ps1
```

apoi deschizi http://localhost:8766/
