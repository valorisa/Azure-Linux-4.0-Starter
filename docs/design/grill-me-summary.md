# Stress-Test du projet JaiLIP Educational

## Décisions clés issues du grill

### Architecture
- Pur `transformers` (pas `lavis`) — pour la pérennité et la transparence
- Modèle : `blip2-flan-t5-xl` — tient sur T4 Colab

### Pédagogie
- Top-down, encastrée : magic trick → déconstruction → boucle complète
- Proxy de censure : pas de toxicité, contournement d'alignement démontré

### Robustesse
- Checkpointing toutes les 50 itérations
- Loss en float32 pour éviter l'underflow
- Trapdoor : possibilité de changer de modèle en cas d'OOM

### CI/CD
- Pylint avec dépendances installées
- Python 3.10 & 3.11
- Score minimum 8.0/10
