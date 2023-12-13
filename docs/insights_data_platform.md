# Insights Data Platform

This page explains the data interfaces provided by [Elexon Insights](https://bmrs.elexon.co.uk).

You'll find high-level guidance targeted at developers, data engineers & architects looking to make use of Elexon data.

## Platform Context

Elexon Insights is a data platform that publishes real-time and historic data about the UK electricity grid and energy market. All data interfaces are freely accessible to industry and public, maintained by Elexon. 

The platform exists to meet (and exceed) Elexon's obligations to publish market data under the Balancing & Settlement Code.


## Data Interfaces

Insights is a multi-channel data platform, and every dataset can be accessed in multiple ways. Each channel exists to meet the needs of particular user types or downstream applications. The channels available are:

1. Insights Website
1. Insights API
1. Insights Real-time Integration Service (IRIS)
1. Insights Data Archive

### 1. Insights Website

The Insights Solution website (https://bmrs.elexon.co.uk) is the public face of Insights.

Website users can browse many BMRS datasets, complete with rich visualisations. The website supports data downloads directly from the page. Each webpage provides links to relevant API endponts for further data, and includes interactive API documentation.

The website is suitable for:
- Non-technical users
- Quick exploration of key datasets
- Ready-made dashboards
- Rich visualisations and context about the data

The website is NOT suitable for:
- Some API/IRIS-only datasets
- Real-time data
- Full historic data

You should never point a web scraper at the Insights website. The website is built on top of the public Insights API, which is fully documented and available for direct third-party integration.

### 2. Insights API

The Insights API provides REST-like interfaces for all datasets available on the Insights platform, including full access to historic data. The Insights API is publicly accessible and requires no authentication. It supports JSON and XML response formats.

API documentation is available in the following locations:
- Interactive API explorer on the Insights Website ([link](https://bmrs.elexon.co.uk/api-documentation/guidance))
- OpenAPI Spec File ([link](https://data.elexon.co.uk/swagger/v1/swagger.json))
- Plain SwaggerUI interface ([link](https://data.elexon.co.uk/swagger/index.html))

The interactive API explorer hosts additional guidance on using the API (see [guidance page](https://bmrs.elexon.co.uk/api-documentation/guidance)), including API-internal design patterns.

The Insights API is suitable for:
- Building downstream applications
- Custom dashboards
- One-off data fetching via scripts


The Insights API is NOT suitable for:
- Building time-critical downstream applications

### 3. Insights Real-time Integration Service (IRIS)

IRIS is an AMQP messaging service designed to supply downstream applications with reliable near-realtime data.

Integrating with IRIS is a relatively heavyweight solution generally only required by, and appropriate for, electricity market participants for whom data completeness and timeliness are mission-critical. An active IRIS subscriber receives live data from Insights in an event-driven, fault-tolerant manner at lower latency than any other channel.

Like the other channels, IRIS is accessible to both industry and public. To use IRIS you must:
- Register an account with the Insights website (https://bmrs.elexon.co.uk/iris)
- Retrieve connection details & credentials from the Insights website
- Deploy & connect an AMQP client application (see [examples](https://github.com/elexon-data/iris-clients))
- Refresh credentials before they expire

Registration and authentication are required so Elexon can provision, and authorise access to, user-specific cloud infrastructure and message queues. Authentication credentials expire on a schedule and will require rotation. IRIS is only recommended for users with the resources to run a 24/7 client application and handle secret rotation.

IRIS is suitable for:
- Mission-critical business applications
- Systems requiring near-zero latency on electricity market data

IRIS is NOT suitable for:
- Making adhoc data requests
- Historical data requests
- Lightweight data exploration

### 4. Insights Data Archive

The Insights Data Archive is cloud blob storage service that hosts a carbon copy of every JSON message sent to IRIS users. The archive is publicly readable and updated in near-real-time as IRIS messages are published.

The Data Archive is suitable for:
- Finding historical IRIS messages
- Inspecting the IRIS JSON schema
- Downloading bulk raw Insights data

The Data Archive is NOT suitable for:
- Finding specific data (use the API instead)
- Accessing the latest data (connect to IRIS instead)

## Migrations from legacy BMRS services

The table below maps legacy BMRS services to Insights data interfaces. 

| Legacy Service    | Insights Channel      |
|-------------------|-----------------------|
| BMRS Website      | Insights Website      |
| BMRS API          | Insights API          |
| Data Push Service | IRIS                  |
| TIBCO Service     | IRIS                  |
| TIBCO Relay       | Insights Data Archive |

Not: These are not plug-and-play migration routes. Insights has been build using modern design principles, data formats and communication protocols to deliver enhanced performance compared to Legacy services.