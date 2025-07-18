---
title: Installation
sidebar_position: 0
sidebar_label: ⚙️ Installation
---


# ⚙️ Installation
To start building with Serverpod, you first need to install the required tools and the Serverpod CLI.


## ✅ Prerequisites
To start building with Serverpod, you first need to install the required tools and the Serverpod CLI.

### Flutter
Serverpod is tested on Mac, Windows, and Linux. Before you can install Serverpod, you need to have **[Flutter](https://flutter.dev/docs/get-started/install)** installed.

:::info
Check your Flutter installation by running the following command in your terminal:

```bash
$ flutter doctor
```

:::

### Docker

To make use of Serverpod's database connectivity, you need to have access to a PostgreSQL database. We recommend using Docker to run PostgreSQL locally. You can find instructions for installing Docker on the official **[Docker website](https://docs.docker.com/get-docker/)**. Each Serverpod project comes with its own `docker-compose.yaml`, so there is no need to install any custom containers. We will guide you through the process in the Getting Started section.

:::info
Check your Docker installation by running the following command in your terminal:

```bash
$ docker info
```

If you are using Docker Desktop and you get an error, make sure that Docker is running. You can check this by looking for the Docker icon in your system tray or taskbar. If it's not running, start Docker Desktop and try again.
:::

### Install Serverpod

Serverpod is installed using the Dart package manager. To install Serverpod, run the following command in your terminal:

```bash
$ dart pub global activate serverpod_cli
```

This command will install the Serverpod command-line interface (CLI) globally on your machine. You can verify the installation by running:

```bash
$ serverpod
```

If everything is correctly configured, the help for the `serverpod` command is now displayed.

### Install the VS Code extension (recommended)

The Serverpod VS Code extension makes it easy to work with your Serverpod projects. It provides real-time diagnostics and syntax highlighting for model files in your project.
![Serverpod extension](/img/syntax-highlighting.png)

You can **[install the extension](https://marketplace.visualstudio.com/items?itemName=serverpod.serverpod)** from the VS Code Marketplace or search for _Serverpod_ from inside VS Code.

### Install Serverpod Insights (recommended)

**[Serverpod Insights](./09-tools/01-insights.md)** is a companion app bundled with Serverpod. It allows you to access your server's logs and health metrics. Insights is available for Mac and Windows, but we will be adding support for Linux in the future.
![Serverpod Insights](https://serverpod.dev/assets/img/serverpod-screenshot.webp)

