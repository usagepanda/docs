---
title: Security
layout: default
nav_order: 7
---

# Security
Usage Panda is operated with a minimal infrastructure footprint in a secure cloud environment and follows application security best practices. It is designed to separate the proxy (the component inspecting OpenAI API requests) from its other SaaS application components and request and response details are only logged if you opt _in_.

## Infrastructure Architecture
Usage Panda is hosted on Amazon Web Services (AWS) in the us-east-1 region and leverages AWS compute, storage, database, and CDN services. Cloud security best practices (minimizing public resource exposure, secure VPC network configuration, encryption in transit and at rest, etc.) are followed.

## Data Access
Usage Panda only has visibility into OpenAI requests that are sent through the proxy. Only metadata is logged, unless you opt into logging request and response details for more visibility or debugging. Usage Panda never logs, saves, or stores your OpenAI API key.

## Billing
All account billing is managed by our PCI-compliant third-party payment provider, Stripe. Usage Panda does not have visibility into user payment account numbers.

## Responsible Disclosure
If you discover a security concern with Usage Panda, please email `hello@usagepanda.com` with a detailed description of the issue. We will respond within 24 hours.