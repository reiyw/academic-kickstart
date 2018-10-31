+++
title = "Interpretable and Compositional Relation Learning by Joint Training with an Autoencoder"
date = "2018-07-15"

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = ["**Ryo Takahashi**", "Ran Tian", "Kentaro Inui"]

# Publication type.
# Legend:
# 0 = Uncategorized
# 1 = Conference proceedings
# 2 = Journal
# 3 = Work in progress
# 4 = Technical report
# 5 = Book
# 6 = Book chapter
publication_types = ["1"]

# Publication name and optional abbreviated version.
publication = "In Proceedings of the 56th Annual Meeting of the Association for Computational Linguistics"
publication_short = "In *ACL*"

# Abstract and optional shortened version.
abstract = "Embedding models for entities and relations are extremely useful for recovering missing facts in a knowledge base. Intuitively, a relation can be modeled by a matrix mapping entity vectors. However, relations reside on low dimension sub-manifolds in the parameter space of arbitrary matrices --- for one reason, composition of two relations $\\boldsymbol{M}_1,\\boldsymbol{M}_2$ may match a third $\\boldsymbol{M}_3$ (e.g. composition of relations currency_of_country and country_of_film usually matches currency_of_film_budget), which imposes compositional constraints to be satisfied by the parameters (i.e. $\\boldsymbol{M}_1\\cdot \\boldsymbol{M}_2\\approx \\boldsymbol{M}_3$). In this paper we investigate a dimension reduction technique by training relations jointly with an autoencoder, which is expected to better capture compositional constraints. We achieve state-of-the-art on Knowledge Base Completion tasks with strongly improved Mean Rank, and show that joint training with an autoencoder leads to interpretable sparse codings of relations, helps discovering compositional constraints and benefits from compositional training. Our source code is released at [github.com/tianran/glimvec](https://github.com/tianran/glimvec)."
abstract_short = ""

# Featured image thumbnail (optional)
image_preview = ""

# Is this a selected publication? (true/false)
selected = false

# Projects (optional).
#   Associate this publication with one or more of your projects.
#   Simply enter the filename (excluding '.md') of your project file in `content/project/`.
projects = []

# Links (optional).
url_pdf = "http://aclweb.org/anthology/P18-1200"
url_preprint = "https://arxiv.org/abs/1805.09547v1"
url_code = "https://github.com/tianran/glimvec"
url_dataset = ""
url_project = ""
url_slides = "slides/example-slides"
url_video = ""
url_poster = ""
url_source = ""

# Does the content use math formatting?
math = true

# Does the content use source code highlighting?
highlight = true

# Featured image
# Place your image in the `static/img/` folder and reference its filename below, e.g. `image = "example.jpg"`.
[header]
image = ""
caption = ""

+++

Equal contribution from first two authors.
