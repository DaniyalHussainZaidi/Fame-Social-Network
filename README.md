## Fame Social Network

A Django based social network inspired by Twitter, with one big twist: a feature called Fame. Instead of likes, followers, and opinions, Fame tracks what a user actually knows and how accurate they've been about it.

Fame profiles don't care about your political views, your browsing habits, or what you bought last week. They only track your skills, both the good side (real expertise) and the bad side (spreading misinformation on a topic). Skills are organized in a simple hierarchy, for example Django under Databases under Computer Science.

## Project Overview

The project is built from two Django apps that work together.

`socialnetwork` handles the usual social media stuff: login, following people, posting, timelines, search, and an internal API layer that keeps the logic separate from the HTML and REST views.

`fame` manages the reputation side. Every user has a Fame profile made of expertise areas, and each one has a fame level. This can range from something strongly positive like Super Pro down to something clearly negative like Bullshitter.

These two apps are connected inside `famesocialnetwork`, where every post gets analyzed automatically and used to update the author's Fame profile over time.

## Core Concepts

**Fame Profiles**
A record of your skills based on what you actually post, not what you claim to know.

**Negative Fame**
If you post something false or misleading, your fame in that topic goes down. The post isn't just deleted, it becomes part of your track record.

**Communities**
Once your fame in a topic reaches Super Pro or higher, you can join that topic's community. If your fame drops below that, you get removed automatically.

**Similarity Scoring**
Users get compared based on how similar their fame levels are across shared topics, which helps surface people with similar skills or interests.

**Bullshitter Rankings**
A ranked list, per topic, showing which users have the worst fame there, ordered by how bad it is and how recent.

## Key Features

User login, following, and timelines
Text posts with automatic topic detection
Fame based publishing rules, so your reputation affects what you can post
Two timeline modes: standard and community
User similarity search based on fame overlap
REST API using Django REST Framework, plus regular HTML views
Fake data generation through custom management commands for testing locally

## ML Truth Detection and Adversarial Evasion System

Before a post gets published, it runs through a small truth checking pipeline in `socialnetwork/ml_engine.py`, built using scikit learn.

It uses TF IDF combined with Logistic Regression to estimate how likely a post is to be spam or low quality. Based on set thresholds, each post gets labeled TRUE, BULLSHIT, or UNKNOWN if the model isn't confident enough either way. That label then feeds straight into the Fame system, so a post flagged as bullshit actually hurts the author's fame in that topic.

### Adversarial Evasion Module

To see how easy it is to fool the classifier, the project also includes an adversarial evasion module. This simulates the kind of tricks people actually use to dodge automated moderation:

Benign word injection, where harmless words like thanks or meeting get added to the text to shift it away from looking spammy.

Leetspeak substitution, swapping letters for similar looking symbols such as a to @, e to 3, i to !, o to 0, s to $, t to 7, and c to (.

Zero width character injection, hiding invisible unicode characters inside words to split them apart without changing how the text looks.

This module is only meant for testing. It helps measure how much the classifier's accuracy drops under these kinds of attacks, and points toward future fixes like normalizing text before tokenizing it, adversarial training, or adding a second verification step. It's not meant to be used to actually dodge moderation in a live system, it's a way to test and improve the model.

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
