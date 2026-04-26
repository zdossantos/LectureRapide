# LectureRapide

Application de **lecture rapide** par affichage séquentiel de mots (technique RSVP — *Rapid Serial Visual Presentation*).

Importez un fichier PDF, choisissez votre vitesse de lecture, et lisez mot par mot sans bouger les yeux.

---

## Fonctionnalités

- 📄 **Import PDF** — extraction automatique du texte de n'importe quel PDF
- ⚡ **Vitesse ajustable** — de 50 à 1 000+ mots par minute
- ▶️ **Lecture / Pause / Reset** — contrôle total de la session
- 📊 **Progression** — affichage du mot courant et du temps restant estimé

## Stack technique

- [Vue 3](https://vuejs.org/)
- [Vite](https://vitejs.dev/)
- [Tailwind CSS](https://tailwindcss.com/)
- [PDF.js](https://mozilla.github.io/pdf.js/) (`pdfjs-dist`)
- [Radix Vue](https://www.radix-vue.com/)
- [Lucide Vue Next](https://lucide.dev/)

## Installation

```bash
npm install
```

## Développement

```bash
npm run dev
```

## Build de production

```bash
npm run build
```

## Prévisualiser le build

```bash
npm run preview
```

## Lint

```bash
npm run lint
```

## Déploiement

Le projet est configuré pour [Netlify](https://www.netlify.com/)
