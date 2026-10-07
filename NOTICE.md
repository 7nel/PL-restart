# Composants tiers

PL-restart’ est sous licence CC BY-NC 4.0. Le dossier `voix/` (voix naturelle) reprend, tels quels, les fichiers déjà utilisés par PL-lect’, avec les licences ci-dessous. Elles sont celles indiquées par les sources et n'ont pas été recoupées une à une : à vérifier avant toute redistribution.

| Composant | Auteurs | Licence | Source |
|---|---|---|---|
| Modèle `fr_FR-siwis-medium` (voix) | Projet Piper (Rhasspy) ; données SIWIS, Université d'Édimbourg | CC BY 4.0 (fiche du modèle) | <https://huggingface.co/diffusionstudio/piper-voices> |
| `piper-tts-web` (copie modifiée dans `voix/vendor/`) | Mintplex Labs, d'après `vits-web` de Diffusion Studio | MIT (d'après son `package.json`) | <https://github.com/Mintplex-Labs/piper-tts-web> |
| Phonétiseur `piper_phonemize` (`voix/wasm/`) | Diffusion Studio, d'après Piper et espeak-ng | MIT d'après le paquet ; les données espeak-ng sont, à ma connaissance, sous GPL v3 : à recouper | <https://www.npmjs.com/package/@diffusionstudio/piper-wasm> |
| `onnxruntime-web` 1.18.0 (`voix/ort/`) | Microsoft | MIT | <https://github.com/microsoft/onnxruntime> |

Modification locale : `voix/vendor/piper-tts-web.js` (nom du cache `plrestart-voix-v1`).
Polices Atkinson Hyperlegible et Lexend : SIL OFL 1.1 (dossier `fonts/`).
