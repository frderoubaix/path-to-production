# Livrer petit, livrer souvent <!-- .element: class="r-fit-text" -->

### Pourquoi vous n'avez pas encore déployé — et pourquoi c'est un problème.

---

<!-- .slide: data-background="#B71C1C" -->

## Session 4. Zéro déploiement. <!-- .element: class="r-fit-text" style="color:#fff;" -->

<p style="color:#ffcdd2; font-size:1.2em;" class="fragment">Ni en dev. Ni en prod.</p>

<p style="color:#fff; margin-top:1em;" class="fragment">Ce n'est pas un retard de planning.</p>
<p style="color:#ef5350; font-weight:bold;" class="fragment">C'est un signal d'alarme sur votre façon de travailler.</p>

---

## Ce que vous faites (sans le savoir)

<!-- .slide: data-background="#263238" -->

<div style="display:flex; justify-content:space-around; align-items:flex-start; color:#fff; margin-top:1em;">
  <div style="width:44%; background:#37474f; padding:1em; border-radius:8px;">
    <h3 style="color:#ef5350;">Ce que vous faites</h3>
    <ul style="font-size:0.85em;">
      <li>On code tout</li>
      <li>On assemble à la fin</li>
      <li>On déploie "quand c'est prêt"</li>
      <li>On livre pour la dernière session</li>
    </ul>
  </div>
  <div style="width:44%; background:#1b5e20; padding:1em; border-radius:8px;" class="fragment">
    <h3 style="color:#a5d6a7;">Ce qu'on attendait</h3>
    <ul style="font-size:0.85em;">
      <li>On déploie dès qu'une chose marche</li>
      <li>On intègre en continu</li>
      <li>Dev tourne depuis la session 1</li>
      <li>Chaque session = nouvelle livraison</li>
    </ul>
  </div>
</div>

<p style="color:#ffcc02; margin-top:1em; font-weight:bold;" class="fragment">Vous faites un cycle en V déguisé en projet agile.</p>

---

<!-- .slide: data-background="#1A237E" -->

> *"If it hurts, do it more often."*
> — Jez Humble, *Continuous Delivery* <!-- .element: style="color:#9fa8da; font-size:0.8em;" -->

<!-- .element: style="color:#fff; font-size:1.4em;" -->

---

## La dette de déploiement

<div style="display:flex; flex-direction:column; gap:0.5em; margin-top:0.5em;">
  <div style="background:#ffebee; padding:0.7em 1em; border-left:4px solid #ef5350; border-radius:4px;" class="fragment">
    <strong>On accumule du code non déployé</strong> → le lot grossit
  </div>
  <div style="background:#fff3e0; padding:0.7em 1em; border-left:4px solid #ff9800; border-radius:4px;" class="fragment">
    <strong>Le lot est gros</strong> → le risque de régression augmente
  </div>
  <div style="background:#fce4ec; padding:0.7em 1em; border-left:4px solid #e91e63; border-radius:4px;" class="fragment">
    <strong>Le risque est élevé</strong> → on a peur de déployer
  </div>
  <div style="background:#f3e5f5; padding:0.7em 1em; border-left:4px solid #9c27b0; border-radius:4px;" class="fragment">
    <strong>On reporte encore</strong> → le lot grossit encore plus
  </div>
  <div style="background:#b71c1c; padding:0.9em 1em; border-left:4px solid #ff1744; border-radius:4px; color:#fff;" class="fragment">
    <strong>Résultat :</strong> Un déploiement massif, stressant, incontrôlable.
  </div>
</div>

---

## La spirale en chiffres

<!-- .slide: data-background="#ECEFF1" -->

| Taille du lot | Nb de changements | Temps de debug moyen | Rollback possible ? |
|:---:|:---:|:---:|:---:|
| 1 feature | ~50 lignes | 10 min | ✅ Facile |
| 1 sprint | ~500 lignes | 2h | ⚠️ Difficile |
| 3 sessions | ~5000 lignes | ??? | ❌ Bonne chance |

<p class="fragment" style="margin-top:1em;"><strong>Plus le diff est petit, plus le bug est facile à trouver.</strong></p>
<p class="fragment">C'est une loi, pas une opinion.</p>

---

<!-- .slide: data-background="#004D40" -->

> *"Il ne se passe pas un seul instant sans qu'Amazon ne déploie en production quelque part dans le monde."*

<!-- .element: style="color:#e0f2f1; font-size:1.2em;" -->

<p style="color:#80cbc4; margin-top:1em;" class="fragment">En 2011 : <strong style="color:#fff;">1 déploiement toutes les 11,6 secondes.</strong></p>
<p style="color:#80cbc4;" class="fragment">Aujourd'hui : des <strong style="color:#fff;">dizaines de milliers par jour</strong>.</p>
<p style="color:#a7ffeb; margin-top:1em; font-weight:bold;" class="fragment">Ce n'est pas possible parce qu'Amazon est grand.<br>C'est possible parce qu'ils livrent <em>petit</em>.</p>

---

## Ce que "livrer petit" change concrètement

<!-- .slide: data-background="#E8F5E9" -->

<p class="fragment" data-fragment-index="1">✅ <strong>Feedback immédiat :</strong> On sait en 5 minutes si quelque chose casse en intégration.</p>
<p class="fragment" data-fragment-index="2">✅ <strong>Rollback trivial :</strong> Revenir en arrière sur 50 lignes, c'est 1 commande.</p>
<p class="fragment" data-fragment-index="3">✅ <strong>Debugging ciblé :</strong> Le bug est forcément dans les 50 dernières lignes.</p>
<p class="fragment" data-fragment-index="4">✅ <strong>Confiance qui s'accumule :</strong> Chaque petit déploiement réussi renforce le process.</p>
<p class="fragment" data-fragment-index="5">✅ <strong>Intégration inter-équipes :</strong> T2 découvre tout de suite si l'API de T1 ne correspond pas au contrat.</p>

---

## Mais déployer sans regarder, c'est voler à l'aveugle

<!-- .slide: data-background="#212121" -->

<p style="color:#fff;" class="fragment">Livrer petit n'a de valeur que si vous <strong style="color:#ffcc02;">observez ce que vous livrez</strong>.</p>

<div style="display:flex; justify-content:space-around; margin-top:1em;" class="fragment">
  <div style="background:#1a1a2e; border:1px solid #ef5350; padding:0.8em 1.2em; border-radius:8px; color:#ef9a9a; text-align:center;">
    <div style="font-size:1.8em;">📋</div>
    <strong>Logs</strong>
    <p style="font-size:0.75em; color:#ccc;">Ce qui s'est passé</p>
  </div>
  <div style="background:#1a1a2e; border:1px solid #ffcc02; padding:0.8em 1.2em; border-radius:8px; color:#fff176; text-align:center;">
    <div style="font-size:1.8em;">📈</div>
    <strong>Métriques</strong>
    <p style="font-size:0.75em; color:#ccc;">CPU, mémoire, latence, erreurs</p>
  </div>
  <div style="background:#1a1a2e; border:1px solid #66bb6a; padding:0.8em 1.2em; border-radius:8px; color:#a5d6a7; text-align:center;">
    <div style="font-size:1.8em;">🔍</div>
    <strong>Traces</strong>
    <p style="font-size:0.75em; color:#ccc;">Le chemin d'une requête</p>
  </div>
</div>

<p style="color:#9e9e9e; margin-top:1em; font-size:0.9em;" class="fragment">Sans ça, quand quelque chose casse en prod →<br>vous avez un bug, mais pas d'indice. Juste la panique.</p>

---

## Le monitoring, c'est votre boucle de feedback

<!-- .slide: data-background="#0D1B2A" -->

<div style="color:#fff; font-size:0.95em;">

<p class="fragment">Chaque déploiement doit s'accompagner d'une question :<br>
<strong style="color:#ffcc02;">"Comment je sais que ça marche ?"</strong></p>

<ul style="margin-top:0.8em;">
  <li class="fragment">Est-ce que mes endpoints répondent ? <span style="color:#66bb6a;">→ healthcheck</span></li>
  <li class="fragment">Est-ce que j'ai des erreurs 5xx ? <span style="color:#66bb6a;">→ métriques HTTP</span></li>
  <li class="fragment">Est-ce que la réservation d'un siège prend moins de 200ms ? <span style="color:#66bb6a;">→ SLO latence</span></li>
  <li class="fragment">Est-ce que deux commandes ont réservé le même siège ? <span style="color:#ef5350;">→ alerte métier</span></li>
</ul>

<p style="color:#80cbc4; margin-top:1em; font-weight:bold;" class="fragment">Livrer sans monitorer, c'est ouvrir le Shotgun sans savoir si le site tient.</p>

</div>

---

## Ce que ça veut dire pour vous, maintenant

<!-- .slide: data-background="#F3E5F5" -->

<p class="fragment" data-fragment-index="1">🎯 <strong>Cette semaine :</strong> chaque équipe déploie quelque chose en <code>dev</code>. N'importe quoi. Un endpoint qui répond 200. Le squelette.</p>

<p class="fragment" data-fragment-index="2">🔥 <strong>Déployer quelque chose de "pas fini" en dev, c'est le but.</strong><br>Dev = bac à sable. On a le droit de casser.</p>

<p class="fragment" data-fragment-index="3">📋 <strong>Votre Definition of Done change :</strong><br>Une feature n'est <em>pas terminée</em> tant qu'elle n'est pas déployée en dev.</p>

<p class="fragment" data-fragment-index="4">⛔ <strong>Ce qui n'est pas acceptable :</strong> coder pendant 3 semaines et déployer une seule fois à la fin.</p>

---

## Les jalons attendus du cours

<!-- .slide: data-background="#E3F2FD" -->

<div style="font-size:0.85em;">

| Session | Env | Objectif |
|:---:|:---:|:---|
| **S1** | local | Le squelette du service tourne en local |
| **S2** | local | Pipeline CI passe ✅ — image Docker buildée |
| **S3** | `dev` | Premier déploiement — au moins un endpoint live |
| **S4** | `dev` | ⚠️ **Intégrations inter-équipes testées** |
| **S5** | `dev` → `prod` | Premier déploiement en `prod` |
| **S6–S7** | `prod` | Journées complètes : itérations prod, **monitoring actif** |
| **S8** | `prod` | Stabilisation, observabilité, SLOs |
| **S9** | `prod` | Shotgun — go live réel |

</div>

<p style="color:#ef5350; font-weight:bold; margin-top:0.8em;" class="fragment">On est en S4. Dev devrait tourner depuis S3. Il vous reste 5 sessions pour aller en prod.</p>

---

<!-- .slide: data-background="#212121" -->

## La question à se poser chaque jour <!-- .element: style="color:#fff;" -->

<br>

> *"Qu'est-ce que j'ai livré aujourd'hui qui tourne quelque part ?"* <!-- .element: style="color:#ffcc02; font-size:1.3em;" -->

<p style="color:#9e9e9e; margin-top:1.5em;" class="fragment">Si la réponse est "rien" plusieurs jours de suite →<br>vous avez un problème de process, pas un problème de code.</p>
