# Project Tracking Board — Stream Deck ESP32

> Suivi de l'avancement du projet ESPHome + LVGL pour Home Assistant.
> Mis à jour manuellement ou via Claude Code.

---

## Légende des priorités

| Symbole | Priorité |
|---------|----------|
| 🔴 | Haute |
| 🟡 | Moyenne |
| 🟢 | Basse |

---

## Kanban

### ✅ Terminé

| Tâche | Appareil | Détail |
|-------|----------|--------|
| Structure modulaire du repo | Tous | Architecture `device/`, `addon/`, `assets/`, `theme/` en place. Chaque device a son `esphome.yaml` + `package.yaml` comme point d'entrée. |
| Système de thème par substitutions | Tous | `theme/button.yaml` centralise couleurs, tailles, radius. Modification d'apparence en un seul endroit. |
| Intégration Home Assistant — capteurs | Tous | `sensors.yaml` sur chaque device : capteurs numériques (temp, position stores, AC), texte (HVAC state), binaires (scènes, appareils), templates. |
| Addon NTP / horloge | Tous | `addon/time.yaml` : sync SNTP Europe/London, script de mise à jour toutes les minutes. |
| Addon rétroéclairage | Tous | `addon/backlight.yaml` : PWM, mode économiseur d'écran avec détection de présence (P4), ALWAYS_ON (S3). |
| Addon diagnostics réseau | Tous | `addon/network.yaml` : état connexion, uptime, signal WiFi (dB → %), adresse IP exposés à HA. |
| Fonts Material Design Icons | Tous | `assets/fonts.yaml` + `assets/icons.yaml` : MDI v7.4.47, glyphes explicites (ampoule, imprimante 3D, stores, batterie, etc.). |
| UI complète multi-pages — S3 4848S040 | S3-4848S040 | 1 473 lignes LVGL : page principale, laboratoire AC (arc sliders), laboratoire chauffage, affichage temp/humidité, positions stores. |
| UI personnalisée — JC1060P470 (7") | P4-JC1060P470 | Labels français (Bureau, Fado, Chambre, Salon), toggles lumières, panneau latéral avec presets luminosité (25/50/100%). |
| UI générique fonctionnelle — JC4880P443 (8.8") | P4-JC4880P443 | Layout flex `row_wrap`, boutons standard, affichage progression imprimante + humidité. |
| UI générique fonctionnelle — JC8012P4A1 (12.1") | P4-JC8012P4A1 | Layout flex `column_wrap`, parité fonctionnelle avec JC4880, le plus grand écran. |
| Documentation CLAUDE.md | Repo | Guide complet pour Claude Code : architecture, patterns LVGL, workflow ESPHome, conventions. |
| Documentation README.md | Repo | Vue d'ensemble du projet, tableau des devices, instructions de démarrage. |

---

### 🔄 En cours

| Tâche | Priorité | Appareil | Détail |
|-------|----------|----------|--------|
| Personnalisation UI — JC4880P443 | 🟡 | P4-JC4880P443 | L'UI actuelle est un template générique en anglais (copié depuis S3). À adapter aux entités HA réelles de l'utilisateur : renommer les boutons, ajuster les entity_id, ajouter les scènes spécifiques. |
| Personnalisation UI — JC8012P4A1 | 🟡 | P4-JC8012P4A1 | Même problème que JC4880. L'UI 12.1" a le plus grand potentiel (surface d'affichage) mais est actuellement sous-exploitée avec un layout calqué sur le 8.8". Profiter de l'espace supplémentaire. |

---

### 📋 À faire

| Tâche | Priorité | Appareil | Détail |
|-------|----------|----------|--------|
| Ajouter boutons UI manquants (storage_lights, network_rack) | 🔴 | S3-4848S040 | `sensors.yaml` définit `storage_lights_button` (lignes 754-769) et `network_rack_button` (lignes 772-797) mais ces boutons n'existent pas dans `lvgl.yaml`. L'état HA est capturé mais l'UI ne les affiche pas. Ajouter les boutons correspondants sur la page principale ou une page dédiée. |
| Activer l'assistant vocal | 🟡 | P4-JC8012P4A1 / P4-JC4880P443 | `spares/voice.yaml` est présent avec wake word "Alexa" (micro wake word), ducking media player, mute pendant capture. Actuellement exclu du `package.yaml`. Tester et activer sur le device le plus adapté (microphone disponible). |
| Activer le pipeline audio (haut-parleur) | 🟡 | P4-JC8012P4A1 / P4-JC4880P443 | `spares/speaker.yaml` : I2S DAC ES8311, ADC ES7210, mixer avec ducking, resampler, séparation annonces/media. Pipeline complet mais désactivé. Dépendance avec voice.yaml. |
| Activer le stockage SD card + WebDAV | 🟢 | P4-JC8012P4A1 / P4-JC4880P443 | `spares/sdcard.yaml` : interface SDMMC 4-bit (GPIO43-42), navigateur HTTP port 81, serveur WebDAV. Utile pour stocker des ressources (images, sons). Tester la compatibilité avec les P4. |
| Corriger les typos dans les fichiers spares | 🟢 | P4-JC4880P443 / P4-JC8012P4A1 | `spares/speaker.yaml` ligne 130 : "runnning" → "running", "waekword" → "wakeword". `spares/voice.yaml` ligne 1 : "Migated" → "Migrated". Code copié depuis un vieux template. |
| Nettoyer les fonts inutilisées — S3 | 🟢 | S3-4848S040 | `assets/fonts.yaml` définit 8 fonts Roboto (tailles 15-72) mais `lvgl.yaml` n'en utilise qu'une seule (`roboto15_style`). Soit nettoyer les fonts inutilisées, soit les utiliser dans l'UI pour enrichir l'affichage. Incohérence de nommage : `roboto15_style` référence `roboto18_font`. |
| Migrer les entity_id Zigbee hex vers des noms sémantiques | 🟢 | P4-JC1060P470 | `sensors.yaml` et `lvgl.yaml` utilisent des IDs bruts type `light.0x001788010dc8ba13`. Les renommer en IDs HA lisibles si les entités ont été renommées dans HA (ex: `light.bureau_principal`). |
| Évaluer et activer le support Ethernet | 🟢 | P4-JC4880P443 / P4-JC8012P4A1 | `spares/ethernet.yaml` entièrement commenté (IP101 PHY). Si le hardware le permet, l'Ethernet offre une connexion plus stable que le WiFi pour des dashboards critiques. À tester sur les devices P4 qui ont potentiellement le PHY. |
| Optimiser l'UI 12.1" pour la grande surface | 🟡 | P4-JC8012P4A1 | Le 12.1" a le même layout que le 8.8" (414 vs 415 lignes). Avec 800x1280px disponibles, possibilité d'afficher bien plus : météo, graphiques historiques HA, widgets supplémentaires, layout multi-colonnes. |
| Ajouter un effet de transition entre les pages | 🟢 | Tous | Les changements de page via `lvgl.page.show` sont instantanés. LVGL supporte des animations de transition (fade, slide). Améliorerait l'expérience utilisateur. |
| Tester la compilation de tous les devices | 🔴 | Tous | Valider avec `esphome compile <device>/esphome.yaml` que tous les devices compilent sans erreur après les modifications récentes (PR #1 mergée). |

---

## Backlog — Idées futures

| Idée | Complexité | Détail |
|------|-----------|--------|
| Page météo dédiée | Moyenne | Intégration capteurs météo HA (OpenWeatherMap, intégration locale), affichage icônes météo MDI, prévisions 3 jours. |
| Graphiques historiques | Haute | LVGL supporte des charts (line chart, bar chart). Récupérer les statistiques HA via `homeassistant` sensors pour afficher l'historique température, consommation électrique. |
| Notifications push HA → écran | Moyenne | Utiliser les `persistent_notification` de HA ou un service MQTT pour afficher des notifications contextuelles sur l'écran. |
| Thème sombre / clair automatique | Basse | Basculer entre thème clair (jour) et sombre (nuit) selon l'heure ou une scène HA. Modifier les substitutions dynamiquement. |
| Dashboard énergie | Moyenne | Affichage consommation en temps réel (watts), production solaire si applicable, compteur journalier/mensuel. |
| Contrôle multiroom audio | Haute | Intégration avec un media player HA (Sonos, Spotify Connect) pour afficher le titre en cours et contrôler le volume depuis l'écran. |

---

## Notes techniques

- **Pas de build/test automatisé** — validation = compile ESPHome + flash + vérification HA
- **Secrets** : WiFi, API key HA dans `secrets.yaml` ESPHome hors repo (`!secret`)
- **JC1060P470** : utilise le fork p1ngb4ck d'ESPHome pour les features MIPI DSI avancées
- **JC8012P4A1** : `DEBUG` logging activé — à désactiver en production pour les performances
- **Branch de dev** : `claude/project-tracking-board-iQGAl`
