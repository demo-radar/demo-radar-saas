# Demo Radar

**Making public information useful in everyday life.**

> This repository is being prepared as a public overview of Demo Radar.
> It is private while this draft is reviewed. The application source will stay private.

Demo Radar turns Berlin's public demonstration records into a searchable map.
It helps residents and visitors explore causes, find gatherings, and understand
their times, locations and reported routes. People can use that information to
decide where they want to participate and which areas they would rather avoid.

**[Explore the live application](https://demo-radar.com)** ·
**[Read the DemoLogic project](https://github.com/demo-radar/demo-radar-logic)** ·
**[See the architecture](docs/architecture.md)**

## Why I built it

Public records can be available without being easy to use. A long list of
assembly titles and addresses is hard to explore, especially when a slogan
needs context or a route is described as text.

I built Demo Radar to make that information easier to navigate. The project
also revealed work behind the map: checking locations, repairing routes,
understanding slogans and choosing consistent topic tags. That became the
starting point for DemoLogic, the AI research project and service alongside
the application.

## What the application does

| Area | Implemented capability |
| --- | --- |
| Discover events | Search and filter demonstrations by date and topic on a map and in a list. |
| Understand locations | Show assembly points, clustered markers and available route geometry. |
| Read across languages | Offer German/English interface text and DeepL translations of titles and daily briefings. |
| Explore topics | Show saved topic tags, with AI suggestions subject to staff approval. |
| Read a daily briefing | Display a reviewed, published briefing when one is available for the selected day. |
| Maintain the information | Give authorized staff tools to inspect source records, review suggestions and correct locations or routes. |

These are descriptions of the application, not features you can run from this
repository. This repository contains documentation only. It has no map demo,
application source, production records or deployment commands.

## Where DemoLogic fits

**DemoLogic — Figuring out what Berlin's demos have to say, one slogan at a time.**

DemoLogic compares rules, classical text classifiers, neural language models
and hosted language models. Its service suggests topic tags and drafts daily
briefings. Demo Radar saves those results and provides the review workflow;
staff decide what is published. Eligible saved answers and explicitly approved
tagging rules can be reused for recurring events.

The public [DemoLogic repository](https://github.com/demo-radar/demo-radar-logic)
contains the service, research methodology, aggregate findings and limitations.
The application owns event records, permissions, translations and publication.
Browsing approved content does not require a new model call.

## Engineering behind the map

- **Turning text into geography:** interpret addresses and route descriptions,
  use mapping services, and provide a correction workflow for uncertain results.
- **Keeping maps readable:** cluster points, reveal route detail at useful zoom
  levels, and distinguish overlapping paths.
- **Making AI reviewable:** save suggestions before publication, retain review
  decisions, and reuse eligible work instead of repeatedly asking a model.
- **Handling uncertain failures:** check for a saved answer after a connection
  problem and require an explicit recovery decision when the outcome is unknown.

These are implementation choices, not claims of perfect accuracy or measured
time savings. The [architecture overview](docs/architecture.md) explains the
service boundaries and documented hosting setup.

## Hosting and source

The application is hosted on Hetzner infrastructure. The documented production
setup uses Cloudflare Tunnel for the public entry point and Docker for the
application services. DemoLogic calls the configured model provider; DeepL
handles translation. GitHub hosts this documentation and the public DemoLogic
source, not the Demo Radar application.

Application development and deployment remain in a private Gogs repository.
The frontend/backend implementation, operational configuration and research
inputs are outside this documentation repository. No application source licence
or right to reuse private code is granted by this overview.

## Limits and next steps

Demo Radar is an independent project. It uses demonstration records published
by Berlin's assembly authority; it is not an official authority channel.
Source information and map geometry can be incomplete, delayed or incorrect.
Topic tags describe a demonstration's subject, not individual participants'
beliefs, and do not verify claims made in a slogan.

A reported route is not confirmation of a road closure, crowd size or safety
condition. Automated disruption alerts, expansion to other cities, and measured
reductions in staff effort are development or evaluation goals, not established
outcomes. This overview does not assert legal or accessibility certification.

Next, I want to learn from residents and civic organizations, evaluate usability
and accessibility, and develop the mobile clients. A future project article can
explain a real engineering decision in more detail without publishing private
source or operational information.

**Author: Roy Grossman.** Third-party services and source material retain their
own rights. Documentation and image permissions are to be agreed before release.
