# Codice Fiscale Checker

As *Agenzia delle Entrate* mostly checks only the length and basic plausibility of a Codice Fiscale, this project goes further.

**Based on personal data (name, surname, gender, date and place of birth), it computes a confidence score that your Codice Fiscale is consistent with the physical person data.**

The goal is to help humans quickly see **where and why** a CF may be wrong, not just whether it “looks valid”.

---

## What the checker validates

Given:

- **Name**
- **Surname**
- **Gender**
- **Date of birth**
- **Codice Fiscale**

the checker performs several independent “checks”:

1. **Name + Surname block (first 6 chars)**  
   Rebuilds the 3‑letter surname code and 3‑letter name code according to the official rules and compares them with the CF.

2. **Date of birth + Gender**  
   - Extracts year, month letter and encoded day from the CF.  
   - Decodes the “+40” rule for females.  
   - Compares with the provided date and gender.

3. **Comune / place of birth code (Check 5)**  
   - Extracts positions 12–15 of the CF.  
   - Looks up the code in a table of Italian comuni and foreign country codes.  
   - Reports the resolved place or flags “not found / not born in Italy”.

4. **Control character (Check 6)**  
   - Recalculates the last letter using odd/even position weights and modulo 26.  
   - Compares with the CF and reports `PASS` / `WRONG (should be X)`.

Each check can output a **PASS/FAIL message** and a **partial score**, so you can see if a problem comes from spelling of the name, wrong date, wrong comune, or an incorrect last letter.

---

## Implementations

The same logic is provided in two environments:

### 1. Google Sheets

- Custom functions written in Apps Script (JavaScript), e.g.:
  - `CHECK_CF`
  - `CHECK_DIGIT`
- Can be used directly in cells as:
  - `=CHECK_CF(F2)`
  - `=CHECK_DIGIT(F2)`
- Ideal for quick interactive validation inside Sheets.

### 2. Excel (macOS)

Excel does not support Office Scripts as cell functions, so the checker is split into:

- **Dynamic formulas** (with `LET()`) for:
  - Name/surname encoding
  - Date of birth + gender checks

- **Office Scripts (TypeScript)** for batch checks:
  - `CHECK_CF_MAIN`  
    Reads Codice Fiscale from a column (e.g. `E2:E1000`) and fills the **Check 5** column with place‑of‑birth validation.
  - `CHECK_DIGIT`  
    Reads the same CF column and fills the **Check 6** column with control‑character validation.

To run them:

1. Open `excel/Codice_Fiscale_Checker_SAMPLE.xlsx`.
2. Go to the **Automate** tab → **All scripts**.
3. Run:
   - `CHECK_CF_MAIN` to recompute the *Check 5* column.
   - `CHECK_DIGIT` to recompute the *Check 6* column.

---

## Repository structure

```text
Codice_Fiscale_Checker/
├─ README.md
├─ .gitignore
├─ excel/
│  ├─ Codice_Fiscale_Checker_SAMPLE.xlsx
├─ gsheets/
│  ├─ apps_script/
│  │  ├─ CHECK_CF.gs
│  │  ├─ CHECK_DIGIT.gs
├─ office_scripts/
│  ├─ CHECK_CF_MAIN.ts
│  ├─ CHECK_DIGIT.ts
└─ docs/
   ├─ checks.md
