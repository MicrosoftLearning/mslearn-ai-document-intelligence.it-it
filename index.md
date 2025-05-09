---
title: Esercizi di Document Intelligence di Azure AI
permalink: index.html
layout: home
---

Gli esercizi seguenti sono progettati per offrire un'esperienza di apprendimento pratica in cui verranno esaminate le attività comuni eseguite dagli sviluppatori durante la creazione di soluzioni di elaborazione intelligente dei documenti con Microsoft Azure.

> **Nota**: per completare gli esercizi, è necessaria una sottoscrizione di Azure in cui si dispone di autorizzazioni e quote sufficienti per effettuare il provisioning delle risorse di Azure necessarie. Creazione di un [account di Azure](https://azure.microsoft.com/free), se non già disponibile. È disponibile un'opzione di versione gratuita per i nuovi utenti che include crediti per i primi 30 giorni.

## Esercizi

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions'" %} {% for activity in labs  %} {% if activity.url contains 'ai-foundry' %} {% continue %} {% endif %}
<hr>
### [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }})

{{activity.lab.description}}

{% endfor %}

> **Note**: anche se è possibile completare questi esercizi autonomamente, questi sono progettati per integrare i moduli in [Microsoft Learn](https://learn.microsoft.com/training/paths/extract-data-from-forms-document-intelligence/), in cui è possibile approfondire alcuni dei concetti sottostanti su cui essi si basano.
