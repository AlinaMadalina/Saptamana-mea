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
