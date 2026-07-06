## Fame Social Network

A Django-based social network inspired by Twitter, extended with **Fame** — a
skill-based reputation system that replaces likes, follower counts, and
opinion-driven metrics with a transparent, evolving record of what a user
actually knows and does well.

Instead of profiling users by preferences, political leanings, or browsing
habits, Fame profiles track **demonstrated expertise** across a taxonomy of
topics (e.g. `Django → Databases → Computer Science`), including both
positive fame (skill, competence) and negative fame (repeated
misinformation, "bullshitting") in a given area.

## Project Overview

This project combines two Django apps into a single platform:

- **`socialnetwork`** — the core Twitter-like functionality: user
  authentication, following, posts, timelines, search, and an internal API
  layer (`api.py`) that keeps business logic independent of the HTML and
  REST views.
- **`fame`** — the reputation layer. Every user has a Fame profile made up
  of hierarchical expertise areas, each with a fame level ranging from
  strongly positive (e.g. `Super Pro`) to strongly negative (e.g.
  `Bullshitter`).

The two apps are wired together in `famesocialnetwork`, where a post's
content is automatically analyzed, classified, and used to adjust the
author's Fame profile over time.

## Core Concepts

- **Fame Profiles** — A per-user taxonomy of skills, built from actual
  behavior (what you post, how accurate it is) rather than
  self-declaration.
- **Negative Fame** — Posting inaccurate or misleading content lowers a
  user's fame in the relevant expertise area instead of simply being
  deleted or hidden — it becomes part of the user's public track record.
- **Communities** — Once a user reaches a high fame level (`Super Pro` or
  above) in a topic, they can join a dedicated community for that topic.
  Falling below that threshold automatically removes them.
- **Similarity Scoring** — Users can be compared based on how closely
  their fame levels align across shared expertise areas, enabling
  discovery of like-minded / similarly skilled users.
- **Bullshitter Rankings** — A ranked, per-topic view of users with the
  most negative fame, ordered by severity and recency.

## Key Features

- User authentication, following, and timelines
- Text-based posts with automatic expertise-area detection
- Fame-aware publishing: posts are blocked or penalized based on the
  author's existing reputation in the relevant topic
- Standard and community-based timeline modes
- User similarity search based on fame profile overlap
- REST API (Django REST Framework) alongside server-rendered HTML views
- Fake data generation via custom Django management commands, for local
  development and demoing

## ML Truth Detection & Adversarial Evasion System

Posts aren't taken at face value. Before publishing, each post is run
through a lightweight **truth-classification pipeline**
(`socialnetwork/ml_engine.py`) built with `scikit-learn`:

- A **TF-IDF + Logistic Regression** pipeline estimates the probability
  that a post's text is low-quality / spam-like ("bullshit").
- Based on configurable probability thresholds, each post is labeled
  `TRUE`, `BULLSHIT`, or `UNKNOWN` (when the model isn't confident enough
  either way).
- This verdict feeds directly into the Fame system: posts flagged as
  bullshit affect the author's fame in the detected expertise area,
  exactly like a real automated moderation/reputation system would.

### Adversarial Evasion Module

To stress-test the classifier's robustness, the project includes an
**adversarial evasion simulator** — a set of techniques designed to probe
how easily the truth-detection model can be fooled, mirroring
real-world evasion tactics seen on social platforms:

- **Benign word injection** — Appending innocuous, neutral phrases (e.g.
  "thanks", "regards", "meeting") to shift the model's TF-IDF signal away
  from spam-like patterns.
- **Leetspeak substitution** — Randomly replacing letters with
  visually-similar symbols (`a → @`, `e → 3`, `i → !`, `o → 0`, `s → $`,
  `t → 7`, `c → (`) to break tokenizer pattern matching.
- **Zero-width character injection** — Inserting invisible Unicode
  zero-width space characters (`\u200b`) inside words to split tokens
  without any visible change to the text.

This module exists purely as a **research and evaluation tool**: it lets
the team measure how much the classifier's accuracy degrades under
adversarial input, and informs future hardening of the truth-detection
pipeline (e.g. text normalization before tokenization, adversarial
training, or ensemble verification). It is not used to evade moderation
in production — it's a testing harness for the model itself.


## Preliminary

You need [pipenv](https://pipenv.pypa.io/en/latest/) to run the project. For the recommended installation, please run the following command on your machine:
```
pip install --user pipenv
```

If you are using Windows as OS, you should use the PowerShell to install pipenv and
run the project.

You don't necessarily need the Docker container of the lecture.

## Installation

Install the project using pipenv in the directory where the `Pipfile` resides. The command
```
pipenv install
```
will create a virtual environment and install the required dependencies. Note that we use
Python3.12, you may also specify the applied Python installation explicitly using
```
pipenv --python /path/to/python3.12 install
```

When using an IDE like PyCharm you want to make sure that that virtual environment is used for the project.

Then
```
pipenv shell
```
will activate that virtual environment.

After working on the project, the virtual environment can be deactivated again using the
```
exit
```
command.

## Unit Tests

To run the unit tests in the virtual environment, use the following command:

```
python manage.py test
```

Recall to disable the failing tests and enable them one by one to see the failing tests. 
Note that the tests use the fixture `database_dump.json`.

## Server

To run the server in the virtual environment, use the following command:
```
python manage.py runserver
```

You can access the server in a browser of your choice under http://127.0.0.1:8000/. To stop the server again press `Ctrl-C`.

## Models, Database, and Fake Data

Use the script
```
recreate_models_and_data.sh
```
to recreate the migrations, database, fake data and fixtures.
