---
title: Esercizi di Azure OpenAI
permalink: index.html
layout: home
---

# Sviluppare applicazioni di intelligenza artificiale generativa con Studio AI della piattaforma Azure

Gli esercizi seguenti sono progettati per offrire un'esperienza di apprendimento pratica in cui verranno esaminati modelli e tecniche comuni usati dagli sviluppatori per creare applicazioni di intelligenza artificiale generativa come "copiloti" basati su chat e imparare a implementare questi modelli usando i servizi di Azure AI, in particolare il Servizio OpenAI di Azure e Studio AI della piattaforma Azure.

Anche se è possibile completare questi esercizi autonomamente, questi sono progettati per integrare i moduli in [Microsoft Learn](https://learn.microsoft.com/training/paths/create-custom-copilots-ai-studio/), in cui è possibile approfondire alcuni dei concetti sottostanti su cui essi si basano.

> **Nota**: per completare gli esercizi, è necessaria una sottoscrizione di Azure in cui si dispone di autorizzazioni e quote sufficienti per effettuare il provisioning delle risorse di Azure usate da Studio AI della piattaforma Azure e per distribuire e usare i modelli GPT di Azure OpenAI. Creazione di un [account di Azure](https://azure.microsoft.com/free), se non già disponibile. È disponibile un'opzione di versione gratuita per i nuovi utenti che include crediti per i primi 30 giorni.

## Esercizi

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions'" %} {% for activity in labs  %}
- [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) {% endfor %}