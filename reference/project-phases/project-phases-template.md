# Project Phases

The project's current phase is stated at the top of `AGENTS.md` and must be kept up to date. Setting the right phase is primarily the developer's responsibility.


## 1: Concept

No code yet, only ideation: writing down the purpose of the application and its use cases, at a high level, with no implementation details unless necessary.


## 2: Planning

Planning the details: deciding the platforms, architecture, frameworks and technologies, such as which database to use, and the repo structure.

The decisions must be written down inside the project. Help the developer write down these decisions.


## 3: Early development

Implementing the application before initial deployment.
In this phase there are no active users, so there is no reason to be careful; ripping out and replacing anything (code, decisions, libraries etc.) is encouraged.

In this phase it is encouraged to make large refactors whenever an issue is structural in nature. Change the structure instead of patching a bad structure.
A bad structure will only cause issues in the long run.
Decisions from the previous phase are encouraged to change when a change in the concept requires it, when a decision turns out to be insufficient, or for any other reason.

Reduced security to improve development speed is ok, but it must be a written decision so it is not forgotten.

The principle here is that it is totally ok to rewrite **anything**, because that cannot hurt anyone yet. Large changes are encouraged here rather than later.


## 4: Pre-deploy

Critical tech debt needs to be resolved, and code quality must be confirmed.

The principle here is that actual users will soon use this application, and critical bugs must be caught so as not to make a bad impression from the start.


## 5: Deployed

The application is deployed with some actual users. Large refactors are not encouraged unless evidently necessary.
New intents (features, bug fixes, improvements etc.) should try to reuse code that is already written, to prevent creep.

Some refactors are ok, but then extra caution is needed not to create new bugs for the end user.

The principle here is that users have now generated data that needs special care not to mess up, and a new bug that accidentally gets created will make someone angry.
