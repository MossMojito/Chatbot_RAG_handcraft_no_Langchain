# 🏆 Adaptive Sports RAG (Technical Showcase)

> **Role**: AI Engineer (End-to-End Implementation)
> **Tech Stack**: Python, Crawl4AI, Qwen2.5-VL (OCR), ChromaDB, Databricks Serving
> **Key Concept**: Hierarchical Retrieval without LangChain

## 📖 Overview & Problem Solved
**The Challenge**: Standard RAG pipelines fail when scraping complex internal sports dashboards. The data is often locked in **promotional images** (banners with prices) or stored in mixed **"Super Bundles"** (multi-sport packages) where a simple chunking strategy loses the context of which sport belongs to which package.

**The Solution**: A custom-engineered RAG pipeline that:
1.  **"Reads" Images**: Uses a Vision-Language Model (Qwen2.5-VL) to OCR promotional banners during scraping.
2.  **Preserves Context**: Implements a **Parent-Child Indexing** strategy. We search small chunks (Children) for precision but feed the full pricing table (Parent) to the LLM for accuracy.
3.  **Removes Bloat**: Replaces heavy frameworks (LangChain) with a lightweight, hand-crafted `ConversationMemory` and `RetrievalEngine` for full control over the context window.

---

## 🏗️ System Architecture

![Architecture Diagram](https://mermaid.ink/img/pako:eNqdVdtu2zgQ_RWCjy1QO06c9KXFokC3C7SLbZCiD70wDI2OrayRREo-JAvD_76kLMuyk-SDscBwZs6cMzOcvmClKkEDq_S6-IJVzIGeK1sK-1a8fl3e3t3eP_Ffz99_Xb-Vz7e3D_wX5-3XzfLb_fPtCj4t_y5vH575L8ufXzff7leL77cr_ru8eXh4usPPDzeLxyX8tHj4fn33yM_Lh_vvS_g5v3944L-eHm5-L5ffHnj5cP_1-ft9Wcr6u_x5_fR0u_yxfH7kF375eH_7cMs_PpSr9Wq1glaU0K4Q9VwY621d1dAYbW3F9FKYWmpboO1FaYXFjFvjK91aW-pKe1vKChYwN2XF_zCnhS4r3UBlq1JbsFDrQnujK21gAS1LbcVb66R1FiyMqbQqoa5qrcFCo3WhwYqF1oVW0FpdaLBgYaxSKs_g19rZSoMFk9pWpS5ggTldGgtWDNVKa7Awo0urwAK6qrQutAYLjbQVC72uNNirO9vWWoEFrEpd8D_C1BqtjQILRttKgwWTopYVWAipS63AgtGl1mDBpNZKgwW01rYqYD4kK0vtwAIWeluwcFZZXWiwgNGl0mDhTKW0BgsYq63OsQ8ZtdI6x35I10aDhRldGg0W0KXWYOEsdWnBgpG61BoscKkLrQoYDWZ0aRRYwFhddY49yKiVBgsYq63OcRwZXVrtHOeRsdK2zHEead1aFziPjC6tco7zSOvWVrnAeWRsaatc4DwyprQVC5xHxpS2yiXOI2NKW-US55Exta1yifPImNJWucR5ZExpK1ziPDImtVUucR4Zk9oqlziPjEltlUucR8aktsoVziNjSlvlCueRManLHOdZZEzqyhznWWRMW5njPIuMaV3mOM8iY1qXOc6zyJjWZY7zLDKmdZnzPCuRMantHOdZZExrO8d5FhlT2jrPs8iY0tZ5nkXGtK7yPMy6yJjWdZ5nkTGt6zzPImNa13meRca0rvM8i4xpW5d5nrfImLb1eZ63yJi29Xmet8iYtvV5nrfImLZ1nuddZExre57nXTQY09qe53kXGdPanud5FxlTts7zvIuMKVuf53kXGVO2znO-i4wpa5_nfBcZU9Y-z_kuMqasfZ7zXWRMWfs857vImLa1z_O-i4xpa5_nfBcZ07b2ec53kTFt6zznu8iYtnWe811kTNva5znfRca0rc9zvgv6hWlb--_Pdy1kTFv77893LWRMW_vvz3ctZExb--_Pdy1kTFv77893LWRMW_vvz3ctZExb--_Pdy1kTFv77893LWRMW_vvz3ctZExb--_Pdy1kTFv/7893LWRMW_vvz38tZExb--_Pfy1kTFv7n_NlFxlT1v7nfNlFxpSdz3mZRcaUnc95mUXGlJ3PeZlFxpSdz3mZRcaUnc95mUXGlJ3PeZlFxpSdm_NlFxlTdm7Ol11kTNm5OV920UJM2bk5X3aRMWXn5nzZRcaUnZvzbRcZU3ZuG-fbLjKmbN02zrdLtBBTdm7Ol11kTNm5OV92kTFp5y5w3kWDSVk359suMiZlned820XGlK3P820XGVO2Ps-3XWRM2fo833aRMWXr83zbRcaUrc_zbRcZU7Y-z7ddZEzZ-jzfdpExZevzfNtFxpStz_NtFxlTtj7Pt11kTNn6PN92kTFt6_N820XGtK3P820XGdO2Ps-3XWRM2_o833aRMW3r83zbRca0rc_zbRcZ07Y-z7ddZEzbuq1z7WKM6Vq3da7d0WBKW7c1z7WLMabrtsa5di3GlK7bGu3axRjTdWtz7VqMKV23Nc61azGma7fWuXYtxtSu2xrn2sUY03Vb41y7GGO6bmuca9diTOm6rXGuXYsxXbc1zrVrMaZr3da5di3GlK7bOteuxZiu2xrn2sUY03Vb41y7GGO6bmuca9diTNe6rXGuXYsxXeu2xrl2LcbUrtsa59q1GNN1W-Ncu5hium5rnGsXY0zXbc1z7WKM6bqtca5dizFd67Z2LcbUrts6167FmK51W-PauRhjuuZ5rl2MMV3zPNcuxpiueZ5rF2NM1zzPtYsxpmu91rl2LcbUrnea59q1GNN1z3PtYozpmufatRjTNc-1izGma55rF2NM1zzXrv9gTNd6rXPtosGYrnmecxdiTNd6znmZMabrOedlxpiu55yXGWO6nnNeZozpes55mTGmy3qZcabrcplxpstyVmaM6XJeZozpct4fM8Z0Oe-PGWO6nPfHjDFdzvtjxpgu5_0xY0yX8_6YIabrG2PK_jGlG2N63htTuTGm570xpRtjer4xpnZjTN8YU7oxpue9MaUbw3X7Py_9_A8DVk9O)

*(Note: Actual data pathways include strict firewall traversals, represented here conceptually)*

## 🔐 Use of Proprietary Data (Why Mock Data?) -- **[READ THIS]**

This repository contains the **complete, functional source code** of the system used in production. However, because the actual scraping targets are internal dashboards and the data is proprietary:

1.  **Mock Data**: I have included a `data/raw` folder with **mock markdown files** (simulating the structure of real EPL/NBA packages).
2.  **Verified Logic**: You can run `notebooks/demo_ingestion.ipynb` to see the logic work perfectly on this mock data.
3.  **Real Code**: The `src/` folder contains the exact robust logic used in production (including the sliding-window memory and recursive character matching).

## 🚀 How to Run (Portfolio Demo)

1.  **Install**:
    ```bash
    pip install -r requirements.txt
    ```

2.  **Verify Ingestion**:
    Run `python verify_ingestion.py`.
    *Output*: You will see it Chunk -> Index -> Store in ChromaDB.

3.  **Run Chatbot**:
    ```bash
    # Open the UI to test Retrieval (Uses Mock Data)
    export OPENAI_API_KEY="your-key-here"
    python app.py
    ```

## 🧠 Key Engineering Decisions

### Why Hand-Crafted Memory?
Instead of `LangChain.ConversationBufferMemory`, I implemented a custom sliding window class (`src/ais_rag/chatbot/memory.py`).
**Advantage**: Precise control over token counts and ability to "inject" system prompts (like "You are a Sport Assistant") *after* history truncation but *before* the new query, ensuring instructions never get lost in long contexts.

### Why Parent-Child Indexing?
Users ask "How much is the Sport Package?". A simple chunk might just say "Price: $199" without context.
**My Strategy**:
- **Chunk**: "Price: $199" (Good for search match)
- **Retrieved**: The FULL document containing "NBA + EPL + Golf" (Good for answer generation).
- *See `src/ais_rag/ingestion/hierarchy.py` for implementation.*
