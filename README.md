# mcp-a11y-demo

Cible de démonstration **contrôlée** pour
[**mcp-a11y**](https://github.com/P4ST4S/mcp-a11y) — le serveur MCP de
remédiation d'accessibilité web.

Ce dépôt ne contient qu'**une page volontairement cassée**. Il sert à exercer
de bout en bout, en conditions réelles, le pipeline de mcp-a11y :

1. **`audit_page`** détecte les violations WCAG sur l'URL publique (axe-core,
   déterministe).
2. **`fix_contrast`** + **`simple_fixes`** + **`generate_alt_text`** produisent
   des correctifs déterministes (les couleurs réinjectées par sélecteur CSS,
   l'`alt` généré par un vrai appel vision sur l'image hébergée ici).
3. **`open_pr`** ouvre une PR sur **ce dépôt** avec la page corrigée.

> ⚠️ La page [`index.html`](index.html) est **intentionnellement non
> accessible**. C'est le but. L'état « après » remédiation doit retomber à
> **zéro violation**.

## Page auditée

| | |
|---|---|
| **GitHub Pages (branche par défaut)** | https://p4st4s.github.io/mcp-a11y-demo/ |
| **Raw `index.html` (pour ré-auditer une branche de PR)** | `https://raw.githubusercontent.com/P4ST4S/mcp-a11y-demo/<branche>/index.html` |
| **Image hero (pré-requis du vrai appel vision)** | https://raw.githubusercontent.com/P4ST4S/mcp-a11y-demo/main/assets/hero.png |

Pour **vérifier la branche d'une PR**, audite l'URL `raw.githubusercontent.com`
de la branche : c'est instantané, sans le délai de rebuild de GitHub Pages.
GitHub Pages ne sert que la branche par défaut.

## Violations injectées

Exactement les cinq règles axe que mcp-a11y sait corriger — rien d'autre, pour
que l'état « après » tombe à zéro :

| Règle axe | Déclencheur dans la page |
|---|---|
| `html-has-lang` | `<html>` sans attribut `lang` |
| `document-title` | pas de `<title>` dans le `<head>` |
| `image-alt` | 2 `<img>` sans attribut `alt` |
| `color-contrast` | 3 paires texte/fond sous le ratio AA 4.5:1 — `#777777` sur `#ffffff` (4.48:1), `#5a8fd6` sur `#ffffff` (3.32:1), `#ffffff` sur `#6cb2eb` (2.28:1) |
| `label` | 2 champs de formulaire sans `<label>` associé |

## Invariants structurels (NE PAS MODIFIER)

mcp-a11y réinjecte ses correctifs **par sélecteur CSS**, pas par valeur. Sa
glue ne fonctionne que si le DOM rendu est strictement identique à la source.
Donc :

- **100 % statique, ZÉRO JavaScript** — le DOM rendu doit être identique au
  fichier source, sinon les fixes ne se remappent pas.
- **Toutes les couleurs dans un UNIQUE bloc `<style>`** dans le `<head>`, via
  des classes simples. Pas de CSS externe, pas de styles inline, pas de cascade
  complexe.

Tout ajout de JS, de CSS externe ou de styles inline **casse la réinjection**.

## Licence

[MIT](LICENSE).
