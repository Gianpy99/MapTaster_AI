# Product Requirements Document (PRD)
**Project Name:** MapTaster  
**Owner:** Solo build (you)  
**Date:** 2025-08-12  

---

## 1) Problem Statement
When exploring a new city or neighborhood, finding truly high-quality dining and nightlife spots is challenging. Review sites are often biased, flooded with fake ratings, or influenced by marketing campaigns. There is no single, trustworthy tool that aggregates reviews from multiple sources, filters out unreliable opinions, and presents a concise, sentiment-based shortlist — all integrated with navigation tools.

---

## 2) Goals & Objectives
- **Aggregate reviews** from multiple platforms (Google, TripAdvisor, Yelp, etc.).  
- **Filter out unreliable or suspicious reviews** using sentiment analysis and anomaly detection.  
- **Score and rank venues** by trust-adjusted sentiment rather than raw star ratings.  
- **Integrate with Google Maps** for navigation, exploration, and visualization.  
- **Provide a clean, mobile-first UI** that focuses on the best options quickly.

---

## 3) Target Users
- **Primary:** Tourists and travelers who want a quick shortlist of top-rated restaurants, cafes, and bars.  
- **Secondary:** Locals seeking hidden gems beyond mainstream suggestions.  
- **Tertiary:** Food bloggers, influencers, and culinary professionals scouting for quality venues.

---

## 4) Core Features

### MVP
1. **Multi-source Review Aggregation** via scraping/APIs (where permitted).  
2. **Sentiment Analysis** to determine positive/negative bias in textual reviews.  
3. **Fake Review Detection** using heuristics (sudden rating spikes, identical wording).  
4. **Weighted Scoring Algorithm** for final ranking (trust + sentiment).  
5. **Google Maps Integration** with direct “Navigate” links.  
6. **Search by Location & Cuisine**.  
7. **Shortlist View** (Top 5–10 recommendations only).

### Phase 2
8. **User Profiles** to store preferences (price range, cuisine types).  
9. **Offline List Saving** for travel with no internet connection.  
10. **Social Sharing** of shortlists.

### Phase 3
11. **Personalized Recommendations** based on past choices and feedback.  
12. **Real-time Event & Menu Updates** from integrated feeds.  
13. **Local Food Tour Mode** suggesting walking itineraries.

---

## 5) Non-Functional Requirements
- **Performance:** Search results delivered in < 4 seconds on 4G.  
- **Privacy:** No personal data collected unless the user creates an account.  
- **Reliability:** Fallback to cached results if API/scraping fails.  
- **Accessibility:** Mobile-first design; dark/light mode options.

---

## 6) Constraints
- Review platform terms may limit direct API use; scraping must respect local laws and robots.txt.  
- Sentiment analysis must handle multiple languages for international use.  
- Some data sources may have incomplete or outdated information.

---

## 7) Tech Stack Suggestion
- **Backend:** Python (FastAPI) for API + sentiment model integration.  
- **Frontend:** React Native for mobile or React PWA for web.  
- **Database:** PostgreSQL with PostGIS for geo queries.  
- **ML:** Hugging Face transformers for sentiment; scikit-learn for anomaly detection.  
- **Maps API:** Google Maps or Mapbox.

---

## 8) Success Metrics
- **Recommendation Accuracy:** ≥ 80% user agreement that suggestions match preferences.  
- **Trust Score Stability:** Minimal fluctuation in rankings from fake review removal.  
- **Engagement:** ≥ 50% of users save or navigate to a venue from the app.  
- **Retention:** ≥ 40% returning monthly users.

---

## 9) Roadmap

| Phase     | Duration  | Key Deliverables |
|-----------|-----------|------------------|
| **MVP**   | 8 weeks   | Aggregation engine, sentiment analysis, ranking algorithm, maps integration |
| **Phase 2** | +6 weeks | Profiles, offline lists, sharing |
| **Phase 3** | +8 weeks | Personalization, event updates, food tour mode |

---

## 10) Risks & Mitigation
- **Platform Restrictions:** Use official APIs where possible; keep scraping modular for fallback.  
- **Model Bias:** Train sentiment model with food-related datasets; periodically retrain.  
- **Data Gaps:** Cross-check venues across multiple sources for missing details.  
- **Legal Compliance:** Follow GDPR/local data laws; anonymize any stored review text.

---

## 11) Open Questions
- Should we allow **user-submitted reviews** or rely strictly on aggregated data?  
- Do we integrate **restaurant booking links** in MVP?  
- How aggressively should **fake review detection** remove borderline cases?

---
