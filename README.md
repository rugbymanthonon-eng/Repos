# Poker Analyzer — V0.5

Application mobile Expo / React Native pour analyser un **Texas Hold'em Bonus Poker fictif** contre la banque.

## Priorité V0.5

La V0.5 est une version de **fiabilisation**. Comme la V0.4 n'a pas encore été testée sur le téléphone réel, cette version évite d'ajouter des fonctions qui dépendraient de mesures que nous n'avons pas encore. À la place, elle renforce la chaîne critique :

**caméra → OCR multi-passe → stabilité → validation de l'état → moteur exact → probabilités / EV**.

Le principe reste : **mieux vaut ne rien afficher que calculer à partir d'une mauvaise carte**.

## Nouveautés V0.5

- OCR de chaque carte avec **2 cadrages indépendants** de la zone rang/symbole ;
- si les 2 passes ne donnent pas la même carte, la lecture est rejetée ;
- verrou temporel adaptatif :
  - **3 images consécutives à ≥ 99,5 %** pour une lecture très nette ;
  - sinon **5 images consécutives à ≥ 98,5 %** ;
- une lecture provenant d'une seule passe OCR est volontairement plafonnée sous le seuil rapide ;
- surveillance périodique des 2 cartes joueur pour **détecter automatiquement une nouvelle manche**, y compris après un fold avant le flop ;
- quand la main est terminée, l'appli continue à surveiller la prochaine paire de cartes au lieu de s'arrêter ;
- toucher une carte validée permet de **l'effacer et de forcer sa relecture** ;
- panneau **Diagnostic vision** avec :
  - dernière miniature de chaque zone ;
  - carte candidate ;
  - score ;
  - texte OCR brut ;
  - raison d'acceptation/rejet ;
  - détail des deux passes OCR ;
- capture caméra portée à une qualité légèrement supérieure ;
- cadence adaptée à l'étape de la main ;
- **4 auto-tests runtime** au démarrage. Si l'un échoue, le moteur est bloqué et aucun calcul/recommandation n'est affiché ;
- mode manuel conservé comme référence de comparaison.

## Ce qui reste exact

Le moteur mathématique n'utilise toujours **aucun Monte-Carlo après le flop** :

- flop : **1 070 190** états complets ;
- turn : **45 540** états complets ;
- river : **990** mains possibles de la banque.

Les probabilités affichées sont donc issues d'une énumération exhaustive des états possibles à partir des cartes validées.

## Préflop

La décision **FLOP ×2 / FOLD** reste volontairement désactivée. Nous ne l'activerons qu'une fois le moteur préflop complet et sa stratégie validés indépendamment.

## Build natif requis pour la caméra

ML Kit est un module natif et n'est pas inclus dans Expo Go.

```bash
npm install
npx expo prebuild
npm run android:native
```

Pour les lancements suivants :

```bash
npm run start:dev
```

Ou avec EAS :

```bash
npx eas build --profile development --platform android
```

## Tests

```bash
npm run test:logic
```

Validation exhaustive de l'évaluateur 5 cartes :

```bash
npm run validate:evaluator
```

Cette validation doit vérifier exactement les **2 598 960** mains possibles de 5 cartes et leurs fréquences officielles.

## Ce qu'il faudra mesurer au premier essai réel

La prochaine vraie étape dépendra du téléphone et de l'écran utilisés. Le panneau Diagnostic vision de la V0.5 est justement prévu pour récupérer les informations nécessaires :

- quels rangs sont mal lus ;
- quels symboles sont mal lus ;
- temps moyen d'un cycle caméra/OCR ;
- stabilité du cadrage ;
- moiré/reflets éventuels.

À partir de ces données, la V0.6 pourra remplacer ou compléter l'OCR des symboles par un détecteur spécialisé si nécessaire, sans modifier le moteur mathématique.
