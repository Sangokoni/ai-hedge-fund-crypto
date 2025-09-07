# AI Hedge Fund Crypto

[![English](https://img.shields.io/badge/Language-English-blue)](README.md)
[![中文](https://img.shields.io/badge/语言-中文-red)](README_CN.md)
[![Français](https://img.shields.io/badge/Langue-Français-green)](README_FR.md)

Un framework de trading algorithmique de nouvelle génération qui exploite une architecture de workflow basée sur des graphiques, 
une analyse technique d'ensemble et des modèles de langage IA pour prendre des décisions de trading de cryptomonnaies basées sur les données. 
Ce système utilise un graphique acyclique dirigé (DAG) de nœuds spécialisés pour l'analyse multi-temporelle, 
permettant une génération sophistiquée de signaux grâce à des combinaisons pondérées de diverses stratégies de trading.

Au cœur du système, le framework s'appuie sur l'architecture de graphique computationnel de LangGraph pour traiter les données de marché à travers 
un pipeline de nœuds d'analyse technique. Chaque stratégie implémente une interface BaseNode qui traite simultanément les données multi-intervalles 
pour plusieurs actifs. Le framework agrège ensuite ces signaux en utilisant des mécanismes de pondération adaptatifs, 
évalue les paramètres de risque et formule des décisions de gestion de position grâce à l'analyse de modèles de langage large (LLM).

Le système se distingue par :
- **Prise de décision améliorée par l'IA** : Intégration de modèles de langage large (LLMs) pour les décisions de gestion de portefeuille, combinant signaux techniques et raisonnement sophistiqué
- **Architecture compositionnelle** : Nœuds distincts pour la récupération de données, l'exécution de stratégies, la gestion des risques et la gestion de portefeuille
- **Approche d'ensemble de signaux** : Agrégation pondérée de multiples stratégies techniques (suivi de tendance, retour à la moyenne, momentum, volatilité et arbitrage statistique)
- **Analyse multi-temporelle** : Traitement simultané sur divers intervalles de temps pour une génération de signaux plus robuste
- **Visualisation dynamique de stratégies** : Génération automatique de visualisations de graphiques computationnels pour mieux comprendre le flux de décision
- **Backtesting complet** : Évaluation robuste des performances historiques avec métriques détaillées et visualisations

## Résultats de Backtesting

Exemples de résultats de backtesting montrant comment des stratégies de trading bien conçues peuvent générer des signaux de haute qualité 
lorsqu'elles sont implémentées dans ce framework. Les performances du système sont déterminées par la qualité de vos stratégies, 
le framework permettant une implémentation efficace, un raffinement des décisions basé sur LLM et une analyse multi-temporelle :

![Résultats de Backtesting](imgs/backtest1.png)
![Résultats de Backtesting](imgs/backtest2.png)
![Performance du Portefeuille](imgs/backtest3.png)

Ces résultats démontrent la capacité du système à :
- Générer des signaux de trading rentables dans différentes conditions de marché
- Gérer efficacement les risques avec des règles de dimensionnement de position
- S'adapter aux dynamiques changeantes du marché avec une analyse multi-temporelle
- Fournir des métriques de performance détaillées pour l'évaluation des stratégies

## Architecture

Le système est construit autour d'une architecture de graphique acyclique dirigé (DAG) hautement configurable, où les données circulent à travers divers nœuds de traitement. 
Cette architecture offre une flexibilité exceptionnelle, permettant aux utilisateurs de personnaliser à la fois les stratégies disponibles et les intervalles de temps pour l'analyse sans modifier le code principal.

### Système de Workflow Basé sur des Nœuds

Au cœur du système, LangGraph est utilisé pour créer un graphique computationnel dynamique où :

1. **Nœud de Démarrage** : Initialise le workflow et prépare l'état pour le traitement
2. **Nœuds de Données** : Traitent les données de marché pour des intervalles spécifiques (par ex., 30m, 1h, 4h) 
3. **Nœuds de Stratégie** : Appliquent des algorithmes d'analyse technique aux données traitées
4. **Nœud de Gestion des Risques** : Évalue les limites de position et l'exposition
5. **Nœud de Gestion de Portefeuille** : Prend les décisions de trading finales en utilisant le raisonnement LLM

L'innovation clé est que les nœuds de données et les nœuds de stratégie sont entièrement configurables via le fichier `config.yaml`, 
vous permettant d'ajouter, supprimer ou modifier ces nœuds sans changer l'architecture principale.

![Démo Graphique 1](imgs/graph1.png)

### Intervalles de Temps Configurables

Vous pouvez spécifier plusieurs intervalles de temps pour l'analyse dans votre configuration :

```yaml
signals:
  intervals: ["5m", "15m", "30m", "1h", "4h", "1d"]
```

Le système créera dynamiquement des nœuds de traitement de données séparés pour chaque intervalle de temps, permettant aux stratégies d'analyser simultanément le comportement du marché sur plusieurs horizons temporels. 
Cette approche multi-temporelle fournit des signaux plus robustes en capturant à la fois les tendances du marché à court et long terme.

![Démo Graphique 2](imgs/graph2.png)

### Stratégies Configurables

De même, vous pouvez spécifier quelles stratégies de trading inclure :

```yaml
signals:
  strategies: ['MacdStrategy', 'RSIStrategy', 'BollingerStrategy']
```

Le système chargera et intégrera dynamiquement seulement les stratégies spécifiées dans le graphique de workflow. Chaque stratégie est implémentée comme un nœud indépendant qui :

1. Reçoit les données multi-temporelles agrégées
2. Applique ses algorithmes d'analyse technique spécifiques
3. Génère des signaux de trading avec des niveaux de confiance
4. Transmet ces signaux au nœud de gestion des risques

Cette approche modulaire vous permet d'expérimenter facilement avec différentes combinaisons de stratégies sans réécrire de code. 
Vous pouvez également créer vos propres modules de stratégie personnalisés et les ajouter à la configuration.

![Démo Graphique 3](imgs/graph3.png)

### Architecture de Flux de Données

Le flux de données complet fonctionne comme suit :

1. Le nœud de démarrage initialise l'état du workflow
2. Plusieurs nœuds d'intervalles de temps récupèrent et traitent les données en parallèle pour différents intervalles
3. Le nœud de fusion combine les données multi-temporelles en un état unifié
4. Plusieurs nœuds de stratégie analysent ces données unifiées et génèrent des signaux
5. Le nœud de gestion des risques applique le dimensionnement de position et les limites d'exposition
6. Le nœud de gestion de portefeuille prend les décisions de trading finales en utilisant tous les signaux disponibles et le raisonnement LLM

Cette architecture offre plusieurs avantages :
- **Flexibilité** : Changer les stratégies ou les intervalles de temps sans modifications de code
- **Parallélisation** : Traiter plusieurs intervalles de temps simultanément pour améliorer les performances
- **Isolation** : Maintenir la séparation des préoccupations entre différents composants
- **Extensibilité** : Ajouter de nouveaux nœuds de stratégie sans affecter les fonctionnalités existantes
- **Visualisation** : Générer des représentations visuelles du workflow pour une meilleure compréhension

## Fonctionnalités

- **Architecture Basée sur les Stratégies** : Implémenter et backtester plusieurs stratégies de trading
- **Analyse Multi-Temporelle** : Analyser simultanément plusieurs intervalles (1h, 4h, 1j, etc.)
- **Multiples Indicateurs Techniques** : Support MACD, RSI et indicateurs personnalisés
- **Backtesting Complet** : Tester les stratégies contre les données historiques
- **Gestion de Portefeuille** : Gérer les positions avec support pour les trades longs et courts
- **Métriques de Performance** : Statistiques de performance détaillées et visualisation
- **Visualisation de Stratégies** : Générer automatiquement des graphiques de workflow de stratégies

## Table des Matières
- [Configuration](#configuration)
- [Configuration Système](#configuration-système)
- [Utilisation](#utilisation)
  - [Mode Backtest](#mode-backtest)
  - [Mode en Direct](#mode-en-direct)
- [Créer des Stratégies Personnalisées](#créer-des-stratégies-personnalisées)
- [Structure du Projet](#structure-du-projet)
- [Contribution](#contribution)
- [Historique des Étoiles](#historique-des-étoiles)
- [Licence](#licence)
- [Avertissement](#avertissement)
- [Liens de Parrainage](#liens-de-parrainage)

## Configuration

### Prérequis
- Python 3.9 ou supérieur (Python 3.12 recommandé — utilisé pendant le développement)
- Compte Binance (requis pour accéder aux données de marché)

⚠️ Bien que Python 3.9+ devrait fonctionner, nous recommandons d'utiliser Python 3.12 pour une compatibilité complète avec l'environnement de développement.

### Installation

1. Cloner le dépôt :
```bash
git clone https://github.com/51bitquant/ai-hedge-fund-crypto.git
cd ai-hedge-fund-crypto
```

2. Configuration avec uv (recommandé) :
```bash
# Installer uv si vous ne l'avez pas
curl -fsSL https://install.lunarvim.org/uv.sh | sh

# Créer un environnement virtuel avec python 3.12
uv venv --python 3.12

# Activer l'environnement virtuel
# Pour macOS/Linux :
source .venv/bin/activate
# Pour Windows (PowerShell) :
.venv\Scripts\Activate.ps1

# Pour Windows (CMD) :
.venv\Scripts\activate.bat
# Installer les dépendances en utilisant pyproject.toml et le fichier de verrouillage
uv pip sync
```
Si vous avez des questions, n'hésitez pas à consulter la [documentation uv](https://docs.astral.sh/uv/getting-started/installation/)

3. Copier la configuration d'exemple :
```bash
cp config.example.yaml config.yaml
```

4. Configurer les variables d'environnement :
```bash
cp .env.example .env
```

5. Ajouter vos clés API au fichier `.env` :
```
# Clés API Binance (requises pour l'accès aux données)
BINANCE_API_KEY=votre-clé-api-binance
BINANCE_API_SECRET=votre-secret-api-binance
# Pour exécuter les LLMs hébergés par openai (gpt-4o, gpt-4o-mini, etc.)
# Obtenez votre clé API OpenAI sur https://platform.openai.com/
OPENAI_API_KEY=votre-clé-openai
GROQ_API_KEY=votre-clé-groq
OPENROUTER_API_KEY=votre-clé-openrouter
ANTHROPIC_API_KEY=votre-clé-anthropic
GOOGLE_API_KEY=votre-clé-google
```

## Configuration Système

Le système est configuré via le fichier `config.yaml`. Voici ce que signifie chaque paramètre :

```yaml
mode: backtest  # Options : backtest, live
start_date: 2025-04-20  # Date de début pour le backtesting
end_date: 2025-05-01  # Date de fin pour le backtesting
primary_interval: 1h  # Intervalle de temps principal pour l'analyse
initial_cash: 100000  # Capital de départ
margin_requirement: 0.0  # Exigences de marge pour les positions courtes
show_reasoning: false  # Afficher ou non le raisonnement de la stratégie
show_agent_graph: true  # Afficher ou non le graphique de workflow de l'agent
signals:
  intervals: ["30m", "1h", "4h"]  # Intervalles de temps à analyser
  tickers: ["BTCUSDT", "ETHUSDT"]  # Paires de trading
  strategies: ['MacdStrategy']  # Stratégies à utiliser
model:
  name: "gpt-4o-mini" # Configurez votre modèle
  provider: "openai"  # Configurez votre fournisseur LLM, supporte openai, groq, openrouter, gemini, anthropic, ollama
#  base_url: "https://api.openai.com/v1"  # Non requis, mais vous pouvez définir l'URL de base
```
**Les fournisseurs supportés incluent OpenAI, Groq, OpenRouter, Gemini, Anthropic et Ollama.** La plupart des LLMs sont compatibles avec le SDK OpenAI,
vous pouvez donc souvent changer de modèle en ajustant simplement le nom du modèle et l'URL de base pour correspondre au fournisseur désiré.

## Utilisation

### Mode Backtest

Le backtester vous permet de tester vos stratégies de trading contre des données de marché historiques.

1. Configurer vos paramètres dans `config.yaml` :
```yaml
mode: backtest
start_date: 2025-01-01
end_date: 2025-02-01
```

2. Exécuter le backtest :
```bash
uv run backtest.py
```
ou 

```bash
uv run main.py
```

Cela exécutera le backtest en utilisant les stratégies et paramètres définis dans votre fichier de configuration. Les résultats seront affichés dans la console et des graphiques de performance seront montrés.

#### Résultats de Backtesting

Le backtest génère des métriques de performance détaillées et des visualisations :

![Résultats de Backtesting](imgs/backtest1.png)
![Résultats de Backtesting](imgs/backtest2.png)
![Performance du Portefeuille](imgs/backtest3.png)

### Mode en Direct

Le système supporte deux modes de trading en direct : génération de signaux et exécution de trading réel.

#### Génération de Signaux Seulement

Pour l'analyse en temps réel et les signaux de trading sans exécuter de trades réels :

1. Configurer vos paramètres dans `config.yaml` :
```yaml
mode: live
```

2. Exécuter le script principal :
```bash
uv run main.py
```

Cela récupérera les données de marché actuelles, les fera passer par vos stratégies configurées, 
et générera des signaux de trading sans envoyer d'ordres aux exchanges.

#### Exécution de Trading Réel

Pour le trading en direct avec exécution d'ordres réels sur Binance :

1. Configurer vos paramètres dans `config.yaml` :
```yaml
mode: live
```

2. Assurez-vous que vos clés API ont les permissions de trading dans votre fichier `.env` :
```
BINANCE_API_KEY=votre-clé-api-binance-avec-permissions-trading
BINANCE_API_SECRET=votre-secret-api-binance
```

3. Exécuter le script principal :
```bash
uv run main.py
```

## Intégration de Trading Réel (Planifiée)
Le système est conçu pour supporter le trading réel via un client gateway Binance, qui peut envoyer des ordres en direct basés sur les signaux générés par les stratégies. Le module gateway étend les fonctionnalités de la bibliothèque python-binance avec des caractéristiques optimisées pour le trading algorithmique.

Bien que l'exécution complète de trading ne soit pas encore implémentée, la configuration actuelle se concentre sur la génération de signaux de trading structurés. Ces signaux peuvent être convertis plus tard en ordres réels dans le cadre d'une amélioration future.

Voici un exemple de comment exécuter l'agent et récupérer les signaux :
```python

result = Agent.run(
            primary_interval=settings.primary_interval,
            intervals=settings.signals.intervals,
            tickers=settings.signals.tickers,
            end_date=datetime.now(),
            portfolio=portfolio,
            strategies=settings.signals.strategies,
            show_reasoning=settings.show_reasoning,
            show_agent_graph=settings.show_agent_graph,
            model_name=settings.model.name,
            model_provider=settings.model.provider,
            model_base_url=settings.model.base_url
        )
print(result)
```
Exemple de sortie :
```json
{
  "decisions": {
    "BTCUSDT": {
      "action": "hold",
      "quantity": 0,
      "confidence": 20,
      "reasoning": "Les signaux sont mitigés avec des tendances baissières dominant sur les intervalles de temps plus courts. Aucune position longue détenue, et les perspectives baissières indiquent d'éviter les achats."
    },
    "ETHUSDT": {
      "action": "buy",
      "quantity": 10.87547580206634,
      "confidence": 23,
      "reasoning": "Malgré des signaux neutres avec une légère tendance haussière sur les intervalles de temps plus longs, le maximum d'actions peut être acheté. Le sentiment global du marché est incertain, mais ETH montre un potentiel de mouvement vers le haut."
    }
  }
}
```

> **IMPORTANT** : Le trading en direct implique des risques financiers réels. Commencez avec de petits montants et testez minutieusement vos stratégies en mode backtesting avant de déployer avec du capital réel.

## Créer des Stratégies Personnalisées

Créer votre propre stratégie de trading est simple avec l'architecture modulaire du système :

1. **Créer une Classe de Stratégie** :
   Créer un nouveau fichier Python dans le répertoire `src/strategies/` (par ex., `ma_strategie.py`) :

```python
from typing import Dict, Any
import json
import pandas as pd
from langchain_core.messages import HumanMessage
from src.graph import AgentState, BaseNode

class MaStrategie(BaseNode):
    def __call__(self, state: AgentState) -> Dict[str, Any]:
        """
        Implémentation de stratégie personnalisée qui traite les données de marché sur plusieurs intervalles de temps.
        """
        # Accéder aux données d'état
        data = state.get("data", {})
        data['name'] = "MaStrategie"  # Définir le nom de la stratégie pour la visualisation
        
        # Obtenir les tickers et intervalles de l'état
        tickers = data.get("tickers", [])
        intervals = data.get("intervals", [])
        
        # Initialiser le dictionnaire d'analyse pour stocker les résultats
        analyse_technique = {}
        for ticker in tickers:
            analyse_technique[ticker] = {}
        
        # Traiter chaque combinaison ticker et intervalle
        for ticker in tickers:
            for interval in intervals:
                # Accéder aux données de prix pour ce ticker et intervalle
                df = data.get(f"{ticker}_{interval.value}", pd.DataFrame())
                
                if df.empty:
                    continue
                
                # Implémenter votre analyse technique personnalisée ici
                # Exemple : Stratégie de croisement de moyennes mobiles simples
                df['sma_rapide'] = df['close'].rolling(window=10).mean()
                df['sma_lente'] = df['close'].rolling(window=30).mean()
                
                # Générer un signal basé sur votre logique de stratégie
                signal = "neutre"
                confiance = 50
                
                if df['sma_rapide'].iloc[-1] > df['sma_lente'].iloc[-1]:
                    signal = "haussier"
                    confiance = 70
                elif df['sma_rapide'].iloc[-1] < df['sma_lente'].iloc[-1]:
                    signal = "baissier"
                    confiance = 70
                
                # Stocker les résultats d'analyse
                analyse_technique[ticker][interval.value] = {
                    "signal": signal,
                    "confidence": confiance,
                    "strategy_signals": {
                        "croisement_ma_simple": {
                            "signal": signal,
                            "confidence": confiance,
                            "metrics": {
                                "sma_rapide": float(df['sma_rapide'].iloc[-1]),
                                "sma_lente": float(df['sma_lente'].iloc[-1]),
                                "prix": float(df['close'].iloc[-1])
                            }
                        }
                    }
                }
        
        # Créer un message avec les résultats d'analyse
        message = HumanMessage(
            content=json.dumps(analyse_technique),
            name="ma_strategie_agent",
        )
        
        # Mettre à jour l'état avec l'analyse
        state["data"]["analyst_signals"]["ma_strategie_agent"] = analyse_technique
        
        # Retourner l'état mis à jour
        return {
            "messages": [message],
            "data": data,
        }
```

2. **Enregistrer Votre Stratégie** :
   Ajouter votre stratégie à `src/strategies/__init__.py` :

```python
from .macd_strategy import MacdStrategy
from .rsi_strategy import RSIStrategy
from .ma_strategie import MaStrategie

__all__ = [
    "MacdStrategy",
    "RSIStrategy",
    "MaStrategie",
]
```

3. **Configurer Votre Stratégie** :
   Éditer `config.yaml` pour inclure votre stratégie :

```yaml
signals:
  intervals: ["1h", "4h"]
  tickers: ["BTCUSDT", "ETHUSDT"]
  strategies: ['MaStrategie']
```

4. **Exécuter le Système** :
   Exécuter le système pour voir votre stratégie en action :

```bash
uv run backtest.py
```

Votre stratégie sera automatiquement intégrée dans le graphique de workflow et contribuera aux décisions de trading finales. Le système générera une visualisation du workflow incluant votre nœud de stratégie.

## Structure du Projet

Le projet suit une structure modulaire et organisée :

```
ai-hedge-fund-crypto/
├── src/                       # Répertoire du code source
│   ├── agent/                 # Composants du système d'agent
│   │   ├── workflow.py        # Création et gestion de workflow
│   │   └── agent.py           # Implémentation de l'agent principal
│   ├── backtest/              # Framework de backtesting
│   │   └── backtester.py      # Implémentation du backtester
│   ├── graph/                 # Composants du graphique de workflow
│   │   ├── base_node.py       # Interface de nœud de base
│   │   ├── start_node.py      # Nœud d'initialisation de workflow
│   │   ├── data_node.py       # Nœuds de traitement de données
│   │   ├── empty_node.py      # Ne fait rien
│   │   ├── risk_management_node.py  # Gestion des risques
│   │   └── portfolio_management_node.py  # Prise de décision
│   │   └── state.py           # État de l'Agent défini
│   ├── indicators/            # Indicateurs techniques
│   │   ├── general_indicators.py # Quelques indicateurs
│   │   ├── momentum.py        # Indicateurs de momentum (RSI, etc.)
│   │   └── volatility.py      # Indicateurs de volatilité (BB, etc.)
│   ├── llm/                   # Intégration de modèles de langage
│   │   └── __init__.py        # Configuration LLM
│   ├── strategies/            # Stratégies de trading
│   │   ├── macd_strategy.py   # Stratégie basée sur MACD
│   │   ├── rsi_strategy.py    # Stratégie basée sur RSI
│   │   └── __init__.py        # Registre des stratégies
│   ├── utils/                 # Fonctions utilitaires
│   │   ├── binance_data_provider.py  # Récupération de données
│   │   └── config.py          # Gestion de configuration
│   └── gateway/               # Connectivité d'exchange
│       └── binance            # Client Binance amélioré
├── imgs/                      # Visualisations de stratégies et résultats
├── cache/                     # Cache de données pour l'efficacité
├── backtest.py                # Point d'entrée du backtest
├── main.py                    # Point d'entrée principal de l'application
├── config.yaml                # Fichier de configuration principal
├── config.example.yaml        # Configuration d'exemple
├── uv.lock                    # Fichier de verrouillage des dépendances UV
└── pyproject.toml             # Métadonnées du projet et dépendances
```

Fichiers et Répertoires Clés :

- **backtest.py** : Point d'entrée principal pour exécuter les backtests
- **main.py** : Point d'entrée principal pour exécuter l'analyse en direct
- **config.yaml** : Fichier de configuration central où vous définissez les tickers, intervalles et stratégies
- **src/strategies/** : Répertoire pour toutes les implémentations de stratégies de trading
- **src/graph/** : Composants pour construire le workflow de graphique computationnel
- **src/indicators/** : Indicateurs techniques utilisés par les stratégies
- **src/llm/** : Intégration de modèles de langage pour la prise de décision

## Contribution
1. Forker le dépôt
2. Créer une branche de fonctionnalité
3. Committer vos changements
4. Pousser vers la branche
5. Créer une Pull Request

## Historique des Étoiles
<a href="https://www.star-history.com/#51bitquant/ai-hedge-fund-crypto&Date" target="_blank" rel="noopener noreferrer">
  <picture>
    <source 
      media="(prefers-color-scheme: dark)" 
      srcset="https://api.star-history.com/svg?repos=51bitquant/ai-hedge-fund-crypto&type=Date&theme=dark" />
    <source 
      media="(prefers-color-scheme: light)" 
      srcset="https://api.star-history.com/svg?repos=51bitquant/ai-hedge-fund-crypto&type=Date" />
    <img 
      alt="Graphique d'Historique des Étoiles" 
      src="https://api.star-history.com/svg?repos=51bitquant/ai-hedge-fund-crypto&type=Date" 
      loading="lazy" />
  </picture>
</a>

## Licence

Ce projet est sous licence MIT. Voir le fichier [LICENSE](LICENSE) pour plus d'informations.

## Avertissement

Ce projet est fourni "tel quel" sans aucune garantie. Utilisez à vos propres risques.

## Liens de Parrainage

Si vous êtes intéressé par le trading de cryptomonnaies sur Binance, vous pouvez utiliser le lien de parrainage ci-dessous pour recevoir une remise sur les frais de trading :

[Lien de Parrainage Binance Futures](https://www.binance.com/futures/ref/51bitquant) - Inscrivez-vous avec ce lien pour obtenir une remise sur les frais lors du trading sur Binance Futures.