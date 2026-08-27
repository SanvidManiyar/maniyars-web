---
title: "Self-Hosting n8n with Docker and Portainer"
description: "A practical starting architecture for running n8n in a container with Portainer, persistent storage and room to grow."
pubDate: "Aug 25 2026"
heroImage: "/blog-placeholder-2.jpg"
---

n8n is one of the tools I am spending more time with because it sits in an interesting space between traditional integration development and AI-powered automation.

For learning and experimentation, self-hosting it is attractive. You control the runtime, data location, upgrades and surrounding infrastructure, and you can connect it to services that may not be exposed publicly.

A simple way to start is to run n8n in Docker and manage the containers through Portainer.

## Why Docker and Portainer

Docker gives n8n an isolated, repeatable runtime. Portainer adds a convenient web interface for managing containers, images, volumes, networks and stacks.

For a small VM or home-lab environment, this combination is easy to understand and flexible enough to expand later.

A basic setup usually includes:

- a Linux VM
- Docker Engine
- Portainer
- an n8n container
- persistent Docker volumes
- a reverse proxy or secure tunnel for external access
- regular backups

## Persistent storage is essential

The n8n container itself should be disposable. Your workflow definitions, credentials and application state should not be.

That means mounting persistent storage for the n8n data directory. If the container is recreated during an upgrade, the data should remain intact.

For a simple test environment, n8n can start with its default database. For a more serious deployment, I would normally prefer PostgreSQL so the application state is separated from the container lifecycle and easier to manage operationally.

## Do not expose the editor carelessly

The n8n editor can contain credentials and powerful integrations, so it should not simply be exposed to the internet on an open port.

A safer pattern is to put a secure access layer in front of it. Depending on the environment, that may be:

- Cloudflare Tunnel with access controls
- a reverse proxy with TLS
- VPN access such as Tailscale
- an identity-aware proxy

The important principle is that administrative access should be deliberate and protected.

## Separate inbound webhooks from administration

One design I like is to think about the n8n editor and public webhook endpoints separately.

The editor is an administrative surface. Webhooks are application endpoints. They have different security requirements.

Even if they initially share the same n8n instance, designing with that distinction in mind makes it easier to add authentication, rate limits, API gateways or dedicated ingress rules later.

## Backups matter more than the container

A container can be recreated quickly. Rebuilding workflows and credentials cannot.

At minimum, I would back up:

- the n8n persistent volume
- the PostgreSQL database if used
- Docker Compose or Portainer stack definitions
- environment configuration stored outside source control

Backups should also be tested. A backup that has never been restored is only an assumption.

## Where this can grow

A small n8n deployment can gradually become part of a much broader automation platform.

For example, workflows can call Azure APIs, trigger serverless functions, interact with databases, process webhooks, invoke AI models or coordinate multi-step business processes.

The architecture can also grow to include PostgreSQL, Redis queue mode, multiple workers, central logging and stronger secret management if workload or reliability requirements increase.

For my own experiments, the goal is to start simple, understand each layer and only add infrastructure when there is a clear reason for it.

That is generally the approach I prefer for automation platforms as well as cloud architecture: simple first, observable always, and scalable when required.
