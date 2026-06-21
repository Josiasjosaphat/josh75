# TradingView MCP Bridge

Assistant IA personnel pour vos graphiques TradingView Desktop. Connecte Claude Code à votre application TradingView locale via le Chrome DevTools Protocol pour de l'analyse de graphiques assistée par IA, du développement Pine Script et de l'automatisation de workflow.

> [!WARNING]
> **Cet outil n'est ni affilié, ni approuvé, ni associé à TradingView Inc.** Il interagit avec votre application TradingView Desktop tournant localement via le Chrome DevTools Protocol. Lisez la section [Avertissement](#avertissement) avant utilisation.

> [!IMPORTANT]
> **Un abonnement TradingView valide est requis.** Cet outil ne contourne ni ne dépasse aucune restriction d'accès ou paywall de TradingView. Il lit et contrôle l'application TradingView Desktop déjà en cours d'exécution sur votre machine.

> [!NOTE]
> **Tout le traitement des données se fait localement sur votre machine.** Aucune donnée TradingView n'est transmise, stockée ou redistribuée par cet outil.

> [!CAUTION]
> Cet outil accède à des API internes non documentées de TradingView via l'interface de débogage Electron. Celles-ci peuvent changer ou casser sans préavis lors d'une mise à jour de TradingView. Verrouillez votre version de TradingView Desktop si la stabilité est importante pour vous.

## Comment ça marche (et pourquoi c'est sûr à utiliser)

Cet outil ne se connecte pas aux serveurs de TradingView, ne modifie aucun fichier TradingView et n'intercepte aucun trafic réseau. Il communique exclusivement avec votre instance TradingView Desktop tournant localement via le Chrome DevTools Protocol (CDP) — une interface de débogage standard intégrée à toutes les applications Chromium/Electron par Google, dont VS Code, Slack et Discord.

Le port de débogage est désactivé par défaut et doit être explicitement activé par vous via le flag Chromium standard (`--remote-debugging-port=9222`). Rien ne se passe sans cette étape volontaire de votre part.

## Ce que cet outil ne fait pas

- Se connecter aux serveurs ou API de TradingView
- Stocker, transmettre ou redistribuer des données de marché
- Fonctionner sans abonnement TradingView valide et application Desktop installée
- Contourner un paywall ou une restriction d'accès TradingView
- Exécuter de vrais trades (interaction avec le graphique uniquement)
- Fonctionner si TradingView modifie sa structure Electron interne

## Contexte de recherche

Ce projet explore une question de recherche ouverte : **comment des agents basés sur des LLM peuvent-ils interagir avec des interfaces de trading professionnelles pour assister la prise de décision humaine ?**

Plus précisément, il étudie :

- Comment des API d'outils structurées (MCP) peuvent faire le pont entre des LLM et des applications financières de bureau avec état
- Quelles contraintes de latence, de contexte et de fiabilité émergent lorsqu'un agent opère sur des données de graphique en direct
- Comment les agents gèrent un état d'interface financière ambigu (par ex. interpréter une sortie Pine Script, lire des tableaux d'indicateurs)
- Si le langage naturel est une interface efficace pour la navigation de graphiques et le développement Pine Script
- Les modes de défaillance des agents LLM opérant dans des environnements de données en temps réel

Ce n'est pas un bot de trading. C'est une couche d'interface qui rend une application de trading lisible pour un agent LLM, permettant aux chercheurs et développeurs d'étudier la collaboration humain-IA dans les workflows financiers.

Voir [RESEARCH.md](RESEARCH.md) pour les questions ouvertes, résultats et travaux connexes.

## Prérequis

- **Application TradingView Desktop** (abonnement payant requis pour les données en temps réel)
- **Node.js 18+**
- **Claude Code** avec support MCP (pour les outils MCP) ou n'importe quel terminal (pour le CLI)
- **macOS, Windows ou Linux**

## Ce que ça fait

Donne à votre assistant IA des yeux et des mains sur votre propre graphique :

- **Développement Pine Script** — écrire, injecter, compiler, déboguer et itérer sur des scripts avec assistance IA
- **Navigation de graphique** — changer de symbole, de période, zoomer sur des dates, ajouter/retirer des indicateurs
- **Analyse visuelle** — lire les valeurs d'indicateurs, niveaux de prix et annotations de votre graphique
- **Dessin sur graphique** — lignes de tendance, lignes horizontales, rectangles, annotations texte
- **Gestion des alertes** — créer, lister et supprimer des alertes de prix
- **Pratique en mode replay** — avancer barre par barre dans l'historique, s'entraîner aux entrées/sorties
- **Captures d'écran** — capturer l'état du graphique pour analyse visuelle par l'IA
- **Layouts multi-volets** — configurer des grilles 2x2, 3x1, etc. avec différents symboles par volet
- **Surveillance de graphique** — streamer du JSONL depuis votre graphique local pour des scripts de monitoring
- **Accès CLI** — chaque outil MCP est aussi accessible en commande `tv`, compatible avec les pipes et en JSON
- **Lancement de TradingView** — détection automatique et lancement avec le mode debug, sur n'importe quelle plateforme

## Installation avec Claude Code

Collez ceci dans Claude Code et il s'occupera du reste :

> Installe le serveur MCP TradingView. Clone https://github.com/josh75/tradingview-mcp.git, exécute npm install, ajoute-le à ma config MCP dans ~/.claude/.mcp.json, et lance TradingView avec le port de debug. Puis vérifie la connexion avec tv_health_check.

Ou suivez les étapes manuelles ci-dessous.

## Démarrage rapide

### 1. Installation

```bash
git clone https://github.com/josh75/tradingview-mcp.git
cd tradingview-mcp
npm install
```

### 2. Lancer TradingView avec CDP

TradingView Desktop doit tourner avec le Chrome DevTools Protocol activé sur le port 9222.

**Mac :**
```bash
./scripts/launch_tv_debug_mac.sh
```

**Windows :**
```bash
scripts\launch_tv_debug.bat
```

**Linux :**
```bash
./scripts/launch_tv_debug_linux.sh
```

**Ou lancement manuel sur n'importe quelle plateforme :**
```bash
/chemin/vers/TradingView --remote-debugging-port=9222
```

**Ou via l'outil MCP** (détecte automatiquement votre installation) :
> « Utilise tv_launch pour démarrer TradingView en mode debug »

### 3. Ajouter à Claude Code

Ajoutez ceci à votre config MCP Claude Code (`~/.claude/.mcp.json` ou `.mcp.json` au niveau du projet) :

```json
{
  "mcpServers": {
    "tradingview": {
      "command": "node",
      "args": ["/chemin/vers/tradingview-mcp/src/server.js"]
    }
  }
}
```

Remplacez `/chemin/vers/tradingview-mcp` par le chemin réel sur votre machine.

> [!TIP]
> Si le fichier de config existe déjà avec d'autres serveurs MCP, fusionnez simplement l'entrée `tradingview` dans l'objet `mcpServers` existant au lieu de l'écraser.

### 4. Vérifier

Demandez à Claude : *« Utilise tv_health_check pour vérifier que TradingView est connecté »*

Réponse attendue :
```json
{
  "success": true,
  "cdp_connected": true,
  "chart_symbol": "...",
  "api_available": true
}
```

Si `cdp_connected: false`, cela signifie que TradingView n'est pas lancé avec le flag `--remote-debugging-port=9222`.

## CLI

Chaque outil MCP est aussi accessible comme commande `tv`. Toutes les sorties sont en JSON, pratiques avec `jq`.

```bash
# Installation globale (optionnel)
npm link

# Ou exécution directe
node src/cli/index.js <commande>
```

### Exemples rapides

```bash
tv status                          # vérifier la connexion
tv quote                           # prix actuel
tv symbol AAPL                     # changer de symbole
tv ohlcv --summary                 # résumé de prix
tv screenshot -r chart             # capturer le graphique
tv pine compile                    # compiler le Pine Script
tv pane layout 2x2                 # grille de 4 graphiques
tv pane symbol 1 ES1!              # définir le symbole d'un volet
tv stream quote | jq '.close'      # surveiller les variations de prix
```

### Toutes les commandes

```
tv status / launch / state / symbol / timeframe / type / info / search
tv quote / ohlcv / values
tv data lines/labels/tables/boxes/strategy/trades/equity/depth/indicator
tv pine get/set/compile/analyze/check/save/new/open/list/errors/console
tv draw shape/list/get/remove/clear
tv alert list/create/delete
tv watchlist get/add
tv indicator add/remove/toggle/set/get
tv layout list/switch
tv pane list/layout/focus/symbol
tv tab list/new/close/switch
tv replay start/step/stop/status/autoplay/trade
tv stream quote/bars/values/lines/labels/tables/all
tv ui click/keyboard/hover/scroll/find/eval/type/panel/fullscreen/mouse
tv screenshot / discover / ui-state / range / scroll
```

## Streaming

Les commandes `tv stream` interrogent votre instance TradingView Desktop locale à intervalles réguliers via le Chrome DevTools Protocol sur localhost.

Aucune connexion n'est faite vers les serveurs de TradingView. Toutes les données restent sur votre machine.

> [!WARNING]
> La consommation programmatique de données TradingView peut être en conflit avec leurs Conditions d'Utilisation, quelle que soit la source des données. Vous êtes seul responsable de la conformité de votre usage.

```bash
tv stream quote                          # surveillance des variations de prix
tv stream bars                           # mises à jour barre par barre
tv stream values                         # surveillance des valeurs d'indicateurs
tv stream lines --filter "NY Levels"     # surveillance des niveaux de prix
tv stream tables --filter Profiler       # surveillance des données de tableaux
tv stream all                            # tous les volets à la fois (multi-symboles)
```

## Comment Claude sait quel outil utiliser

Claude lit [`CLAUDE.md`](CLAUDE.md) automatiquement lorsqu'il travaille dans ce projet. Il contient un arbre de décision complet :

| Vous dites... | Claude utilise... |
|----------------|---------------------|
| « Qu'y a-t-il sur mon graphique ? » | `chart_get_state` → `data_get_study_values` → `quote_get` |
| « Quels niveaux sont affichés ? » | `data_get_pine_lines` → `data_get_pine_labels` |
| « Lis le tableau de session » | `data_get_pine_tables` avec `study_filter` |
| « Donne-moi une analyse complète » | `quote_get` → `data_get_study_values` → `data_get_pine_lines` → `data_get_pine_labels` → `data_get_pine_tables` → `data_get_ohlcv` (résumé) → `capture_screenshot` |
| « Passe sur AAPL en daily » | `chart_set_symbol` → `chart_set_timeframe` |
| « Écris un Pine Script pour... » | `pine_set_source` → `pine_smart_compile` → `pine_get_errors` |
| « Démarre un replay au 1er mars » | `replay_start` → `replay_step` → `replay_trade` |
| « Configure une grille de 4 graphiques » | `pane_set_layout` → `pane_set_symbol` pour chaque volet |
| « Dessine un niveau à 24500 » | `draw_shape` (horizontal_line) |
| « Prends une capture d'écran » | `capture_screenshot` |

## Référence des outils (78 outils MCP)

### Lecture du graphique

| Outil | Quand l'utiliser | Taille de sortie |
|-------|-------------------|-------------------|
| `chart_get_state` | Premier appel — récupère symbole, période, noms + ID de tous les indicateurs | ~500B |
| `data_get_study_values` | Lire les valeurs actuelles RSI, MACD, BB, EMA de tous les indicateurs | ~500B |
| `quote_get` | Récupérer le dernier prix, OHLC, volume | ~200B |
| `data_get_ohlcv` | Récupérer les barres de prix. **Utiliser `summary: true`** pour des stats compactes | 500B (résumé) / 8KB (100 barres) |

### Données d'indicateurs personnalisés (dessins Pine)

Lit les sorties `line.new()`, `label.new()`, `table.new()`, `box.new()` de n'importe quel indicateur Pine visible.

| Outil | Quand l'utiliser | Taille de sortie |
|-------|-------------------|-------------------|
| `data_get_pine_lines` | Lire les niveaux de prix horizontaux (support/résistance, niveaux de session) | ~1-3KB |
| `data_get_pine_labels` | Lire les annotations texte + prix ("PDH 24550", "Bias Long") | ~2-5KB |
| `data_get_pine_tables` | Lire les tableaux de données (stats de session, dashboards analytiques) | ~1-4KB |
| `data_get_pine_boxes` | Lire les zones/plages de prix sous forme de paires {high, low} | ~1-2KB |

**Utilisez toujours `study_filter`** pour cibler un indicateur spécifique : `study_filter: "Profiler"`.

### Contrôle du graphique

| Outil | Ce qu'il fait |
|-------|----------------|
| `chart_set_symbol` | Changer de ticker (BTCUSD, AAPL, ES1!, NYMEX:CL1!) |
| `chart_set_timeframe` | Changer la résolution (1, 5, 15, 60, D, W, M) |
| `chart_set_type` | Changer le style (Candles, HeikinAshi, Line, Area, Renko) |
| `chart_manage_indicator` | Ajouter/retirer des indicateurs. **Utilisez les noms complets** : "Relative Strength Index" et non "RSI" |
| `chart_scroll_to_date` | Sauter à une date (ISO : "2025-01-15") |
| `chart_set_visible_range` | Zoomer sur une plage exacte (timestamps unix) |
| `symbol_info` / `symbol_search` | Métadonnées et recherche de symbole |
| `indicator_set_inputs` / `indicator_toggle_visibility` | Modifier les paramètres d'un indicateur, afficher/masquer |

### Layouts multi-volets

| Outil | Ce qu'il fait |
|-------|----------------|
| `pane_list` | Lister tous les volets avec symboles et état actif |
| `pane_set_layout` | Changer la grille : `s`, `2h`, `2v`, `2x2`, `4`, `6`, `8` |
| `pane_focus` | Mettre le focus sur un volet par index |
| `pane_set_symbol` | Définir le symbole sur n'importe quel volet |

### Gestion des onglets

| Outil | Ce qu'il fait |
|-------|----------------|
| `tab_list` | Lister les onglets de graphique ouverts |
| `tab_new` / `tab_close` | Ouvrir/fermer des onglets |
| `tab_switch` | Basculer vers un onglet par index |

### Développement Pine Script

| Outil | Étape |
|-------|-------|
| `pine_set_source` | 1. Injecter le code dans l'éditeur |
| `pine_smart_compile` | 2. Compiler avec auto-détection + vérification des erreurs |
| `pine_get_errors` | 3. Lire les erreurs de compilation s'il y en a |
| `pine_get_console` | 4. Lire la sortie de `log.info()` |
| `pine_save` | 5. Sauvegarder sur le cloud TradingView |
| `pine_get_source` | Lire le script actuel (**attention : peut atteindre 200KB+ pour des scripts complexes**) |
| `pine_new` | Créer un indicateur/stratégie/librairie vide |
| `pine_open` / `pine_list_scripts` | Ouvrir ou lister les scripts sauvegardés |
| `pine_analyze` | Analyse statique hors-ligne (sans graphique) |
| `pine_check` | Vérification de compilation côté serveur (sans graphique) |

### Mode Replay

| Outil | Étape |
|-------|-------|
| `replay_start` | Entrer en mode replay à une date |
| `replay_step` | Avancer d'une barre |
| `replay_autoplay` | Avancement automatique (vitesse réglable en ms) |
| `replay_trade` | Acheter/vendre/clôturer des positions |
| `replay_status` | Vérifier position, P&L, date |
| `replay_stop` | Retour au temps réel |

### Dessin, alertes, automatisation UI

| Outil | Ce qu'il fait |
|-------|----------------|
| `draw_shape` | Dessiner horizontal_line, trend_line, rectangle, text |
| `draw_list` / `draw_remove_one` / `draw_clear` | Gérer les dessins |
| `alert_create` / `alert_list` / `alert_delete` | Gérer les alertes de prix |
| `capture_screenshot` | Capture d'écran (régions : full, chart, strategy_tester) |
| `batch_run` | Exécuter une action sur plusieurs symboles/périodes |
| `watchlist_get` / `watchlist_add` | Lire/modifier la watchlist |
| `layout_list` / `layout_switch` | Gérer les layouts sauvegardés |
| `ui_open_panel` / `ui_click` / `ui_evaluate` | Automatisation UI |
| `tv_launch` / `tv_health_check` / `tv_discover` | Gestion de la connexion |

## Gestion du contexte

Les outils retournent une sortie compacte par défaut pour minimiser l'usage du contexte. Pour un workflow typique « analyse mon graphique », le contexte total est d'environ 5-10KB au lieu d'environ 80KB.

| Fonctionnalité | Comment elle économise du contexte |
|------------------|--------------------------------------|
| Lignes Pine | Retourne uniquement les niveaux de prix dédupliqués, pas chaque objet ligne |
| Labels Pine | Plafonné à 50 par étude, texte+prix uniquement |
| Tableaux Pine | Lignes pré-formatées en chaînes, sans métadonnées de cellule |
| Boîtes Pine | Uniquement les zones {high, low} dédupliquées |
| Mode résumé OHLCV | Stats + 5 dernières barres au lieu de toutes les barres |
| Inputs d'indicateurs | Blobs chiffrés/encodés auto-filtrés |
| `verbose: true` | À passer sur n'importe quel outil pine pour obtenir les données brutes avec IDs/couleurs si besoin |
| `study_filter` | Cibler un seul indicateur au lieu de tous les scanner |

## Trouver TradingView sur votre système

Les scripts de lancement et `tv_launch` détectent automatiquement TradingView. Si la détection automatique échoue :

| Plateforme | Emplacements courants |
|------------|--------------------------|
| **Mac** | `/Applications/TradingView.app/Contents/MacOS/TradingView` |
| **Windows** | `%LOCALAPPDATA%\TradingView\TradingView.exe`, `%PROGRAMFILES%\WindowsApps\TradingView*\TradingView.exe` |
| **Linux** | `/opt/TradingView/tradingview`, `~/.local/share/TradingView/TradingView`, `/snap/tradingview/current/tradingview` |

Le flag essentiel : `--remote-debugging-port=9222`

## Tests

```bash
# Nécessite TradingView lancé avec --remote-debugging-port=9222
npm test
```

29 tests couvrant : l'analyse statique Pine Script, la compilation côté serveur, et le routage CLI.

## Architecture

```
Claude Code  ←→  Serveur MCP (stdio)  ←→  CDP (port 9222)  ←→  TradingView Desktop (Electron)
```

- **Transport** : MCP sur stdio (78 outils) + CLI (commande `tv`, 30 commandes avec 66 sous-commandes)
- **Connexion** : Chrome DevTools Protocol sur localhost:9222
- **Streaming** : Boucle poll-and-diff avec déduplication, sortie JSONL vers stdout
- **Aucune dépendance** en dehors de `@modelcontextprotocol/sdk` et `chrome-remote-interface`

## Attributions

Ce projet n'est ni affilié, ni approuvé, ni associé à :
- **TradingView Inc.** — TradingView est une marque déposée de TradingView Inc.
- **Anthropic** — Claude et Claude Code sont des marques déposées d'Anthropic, PBC.

Cet outil est un serveur MCP indépendant qui se connecte à Claude Code via le protocole MCP standard. Il ne contient ni ne modifie aucun logiciel Anthropic.

## Avertissement

Ce projet est fourni **à des fins personnelles, éducatives et de recherche uniquement**.

**Comment cet outil fonctionne :** Cet outil utilise le Chrome DevTools Protocol (CDP), une interface de débogage standard intégrée à toutes les applications basées sur Chromium par Google. Il ne fait pas de rétro-ingénierie d'un protocole TradingView propriétaire, ne se connecte pas aux serveurs de TradingView, et ne contourne aucun contrôle d'accès. Le port de débogage doit être explicitement activé par l'utilisateur via un flag de ligne de commande Chromium standard (`--remote-debugging-port=9222`).

En utilisant ce logiciel, vous reconnaissez et acceptez que :

1. **Vous êtes seul responsable** de vous assurer que votre usage de cet outil respecte les [Conditions d'Utilisation de TradingView](https://www.tradingview.com/policies/) et toutes les lois applicables.
2. Les Conditions d'Utilisation de TradingView **restreignent la collecte automatisée de données, le scraping et l'usage non-display** de leur plateforme et de leurs données. Cet outil utilise le Chrome DevTools Protocol pour interagir de manière programmatique avec l'application TradingView Desktop, ce qui peut entrer en conflit avec ces conditions.
3. **Vous assumez tous les risques** liés à l'utilisation de cet outil. Les auteurs ne sont pas responsables des bannissements de compte, suspensions, actions légales ou autres conséquences résultant de son usage.
4. Cet outil **ne doit pas être utilisé** pour, entre autres :
   - Redistribuer, revendre ou exploiter commercialement les données de marché de TradingView
   - Contourner les contrôles d'accès ou restrictions d'abonnement de TradingView
   - Effectuer du trading automatisé ou de la prise de décision algorithmique à partir des données extraites
   - Violer les droits de propriété intellectuelle des auteurs d'indicateurs Pine Script
   - Se connecter aux serveurs ou à l'infrastructure de TradingView (tout l'accès se fait via l'application Desktop tournant localement)
5. La fonctionnalité de streaming surveille uniquement votre instance TradingView Desktop locale. Elle ne se connecte pas aux serveurs de TradingView et n'extrait pas de données depuis l'infrastructure de TradingView.
6. Les données de marché accessibles via cet outil restent soumises aux conditions de licence des bourses et fournisseurs de données. **Ne redistribuez pas, ne stockez pas et n'exploitez pas commercialement les données obtenues via cet outil.**
7. Cet outil accède à des interfaces internes non documentées de l'application TradingView, qui peuvent changer ou casser à tout moment sans préavis.

**Utilisation à vos propres risques.** Si vous n'êtes pas sûr que votre usage prévu respecte les conditions de TradingView, n'utilisez pas cet outil.

## Licence

MIT — voir [LICENSE](LICENSE) pour plus de détails.

La licence MIT s'applique uniquement au code source de ce projet. Elle n'accorde aucun droit sur le logiciel, les données, les marques déposées ou la propriété intellectuelle de TradingView.
