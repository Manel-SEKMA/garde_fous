# Agent IA sécurisé avec sanitation
# garde_fous
L’objectif de cette version est d’intégrer des garde-fous et une sanitation des entrées utilisateur
## 1. Description du projet

Ce projet contient un agent IA backend capable de répondre à des questions via un frontend, en utilisant plusieurs outils :

- **Recherche web :**  DuckDuckGo

- **Recherche encyclopédique :** Wikipedia

- **Génération de texte :** ChatGoogleGenerativeAI

- **Calculs mathématiques :** LLMMathChain

L’objectif de cette version est d’intégrer des garde-fous et une sanitation des entrées utilisateur pour :

- Éviter les contenus offensants ou dangereux

- Nettoyer et sécuriser les entrées utilisateur

- Filtrer les sources non fiables

- Vérifier les calculs mathématiques avant de renvoyer la réponse

 ## 2. Étapes d’intégration
### - Créer l’utilitaire de sanitation (utils.py)

- Fonction principale :
  
  ```python
  sanitize_input(user_input: str) → str
  
 ```

- Nettoie les balises HTML, supprime les espaces inutiles, limite la longueur.

  **code:**

  ```python
  from utils import sanitize_input
  clean_question = sanitize_input(user_question)
   ```

### - Modifier le backend FastAPI (main.py)

Appeler sanitize_input avant d’envoyer la question à l’agent

Filtrer le contenu sensible avant de renvoyer la réponse

Gérer les erreurs globalement
**code:**

```python

@app.post("/ask")
def ask_question(data: Question):
    question = sanitize_input(data.question)
    try:
        raw_response = agent_executor(question)
        if any(word in raw_response.lower() for word in ["violence", "attaque"]):
            response = "Je ne peux pas répondre à cette question."
        else:
            response = raw_response
        return {"answer": response}
    except Exception:
        return {"answer": "Désolé, je ne peux pas répondre à cette question pour le moment."}

        ```
### -Filtrage des sources (tools.py)
Limiter les résultats DuckDuckGo aux sources fiables (Wikipedia, .edu)

Vérifier les résultats Wikipedia pour le contenu sensible
**code:**

```python
safe_results = safe_duckduckgo_search(query)
wiki_result = safe_wikipedia_query(query)

```
### - Agent IA avec prompt de sécurité (agent.py)
Ajout d’un prompt global pour éviter les contenus offensants ou sensibles

Validation des calculs mathématiques via LLMMathChain

Gestion des erreurs lors du calcul ou de la génération

**code:**

```python
  prompt = f"{SECURE_PROMPT}\nQuestion: {question}"
   response = llm.chat(prompt).content

```
### -Dépendances (requirements.txt)
fastapi
pydantic
uvicorn
langchain

