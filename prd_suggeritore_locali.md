# PRD — Suggeritore Ristoranti/Bar con Sentiment Analysis

## 1. Titolo del prodotto
Suggeritore Locali (nome provvisorio): applicazione che, data una zona geografica e una richiesta (es. "ristoranti italiani", "bar con posti all'aperto"), restituisce una classifica delle migliori strutture basata su recensioni aggregate e sentiment analysis, con filtri per attendibilità e riepilogo sintetico.

## 2. Obiettivo
Permettere a un utente di scoprire rapidamente locali affidabili e rilevanti in una zona, combinando recensioni provenienti da più fonti, analizzandone il sentiment, rimuovendo o de-prioritizzando informazioni inaffidabili e presentando:
- una classifica trasparente e spiegabile;
- un breve recap (punti di forza/debolezza) generato automaticamente;
- filtri avanzati (prezzo, orari, tipo di cucina, accessibilità, ecc.).

## 3. Stakeholder
- Utente finale (turista, residente, professionista che prenota locali)
- Product owner / Team di sviluppo
- Team ML / Data Engineering
- Team Legal (per TOS e privacy)
- Fornitore dell'AI-agent che genera il codice (in VS Code)

## 4. Target e casi d'uso principali
1. Utente cerca il miglior ristorante giapponese entro 1 km: riceve top-5 con ragioni e link alle recensioni.
2. Utente vuole locali con "atmosfera tranquilla" o "adatto a bambini": filtra per sentiment su parole chiave rilevanti.
3. Utente richiede controllo di veridicità: l'app segnala recensioni sospette e mostra punteggi di affidabilità.
4. Gestore locale vuole vedere riepilogo dei punti di forza/debolezza basato su recensioni multi-sorgente.

## 5. MVP (Minimum Viable Product)
Funzionalità minime per rilascio iniziale:
- Ingestione da 3 fonti di recensioni (es. Google Reviews, Yelp, TripAdvisor) via API o scraping.
- Pipeline di normalizzazione e deduplica delle recensioni.
- Modulo di sentiment analysis (frase-level e review-level) che estragga polarità e aspetti (aspect-based sentiment).
- Semplice algoritmo di affidabilità per recensioni (metrica composta: età account, lunghezza testo, pattern sospetto, verifiche incrociate).
- Ranking locale che combini punteggio medio, volume recensioni, sentiment e affidabilità.
- Interfaccia web minimale: ricerca per zona + lista risultati con punteggio, 3-4 filtri e recap sintetico.

## 6. Requisiti funzionali (RF)
RF1 — Ricerca geografica: accetta coordinate o nome zona e raggio in km.
RF2 — Aggregazione dati: pianificare job periodici di raccolta e aggiornamento.
RF3 — Normalizzazione: mappare campi (nome locale, indirizzo, categoria, orari).
RF4 — Sentiment Analysis: output per review = {polarity score, aspects list, confidence}.
RF5 — Affidabilità recensione: output = {trust_score 0..1, reasons}.
RF6 — Ranking: output per locale = {composite_score, breakdown} e possibilità di spiegare la ranking.
RF7 — Filtri utente: prezzo, distanza, tipo, orario di apertura, punteggio minimo, affidabilità minima.
RF8 — Recap testuale: generare 3–5 bullet punto forza/debolezza per ogni locale.
RF9 — Endpoint API: `/search`, `/place/{id}`, `/refresh-source`.
RF10 — Dashboard amministrativa per monitorare pipeline, sorgenti, errori.

## 7. Requisiti non funzionali (RNF)
RNF1 — Performance: tempo di risposta per ricerca < 2s per risultati cache; per ricerche fresche < 5s.
RNF2 — Scalabilità: supportare aggregazione su 1M+ recensioni per città.
RNF3 — Privacy & Compliance: rispetto GDPR — anonimizzazione, opt-out, retention policy.
RNF4 — Sicurezza: API keys rotazione, limit rate per scraping, protezione contro scraping abuse.
RNF5 — Tracciabilità: ogni risultato deve esporre quali sorgenti hanno contribuito.

## 8. Architettura proposta (high level)
- Ingest: connettori API + scraper (modulari per ogni fonte).
- Data lake / DB: store raw reviews (document store come MongoDB o cloud bucket) + normalized store (Postgres/Elasticsearch per ricerca geo).
- ETL/Preprocessing: deduplica, normalizzazione, entity resolution (place matching).
- ML Pipeline: tokenizer → embedding → sentiment classifier → aspect extractor → trust scorer.
- Ranking service: combina segnali con pesi configurabili.
- Backend API (REST) + frontend SPA (React/Vue).
- Orchestrazione: cron jobs o Airflow / Dagster per pipeline.

## 9. Modello ML dettagliato
- Sentiment:
  - Modello base: classificatore trasformers (distilBERT / small multilingual) fine-tuned per recensioni.
  - Output: probabilità per classi (neg/neu/pos) + score continuo (-1..+1).
- Aspect-based sentiment:
  - Estrazione aspetti (cucina, servizio, prezzo, ambiente) via NER + classifier.
- Trust scoring:
  - Feature input: account age (se disponibile), review length, unigram/bigram spam patterns, similarity con altre recensioni, cross-source consistency, IP/user reuse signals (se disponibili).
  - Modello: gradient boosting (LightGBM) o classificatore semplice con explainability.
- Explainability: SHAP o regole di business per produrre la spiegazione del trust_score.

## 10. Fonti dati (esempi e note legali)
- Google Reviews API (policy + quota)
- Yelp Fusion API
- TripAdvisor (attenzione TOS, scraping può essere necessario ma verificare legalità)
- Siti locali e blog (scraping)
- Dati Open: OpenStreetMap per geocoding

> **Nota Legale:** lo scraping di siti soggetti a termini d'uso può avere restrizioni legali. Prima di implementare scraping sistematico, consultare il team legale e prevedere fallback tramite APIs ufficiali.

## 11. Interfaccia utente (wireframe essenziale)
- Barra di ricerca (zona, tipo di locale, raggio)
- Lista risultati con: nome, stella composite, distanza, prezzo, indicatori di affidabilità (icona), breve snippet con 2 bullet (forza/debolezza)
- Pagina dettaglio: mappa, breakdown punteggio (media, sentiment, trust), top 5 recensioni estratte, timeline recensioni, link alla sorgente.
- Filtro avanzato: filtri booleani e slider per punteggi.

## 12. Metriche di successo (KPIs)
- Precision@5 rispetto a valutazione esperta locale (> 0.7 target per MVP)
- Click-through / CTR verso link locale
- Tempo medio di risposta della ricerca
- Percentuale di risultati con trust_score > 0.7
- A/B test: tasso di soddisfazione user (NPS) rispetto a baseline

## 13. Acceptance Criteria (criteri di accettazione per MVP)
- `/search` restituisce top-10 entro 5s per query realistica.
- Ranking spiegabile: per ogni locale mostrare il breakdown dei segnali.
- Sentiment model F1 macro >= 0.75 su dataset di test (dominio recensioni).
- Affidabilità: sistema individua almeno 80% recensioni sintetiche in un dataset di test.
- Frontend mostra recap sintetico per ogni locale.

## 14. Roadmap e milestone
- Settimana 0–2: definizione requisiti, scelta fonti e verifica legale.
- Settimana 2–6: implementazione ingest e pipeline di normalizzazione + DB.
- Settimana 6–10: implementazione modelli ML (sentiment + aspect + trust scorer).
- Settimana 10–12: ranking, API e frontend MVP.
- Settimana 12–14: test sul campo, metriche e ottimizzazioni.

## 15. Dipendenze e rischi
- Disponibilità API e limiti quota.
- Restrizioni legali allo scraping.
- Etichezza e bias dei modelli ML (lingue, dialetti, sarcasmo).
- Costi di storage e compute per raccolta massiva.

## 16. Tecnologie suggerite
- Backend: Python (FastAPI) o Node.js (Express).
- DB: Postgres + Elasticsearch / OpenSearch per ricerca full-text e geo.
- ML: PyTorch + Hugging Face Transformers, scikit-learn, LightGBM.
- Orchestration: Airflow / Dagster / cron.
- Frontend: React (Next.js) o Vue.
- Deployment: Docker + Kubernetes / or serverless (Cloud Run).

## 17. API sketch (esempi)
- `POST /search` {lat, lon, radius_km, q, filters} → top results
- `GET /place/{id}` → dettaglio con breakdown e recensioni estratte
- `POST /admin/refresh` → forza refresh sorgenti

## 18. Test plan
- Unit tests per normalizzazione e deduplica.
- Evaluation set per sentiment e aspect extraction.
- Synthetic dataset per review spam detection.
- Integration test end-to-end (ingest → ML → ranking → API).

## 19. Deliverables per l'agente AI (cose da chiedere al agente in VS Code)
- Codice scaffold per: connettori di 3 sorgenti, pipeline ETL, DB schema, modello sentiment minimal, servizio ranking e API REST.
- Test suite minima e script per populare DB con dataset di esempio.
- README con istruzioni di deploy locale.
- Endpoint Swagger/OpenAPI per testing.

## 20. Note finali
- Il documento è pensato per essere usato come input diretto ad un agente AI che genererà codice; raccomando di accompagnare l'agente con dataset di esempio e con una policy chiara su quali sorgenti usare (API vs scraping).  
- Se desideri, posso generare in aggiunta: user stories, file OpenAPI completo, o prompt pronto per l'agente VS Code che includa task chiari e priorità.

---
*Fine PRD (versione 0.1)*
