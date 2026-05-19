<!-- .slide: data-background="#1B5E20" -->

# Monitoring & Observabilité <!-- .element: class="r-fit-text" -->

<p style="color:#A5D6A7;">SRE · Logs · Metrics · Traces · Datadog · RUM</p>

---

## 🗣️ Atelier d'ouverture

<p class="fragment">👋 Le Shotgun s'ouvre. 600 étudiants se connectent en même temps.</p>
<p class="fragment">👋 Vous avez déployé il y a 10 minutes.</p>
<p class="fragment">👋 Un camarade vous dit : <em>"ça marche pas"</em>.</p>

<p class="fragment" style="margin-top:1em; font-size:1.3em;">
❓ <strong>Par où vous commencez ?</strong>
</p>

<p class="fragment" style="color:#ef5350;">Sans monitoring, vous volez à l'aveugle.</p>

---

<!-- .slide: data-background="#004D40" -->

# Partie 1 — SRE & Culture <!-- .element: class="r-fit-text" -->

<p style="color:#80CBC4;">Site Reliability Engineering</p>

---

## Qu'est-ce que le SRE ?

> *"SRE is what happens when you ask a software engineer to design an operations team."*
> — Ben Treynor Sloss, Google (2003)

<ul>
<li class="fragment">Le SRE n'est <strong>pas</strong> un Ops qui fait du code.</li>
<li class="fragment">C'est une <strong>discipline d'ingénierie</strong> appliquée à la fiabilité.</li>
<li class="fragment">Principe central : la fiabilité est une <strong>feature</strong> comme les autres — elle se mesure, se budget, se priorise.</li>
<li class="fragment">Chez Google, Leroy Merlin, BlaBlaCar... les SRE partagent <strong>la responsabilité de la prod</strong> avec les dev.</li>
</ul>

---

## SLI · SLO · SLA <!-- .slide: data-background="#E0F2F1" -->

<div style="display:grid; grid-template-columns: 1fr 1fr 1fr; gap: 1em; font-size:0.85em; margin-top:0.5em;">

<div style="background:#B2DFDB; padding:1em; border-radius:8px;">
<strong>SLI</strong><br>
<em>Service Level Indicator</em><br><br>
Une <strong>mesure</strong> concrète de la qualité de service.<br><br>
Exemples BDA :<br>
• % de requêtes HTTP 2xx<br>
• Latence P95 du checkout<br>
• Taux de génération de tickets réussie
</div>

<div style="background:#80CBC4; padding:1em; border-radius:8px;">
<strong>SLO</strong><br>
<em>Service Level Objective</em><br><br>
Un <strong>objectif interne</strong> — la cible qu'on se fixe sur un SLI.<br><br>
Exemples BDA :<br>
• 99.5% requêtes réussies sur 30j<br>
• P95 latence checkout &lt; 200ms<br>
• 100% tickets générés en &lt; 5s
</div>

<div style="background:#26A69A; color:#fff; padding:1em; border-radius:8px;">
<strong>SLA</strong><br>
<em>Service Level Agreement</em><br><br>
Un <strong>contrat juridique</strong> avec un partenaire externe — avec pénalités financières en cas de non-respect.<br><br>
Pas dans notre cas BDA, mais omniprésent en entreprise.
</div>

</div>

<p class="fragment" style="margin-top:1em; color:#00695C;">
💡 <strong>SLI</strong> = ce qu'on mesure &nbsp;·&nbsp; <strong>SLO</strong> = ce qu'on vise (interne) &nbsp;·&nbsp; <strong>SLA</strong> = ce qu'on contractualise (externe, avec pénalités)
</p>

---

## L'Error Budget <!-- .slide: data-background="#FFF8E1" -->

> Si votre SLO est **99.5%** de disponibilité sur 30 jours...

<p class="fragment">→ Vous avez droit à <strong>0.5%</strong> d'indisponibilité.</p>
<p class="fragment">→ Sur 30 jours × 24h × 60min = <strong>43 200 min</strong></p>
<p class="fragment">→ Error budget = <strong>216 minutes</strong> de panne tolérée par mois.</p>

<div class="fragment" style="margin-top:1em; display:grid; grid-template-columns:1fr 1fr; gap:1em; font-size:0.85em;">
<div style="background:#C8E6C9; padding:0.8em; border-radius:8px;">
✅ <strong>Budget disponible</strong><br>
On peut déployer des nouvelles features, expérimenter, prendre des risques calculés.
</div>
<div style="background:#FFCDD2; padding:0.8em; border-radius:8px;">
🚨 <strong>Budget épuisé</strong><br>
Stop aux déploiements. Focus sur la fiabilité uniquement. Postmortem obligatoire.
</div>
</div>

---

## Le SLA — Un contrat, pas un objectif <!-- .slide: data-background="#E3F2FD" -->

> Le SLA est un **engagement juridiquement contraignant** envers un tiers, avec des **pénalités financières** en cas de non-respect. Ce n'est pas un outil interne d'ingénierie.

<div style="display:grid; grid-template-columns:1fr 1fr; gap:1em; font-size:0.83em; margin-top:0.8em;">

<div>

**Exemples réels chez les cloud providers :**

- **AWS EC2** : 99.99% uptime garanti / mois<br>→ Si < 99.0% : crédit de **30%** de la facture mensuelle<br>→ Si < 95.0% : crédit de **100%**

- **GCP / Azure** : même logique, SLA par service (Compute, Database, CDN...)

- **Un hébergeur de votre appli** : si votre catalogue est down pendant le Gala à cause d'une panne infra hébergeur → vous pouvez invoquer le SLA pour obtenir un remboursement.

</div>

<div>

**Le SLA dans la chaîne de valeur :**

```
Vous (BDA Shotgun)
  ↓ SLO interne : 99.5%
  ↓ (votre objectif ingénierie)

Cloud / Hébergeur
  ↓ SLA contractuel : 99.9%
  ↓ (leur engagement vers vous)

Vous → vos utilisateurs
  → pas de SLA ici (pas de contrat
    avec les étudiants INSA)
```

**Règle empirique :**
Votre SLO doit toujours être **inférieur** au SLA de vos dépendances. Si votre hébergeur garantit 99.9%, viser 99.99% vous-même n'a aucun sens.

</div>
</div>

---

## Blameless Postmortem <!-- .slide: data-background="#ECEFF1" -->

> *"The goal is not to find who broke prod. The goal is to understand why the system allowed it."*

<p class="fragment">❌ <strong>Pas de blameless :</strong> "C'est la faute de Théo qui a mergé sans review."</p>
<p class="fragment">✅ <strong>Blameless :</strong> "Pourquoi le pipeline a-t-il permis ce merge ? Pourquoi l'alerte n'a pas tiré en &lt; 5 min ?"</p>

<ul class="fragment" style="margin-top:0.8em;">
<li>Structure : Timeline · Impact · Root Cause · Action Items</li>
<li>Partagé publiquement dans l'équipe (cf. GitLab qui publie les siens)</li>
<li>Lien avec votre TD : Knight Capital et GitLab — l'erreur humaine n'est jamais la vraie cause racine.</li>
</ul>

---

<!-- .slide: data-background="#0D47A1" -->

# Partie 2 — Les 3 Piliers de l'Observabilité <!-- .element: class="r-fit-text" -->

<p style="color:#90CAF9;">Logs · Metrics · Traces</p>

---

## 📋 Les Logs — "Qu'est-ce qui s'est passé ?"

<div style="display:grid; grid-template-columns:1fr 1fr; gap:1em; font-size:0.82em;">

<div>

**Ce que c'est :**
- Enregistrements horodatés d'événements discrets
- "À 12:00:03.412, l'utilisateur alice@insa.fr a tenté de réserver CARRÉ-A7"

**Ce qu'on veut en prod :**
- Format **JSON structuré** (parsable automatiquement)
- Niveaux : `DEBUG` / `INFO` / `WARN` / `ERROR`
- Contexte systématique : `traceId`, `userId`, `service`, `env`

</div>

<div>

```json
{
  "timestamp": "2026-04-15T12:00:03.412Z",
  "level": "INFO",
  "service": "checkout-service",
  "traceId": "4bf92f3577b34da6",
  "userId": "alice@insa.fr",
  "message": "Seat hold requested",
  "seatId": "CARRÉ-A7",
  "env": "production"
}
```

</div>
</div>

<p class="fragment" style="color:#ef5350; margin-top:0.5em;">⚠️ Ne jamais logger de données sensibles : email en clair, token Stripe, mot de passe. (cf. RGPD + incident E-S2)</p>

---

## 📊 Les Metrics — "Dans quel état est le système ?"

**La méthode RED** — pour chaque service :

<div style="display:grid; grid-template-columns:1fr 1fr 1fr; gap:0.8em; font-size:0.85em; margin-top:0.5em;">

<div style="background:#E3F2FD; padding:0.8em; border-radius:8px;">
<strong style="color:#1565C0;">R — Rate</strong><br>
Combien de requêtes par seconde ?<br><br>
<em>BDA : requêtes/s sur POST /seats/{id}/hold lors du shotgun</em>
</div>

<div style="background:#FCE4EC; padding:0.8em; border-radius:8px;">
<strong style="color:#C62828;">E — Errors</strong><br>
Quel % de requêtes échouent ?<br><br>
<em>BDA : taux de HTTP 409 (conflit) et 503 (catalog down)</em>
</div>

<div style="background:#E8F5E9; padding:0.8em; border-radius:8px;">
<strong style="color:#2E7D32;">D — Duration</strong><br>
Combien de temps ça prend ? (P50, P95, P99)<br><br>
<em>BDA : latence P95 du checkout en dessous de 200ms ?</em>
</div>

</div>

<p class="fragment" style="margin-top:0.8em;">Spring Boot Actuator expose tout ça sur <code>/actuator/metrics</code> → scraped par Prometheus → visualisé dans Datadog.</p>

---

## 🔍 Les Traces — "Quel chemin a suivi ma requête ?"

<div style="display:grid; grid-template-columns:1fr 1fr; gap:1em; font-size:0.85em;">

<div>

Un utilisateur clique "Réserver CARRÉ-A7".

<p class="fragment" data-fragment-index="1">
1. <code>frontend</code> → <code>GET /events/1/seats</code> [T1, 12ms]
</p>
<p class="fragment" data-fragment-index="2">
2. <code>checkout-service</code> → <code>POST /orders</code> [T2, 145ms]
</p>
<p class="fragment" data-fragment-index="3">
3. <code>checkout-service</code> → <code>POST /seats/42/hold</code> [COOP-01 → T1, 8ms]
</p>
<p class="fragment" data-fragment-index="4">
4. <code>checkout-service</code> → Stripe mock [34ms]
</p>
<p class="fragment" data-fragment-index="5">
5. Message <code>OrderConfirmed</code> → Pulsar → <code>ticketing-service</code> [T3, async]
</p>

</div>

<div class="fragment" data-fragment-index="6">

**Sans traces distribuées :**
> "Le checkout est lent" → lequel des 3 services ?

**Avec traces (Datadog APM) :**
> "C'est l'appel COOP-01 à catalog-service qui prend 800ms au lieu de 8ms"

Chaque requête porte un `traceId` propagé entre tous les services via les headers HTTP.

</div>
</div>

---

<!-- .slide: data-background="#4A148C" -->

# Partie 3 — Logback pour Spring <!-- .element: class="r-fit-text" -->

<p style="color:#CE93D8;">Structurer ses logs pour la production</p>

---

## Logback — Pourquoi ça compte en prod <!-- .slide: data-background="#F3E5F5" -->

<div style="display:grid; grid-template-columns:1fr 1fr; gap:1em; font-size:0.82em;">

<div>

**Sans configuration Logback :**
```
2026-04-15 12:00:03 INFO  c.b.CheckoutService - Order created
2026-04-15 12:00:03 ERROR c.b.CheckoutService - NullPointerException
	at com.bda.checkout...
	at java.base/...
```
😵 Illisible pour une machine. Impossible à requêter dans Datadog.

</div>

<div>

**Avec Logback JSON (logstash-logback-encoder) :**
```json
{
  "ts": "2026-04-15T12:00:03.412Z",
  "level": "ERROR",
  "logger": "CheckoutService",
  "msg": "Payment failed",
  "traceId": "4bf92f3577b34da6",
  "spanId": "00f067aa0ba902b7",
  "userId": "alice@insa.fr",
  "orderId": "ORD-8821",
  "exception": "StripeTimeoutException"
}
```
✅ Parsé automatiquement · Corrélé aux traces APM · Facets Datadog immédiats.

</div>
</div>

<div class="fragment" style="margin-top:0.8em; display:grid; grid-template-columns:1fr 1fr 1fr; gap:0.8em; font-size:0.82em;">
<div style="background:#E1BEE7; padding:0.6em; border-radius:6px;">📦 <strong>MDC</strong><br>Injecter automatiquement <code>traceId</code>, <code>userId</code>, <code>requestId</code> dans chaque log sans les passer manuellement.</div>
<div style="background:#CE93D8; padding:0.6em; border-radius:6px; color:#fff;">🎚️ <strong>Niveaux par env</strong><br><code>DEBUG</code> en dev, <code>INFO/WARN</code> en prod. Jamais de <code>DEBUG</code> en prod → coût + bruit + fuite de données.</div>
<div style="background:#AB47BC; padding:0.6em; border-radius:6px; color:#fff;">🔒 <strong>RGPD</strong><br>Masquer les PII avant de logger : email → hash, numéro de carte → jamais. Un log c'est de la donnée.</div>
</div>

---

<!-- .slide: data-background="#E65100" -->

# Partie 4 — Datadog en pratique <!-- .element: class="r-fit-text" -->

<p style="color:#FFCC80;">APM · Logs · Dashboards · SLO · Alerting</p>

---

## Architecture Datadog dans le projet <!-- .slide: data-background="#FFF3E0" -->

<div style="display:grid; grid-template-columns:1fr 1fr; gap:1em; font-size:0.83em;">

<div>

**Ce qui est déjà en place dans le repo :**

- ✅ **Unified Service Tagging** sur chaque image OCI
  ```yaml
  DD_SERVICE: catalog-service
  DD_VERSION: 1.2.0
  DD_ENV: production
  ```
- ✅ **Annotations Kubernetes** pour autodiscovery des logs (avec règle multiline pour les stack traces Java)
- ✅ **`@datadog/browser-logs`** dans le `package.json` frontend
- ✅ **Spring Boot Actuator** → `/actuator/metrics` scrapable

</div>

<div>

**Comment ça circule :**

```
[Pod Spring Boot]
    │ stdout JSON logs
    ▼
[Datadog Agent DaemonSet]  ← sur chaque nœud K8s
    │ logs + métriques + traces
    ▼
[Datadog Cloud]
    ├── Log Management
    ├── APM & Traces
    ├── Infrastructure Metrics
    └── RUM (Browser)
```

Le tag `service:checkout-service` + `env:production` + `version:1.2.0` **corrèle automatiquement** logs, métriques et traces.

</div>
</div>

---

## APM & Traces distribuées <!-- .slide: data-background="#FFF8E1" -->

> "Le Shotgun est lent." — OK, mais **où** exactement ?

<div style="font-size:0.85em; margin-top:0.5em;">

**Ce que Datadog APM vous donne :**

<p class="fragment">🗺️ <strong>Service Map</strong> — visualisation graphique de qui appelle qui : <code>frontend → checkout → catalog</code>, avec latence et taux d'erreur sur chaque arc.</p>

<p class="fragment">🔬 <strong>Flame Graph</strong> — pour une requête précise, chaque span (appel DB, appel HTTP, appel Redis) avec sa durée exacte. Vous voyez en 2 secondes si c'est la requête SQL ou l'appel COOP-01.</p>

<p class="fragment">🔗 <strong>Log correlation</strong> — depuis une trace, cliquez sur "View related logs" et vous voyez tous les logs de tous les services pour cette requête unique (<code>traceId</code> commun).</p>

<p class="fragment">📈 <strong>Automatic anomaly detection</strong> — Datadog détecte une augmentation de la latence P95 <em>avant</em> que vos utilisateurs se plaignent.</p>

</div>

---

## Construire un bon Dashboard BDA <!-- .slide: data-background="#FFF3E0" -->

**Principe : métriques métier d'abord, infrastructure ensuite.**

<div style="display:grid; grid-template-columns:1fr 1fr; gap:1em; font-size:0.83em; margin-top:0.5em;">

<div>

**Row 1 — Business KPIs** (le plus important)
- Tickets vendus / minute (courbe temps réel)
- Holds actifs en ce moment (jauge)
- Taux de conversion hold → achat confirmé
- Revenus générés (€)

**Row 2 — Service Health**
- Taux d'erreur HTTP par service (RED)
- Latence P95 checkout et catalog
- Statut des pods K8s (vert/rouge)

</div>

<div>

**Row 3 — Infrastructure**
- CPU / Mémoire par pod
- Connexions Redis (pic attendu à l'ouverture)
- Lag Pulsar (messages en attente sur COOP-02/03/04)

**Bonnes pratiques :**
- 1 dashboard par équipe + 1 dashboard "Vue Globale Shotgun"
- Annotations de déploiement (ligne verticale au moment du deploy)
- Variables de template : `$env`, `$service`, `$version`

</div>
</div>

---

## Définir un SLO dans Datadog <!-- .slide: data-background="#E8F5E9" -->

**Exemple : SLO checkout — latence P95 < 200ms**

<div style="display:grid; grid-template-columns:1fr 1fr; gap:1em; font-size:0.83em; margin-top:0.5em;">

<div>

**Étape 1 — Choisir le bon SLI**
```
Type : Metric-based SLO
Numérateur : nombre de requêtes P95 < 200ms
Dénominateur : total des requêtes
```

**Étape 2 — Fixer le target**
```
Target : 99.5% sur une fenêtre de 7 jours
Warning : 99.8% (alerte préventive)
```

**Étape 3 — Créer la Monitor associée**
- Trigger quand le taux de "bon" descend sous 99.8%
- Notification : Slack `#bda-alerts`
- Runbook link dans la description

</div>

<div>

**Ce que vous verrez dans Datadog :**

- Barre de progression de l'error budget
- "Il vous reste 83% de votre budget pour ce mois"
- Alerte automatique si vous brûlez le budget trop vite

**Exemples de SLOs BDA à définir :**

| Service | SLI | Target |
|---------|-----|--------|
| Catalog | Disponibilité /seats | 99.5% / 7j |
| Checkout | Latence P95 hold | < 200ms |
| Ticketing | Génération ticket | < 5s / 100% |
| Frontend | Core Web Vitals LCP | < 2.5s |

</div>
</div>

---

## Alerting & Bonnes pratiques de tagging <!-- .slide: data-background="#FFEBEE" -->

<div style="display:grid; grid-template-columns:1fr 1fr; gap:1em; font-size:0.83em;">

<div>

**Pyramide d'alerting — du plus précis au plus large :**

<p class="fragment" data-fragment-index="1">🔵 <strong>Log Monitor</strong> — "Plus de 10 ERROR en 1 min sur checkout-service"</p>
<p class="fragment" data-fragment-index="2">🟡 <strong>Metric Monitor</strong> — "CPU catalog-service > 90% pendant 3 min"</p>
<p class="fragment" data-fragment-index="3">🔴 <strong>Composite Monitor</strong> — "Latence élevée ET taux d'erreur en hausse" → c'est vraiment grave</p>
<p class="fragment" data-fragment-index="4">💀 <strong>SLO Burn Rate Alert</strong> — "Vous brûlez votre error budget 14× plus vite que la normale"</p>

</div>

<div>

**Unified Service Tagging — pourquoi c'est critique :**

Sans tags cohérents, impossible de faire :
```
service:checkout-service
env:production
version:1.3.0
team:t2
```

✅ Filtrer tous les logs d'un service en 1 clic  
✅ Comparer la latence entre `v1.2.0` et `v1.3.0` après un deploy  
✅ Corréler une alerte avec le dernier déploiement  
✅ Calculer le coût infra par service  

**Alert fatigue :**  
Trop d'alertes = aucune alerte n'est prise au sérieux. Chaque alerte doit avoir un runbook et une action claire.

</div>
</div>

---

<!-- .slide: data-background="#880E4F" -->

# Partie 5 — RUM & Monitoring Frontend <!-- .element: class="r-fit-text" -->

<p style="color:#F48FB1;">Real User Monitoring — Ce que voient vraiment vos utilisateurs</p>

---

## Le backend est "OK". Vraiment ? <!-- .slide: data-background="#FCE4EC" -->

<div style="display:grid; grid-template-columns:1fr 1fr; gap:1em; font-size:0.85em;">

<div>

**Vous regardez votre dashboard Datadog :**
- ✅ Catalog Service : HTTP 200, latence 45ms
- ✅ Checkout Service : P95 = 180ms
- ✅ Ticketing Service : 0 erreurs
- ✅ Pods tous en Running

<p class="fragment" style="color:#C62828; font-size:1.1em; margin-top:1em;">
Alors pourquoi 40% des étudiants n'arrivent pas à réserver ?
</p>

</div>

<div class="fragment">

**Ce que vous ne voyez PAS avec le monitoring backend :**
- Le bundle Angular qui met 8 secondes à charger sur mobile 4G
- Le composant de sélection des sièges qui freeze sur Safari iOS
- L'erreur JS silencieuse qui empêche le bouton "Confirmer" de fonctionner
- Les utilisateurs qui rage-cliquent "Réserver" 5 fois et créent 5 holds simultanés

</div>
</div>

<p class="fragment" style="margin-top:1em; text-align:center; font-size:1.1em;">
👉 <strong>Le backend sert des réponses. Le RUM mesure des expériences.</strong>
</p>

---

## Qu'est-ce que le RUM ? <!-- .slide: data-background="#F8BBD9" -->

> **Real User Monitoring** = mesure de la performance et de l'expérience **réelle** de chaque utilisateur, dans son vrai navigateur, sur sa vraie connexion.

<div style="display:grid; grid-template-columns:1fr 1fr 1fr; gap:0.8em; font-size:0.82em; margin-top:0.8em;">

<div style="background:#FFECF1; padding:0.8em; border-radius:8px;">
<strong>🌐 Performance</strong><br><br>
Temps de chargement page, Time to First Byte, Time to Interactive, taille des ressources JS/CSS
</div>

<div style="background:#F8BBD9; padding:0.8em; border-radius:8px;">
<strong>🐛 Erreurs</strong><br><br>
Erreurs JavaScript catchées et non-catchées, erreurs réseau (requêtes échouées vers les APIs), source maps pour avoir la vraie stacktrace
</div>

<div style="background:#F48FB1; padding:0.8em; border-radius:8px; color:#fff;">
<strong>👤 Sessions</strong><br><br>
Parcours complet d'un utilisateur, pages visitées, clics, Session Replay (enregistrement anonymisé de la session)
</div>

</div>

<p class="fragment" style="margin-top:0.8em;">
Datadog RUM = tout ça + <strong>corrélé automatiquement aux traces APM</strong> → vous voyez la requête frontend ET le span backend correspondant dans la même vue.
</p>

---

## Core Web Vitals — Les métriques qui comptent <!-- .slide: data-background="#FCE4EC" -->

Métriques définies par Google, utilisées pour le SEO et le ressenti utilisateur.

<div style="display:grid; grid-template-columns:1fr 1fr 1fr; gap:0.8em; font-size:0.82em; margin-top:0.5em;">

<div style="background:#E8F5E9; padding:0.8em; border-radius:8px;">
<strong>LCP</strong> — Largest Contentful Paint<br>
Temps avant que le contenu principal soit visible.<br><br>
🟢 Bon : &lt; 2.5s<br>
🟡 Moyen : 2.5s – 4s<br>
🔴 Mauvais : &gt; 4s<br><br>
<em>BDA : le plan de salle (image SVG) est le LCP — il faut qu'il charge vite.</em>
</div>

<div style="background:#FFF9C4; padding:0.8em; border-radius:8px;">
<strong>INP</strong> — Interaction to Next Paint<br>
Réactivité de l'interface aux clics.<br><br>
🟢 Bon : &lt; 200ms<br>
🟡 Moyen : 200ms – 500ms<br>
🔴 Mauvais : &gt; 500ms<br><br>
<em>BDA : cliquer sur un siège doit répondre immédiatement — pas de freeze Angular.</em>
</div>

<div style="background:#FFEBEE; padding:0.8em; border-radius:8px;">
<strong>CLS</strong> — Cumulative Layout Shift<br>
Stabilité visuelle de la page (pas de saut de contenu).<br><br>
🟢 Bon : &lt; 0.1<br>
🟡 Moyen : 0.1 – 0.25<br>
🔴 Mauvais : &gt; 0.25<br><br>
<em>BDA : le bouton "Confirmer" ne doit pas sauter au dernier moment — sinon mauvais clic.</em>
</div>

</div>

---

## Configurer Datadog RUM dans Angular <!-- .slide: data-background="#F3E5F5" -->

**Installer le package et initialiser dans `main.ts` avant le bootstrap.**

<div style="display:grid; grid-template-columns:1fr 1fr; gap:1em; font-size:0.78em;">

<div>

```typescript
// src/main.ts
import { datadogRum } from '@datadog/browser-rum'

datadogRum.init({
  applicationId: 'BDA_APP_ID',
  clientToken: 'pub_xxxx',
  site: 'datadoghq.eu',
  service: 'bda-frontend',
  env: environment.envName,    // 'production'
  version: environment.version, // '1.2.0'
  sessionSampleRate: 100,
  sessionReplaySampleRate: 20, // 20% en prod (coût)
  trackUserInteractions: true,
  trackResources: true,
  trackLongTasks: true,
  defaultPrivacyLevel: 'mask-user-input', // RGPD
})

// Identifier l'utilisateur après login Auth0
datadogRum.setUser({
  id: user.sub,
  email: user.email, // ⚠️ seulement si consentement
  role: user['https://bda/role'],
})

bootstrapApplication(AppComponent, appConfig)
```

</div>

<div>

**Ce que ça active :**

✅ Core Web Vitals automatiques  
✅ Toutes les erreurs JS (même les non-catchées)  
✅ Toutes les requêtes XHR/fetch vers les APIs  
✅ Session Replay (enregistrement anonymisé)  
✅ Corrélation avec les traces APM (même `traceId`)  

**Bonnes pratiques Angular :**
- `env` et `version` viennent des fichiers `environment.ts` / `environment.prod.ts`, injectés par la CI lors du build (`ng build --configuration production`)
- `defaultPrivacyLevel: 'mask-user-input'` obligatoire — masque les champs form
- Initialiser **avant** `bootstrapApplication` pour capturer les erreurs au démarrage

</div>
</div>

---

## RUM en action — Cas concrets BDA <!-- .slide: data-background="#EDE7F6" -->

<p style="font-size:0.9em;">Ce que le RUM vous permet de détecter que le monitoring backend ne voit <strong>jamais</strong> :</p>

<div style="font-size:0.83em; margin-top:0.5em;">

<p class="fragment" data-fragment-index="1">
🔴 <strong>Funnel d'abandon</strong> — "42% des utilisateurs arrivent sur la page siège, 61% abandonnent avant de cliquer 'Confirmer'." → Problème UX ou bug JS sur l'étape paiement ?
</p>

<p class="fragment" data-fragment-index="2">
🔴 <strong>Rage clicks</strong> — "27 utilisateurs ont cliqué plus de 5 fois sur le bouton 'Réserver' en moins de 2 secondes." → Le bouton ne répondait pas. Vous avez créé 27 holds simultanés. (lien avec le scénario T+3min du TD)
</p>

<p class="fragment" data-fragment-index="3">
🔴 <strong>Erreur silencieuse</strong> — "L'appel à <code>POST /orders</code> retourne 200, mais une TypeError dans le handler Angular empêche la redirection." → Le backend est OK. L'utilisateur est bloqué.
</p>

<p class="fragment" data-fragment-index="4">
🟡 <strong>Session Replay</strong> — Vous regardez la session enregistrée (anonymisée). Vous voyez exactement où l'utilisateur s'est bloqué, ce qu'il a cliqué, combien de temps il a attendu.
</p>

<p class="fragment" data-fragment-index="5">
✅ <strong>Corrélation trace</strong> — Dans la session, cliquez sur l'appel HTTP échoué → vous atterrissez directement dans le flame graph APM du backend pour cette requête précise.
</p>

</div>

---

<!-- .slide: data-background="#37474F" -->

# Partie 6 — Alerting & On-call <!-- .element: class="r-fit-text" -->

<p style="color:#B0BEC5;">Ne pas noyer l'équipe sous les alertes</p>

---

## Alerting — Ce qu'on veut éviter <!-- .slide: data-background="#ECEFF1" -->

<div style="display:grid; grid-template-columns:1fr 1fr; gap:1em; font-size:0.85em;">

<div>

**L'alert fatigue :**

> Votre téléphone sonne 47 fois en une nuit pour des alertes CPU à 71% sur un pod non-critique.

<p class="fragment">→ Après la 10ème alerte, personne ne regarde plus.</p>
<p class="fragment">→ La 11ème alerte est la vraie panne. Elle est ignorée.</p>
<p class="fragment">→ <strong>Trop d'alertes = zéro alerte efficace.</strong></p>

</div>

<div class="fragment">

**La pyramide d'alertes saine :**

```
        🔴 CRITIQUE
      PagerDuty / réveil
    "Le service est DOWN"
    ─────────────────────
      🟡 WARNING
    Slack #bda-alerts
  "Latence P95 en hausse"
  ─────────────────────────
    🔵 INFO
  Dashboard / annotation
"Deploy v1.3.0 effectué"
```

**Chaque alerte doit avoir :**
- Un titre clair : quoi, sur quel service, depuis quand
- Un lien vers le runbook
- Une action attendue documentée

</div>
</div>

<p class="fragment" style="margin-top:0.8em; text-align:center;">
💡 Si une alerte se déclenche et que la réponse est "on ignore", <strong>supprimez-la ou corrigez son threshold</strong>.
</p>
