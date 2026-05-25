# pôjdeš so mnou na rande? 🌸

Date-pozvánka — single-page, no build step

## 🪴 Krok 1 — Vytvor Google Form

Choď na **forms.google.com → blank form**. Pomenuj ho ľubovoľne (napr. "rande responses").

Pridaj **presne tieto 4 otázky v tomto poradí**:

| #  | Otázka              | Typ                  | Povinná |
|----|---------------------|----------------------|---------|
| 1  | `Dátum rande`       | **Short answer**     | áno     |
| 2  | `Čas rande`         | **Short answer**     | áno     |
| 3  | `Na čo má chuť`     | **Short answer**     | áno     |
| 4  | `Odkaz (nepovinné)` | **Paragraph**        | nie     |

⚠️ **Daj Short answer, nie Date/Time.** Google Forms je pri Date/Time veľmi prísny na formát a hidden-iframe submit by zlyhal. Short answer zoberie čokoľvek.

## 📊 Krok 2 — Prepoj na Sheets + zapni email

1. **Responses** tab → zelená Sheets ikonka → **Create new spreadsheet** → hotovo, máš Sheet.
2. **Settings** tab → sekcia **Responses** → zapni **"Get email notifications for new responses"**.
   - Email pôjde na google účet ktorý vlastní tento Form.
   - Ak chceš na iný email: založ Form z toho účtu, alebo pridaj iný účet ako collaborator a tam zapnite notifikáciu.

## 🔑 Krok 3 — Vytiahni `entry.XXX` ID-čka

1. **Send** (vpravo hore) → ikonka linku → skopíruj URL formuláru → otvor v novom okne.
2. Na živom formulári pravým klikom → **View page source** (alebo `Cmd+Option+U`).
3. `Cmd+F` → hľadaj `entry.` → uvidíš 4 unikátne čísla, napr. `entry.460455805`. Sú v tom poradí ako otázky.
4. Skopíruj URL z adresného riadku formulára (ten `https://docs.google.com/forms/d/e/.../viewform`) a v `index.html` ju nahraď za URL končiacu `/formResponse` (proste prepíš `viewform` → `formResponse`, hash za tým zmaž).

## 🔧 Krok 4 — Vlož do `index.html`

V `index.html` urob find-and-replace (5 stringov):

| Placeholder              | Nahraď za                                          |
|--------------------------|----------------------------------------------------|
| `YOUR_FORM_ACTION_URL`   | `https://docs.google.com/forms/d/e/.../formResponse` |
| `YOUR_ENTRY_DATE`        | `entry.XXXXXXX` (otázka 1)                         |
| `YOUR_ENTRY_TIME`        | `entry.XXXXXXX` (otázka 2)                         |
| `YOUR_ENTRY_FOOD`        | `entry.XXXXXXX` (otázka 3)                         |
| `YOUR_ENTRY_NOTE`        | `entry.XXXXXXX` (otázka 4)                         |

## 🎀 Krok 5 — Vlož gif-y

Stiahni 3 .gif súbory (Tenor / Giphy / kdekoľvek) a daj ich do `assets/` s presne týmito názvami:

- `assets/kitty.gif` — mačiatko s labkami (úvodný obrazovka)
- `assets/success.gif` — radostná reakcia (po stlačení áno)
- `assets/coming.gif` — niečo s autom / "comin' to get you" (záver)

Tip: na Tenor klik pravým na gif → "Save image as..." → premenuj na `kitty.gif` atď.

## 🧪 Krok 6 — Otestuj lokálne

```bash
open index.html
```

- Klikni na `Nie` (skúsi ti utiecť, na desktope hover, na mobile touch).
- Klikni `Áno` → vyber dátum + čas → vyber jedlo → submit.
- Skontroluj že:
  - v Google Sheete pribudol nový riadok,
  - prišiel email.

## 🚀 Krok 7 — Deploy na GitHub Pages

```bash
cd /Users/misak/Development/Ondrej/date
git init
git add .
git commit -m "rande app"
# vytvor repo na github.com (public alebo private)
git branch -M main
git remote add origin https://github.com/<user>/<repo>.git
git push -u origin main
```

Potom na GitHube: **Settings → Pages → Source: `main` / `(root)` → Save.**

Do ~1 minúty máš live link `https://<user>.github.io/<repo>/`. Pošli ho jej.

## 🐛 Známe pasce

- **Entry ID-čka sa zmenia** ak otázku v Forme zmažeš a znovu vytvoríš. Vždy ich grab-uj až keď máš Form finálny.
- **Submit ide cez `fetch` s `mode: 'no-cors'`** a `application/x-www-form-urlencoded` (objekt `GFORM` v `<script>` na konci `<section id="s4">`). Ak by si menil názvy polí, mení sa to tam.
- **Tenor hotlinky občas umierajú.** Ak ti gif zmizne, fallback emoji sa zobrazí automaticky — alebo si gif daj lokálne do `assets/`.
- **Test najprv na telefóne** — animácia kvetov + utekajúce No tlačidlo sú dva veci čo fungujú na desktope a vedia sa rozbiť na mobile.
