---
title: Get Started
sidebar_position: 1
sidebar_label: 🚀 Get Started
---

## Creating a new project

To create a new Serverpod project, use the `serverpod create` command. It will set up a new project with a server, a client, and a Flutter app.
The project will be created in a new directory with the name you specify. For example, to create a new project called `my_project`, run the following command:

```bash
$ serverpod create my_project
```

:::tip
The name of the project must be a valid Dart package name. It should start with a lowercase letter and can only contain lowercase letters, numbers, and underscores. For example, `my_project` is a valid name, but `MyCounter` is not.
:::

To run your new project you must first start the database from the Docker file that is included with the project. Do this by running the `docker compose up` command in the server directory:

```bash
$ cd my_project/my_project_server
$ docker compose up
```

This will start the PostgreSQL database. You can stop the database server by pressing `Ctrl+C` in the terminal. If you want to run the servers in the background, you can use the `-d` flag:

```bash
$ docker compose up -d
```

This will start the database server in detached mode, meaning it will run in the background and you can safely close the terminal window without stopping it. Stop the database container by running the following command from the server directory:

```bash
$ docker compose down
```

:::tip
If you are using Docker Desktop, you can see and manage all your installed Docker containers from there. It's easy to start and stop containers, and to remove the ones you are no longer using.
:::

Now that the database is up and running we can start the Serverpod server. Because we are running the project for the first time, we need to create the database tables used by Serverpod. This is done through a [database migration](./06-concepts/06-database/11-migrations.md). An initial migration is already created for us, so all we need to do is pass the `--apply-migrations` flag when starting the server:

```bash
$ cd my_project/my_project_server
$ dart run bin/main.dart --apply-migrations
```

This will start the server and set up the initial database tables. You can now access the server at `http://localhost:8080` and the web server is available at `http://localhost:8082`. It should look like this:

![Serverpod web](/img/getting-started/serverpod-web.png)

Now let's run our Flutter app. You can do this by running `flutter run -d chrome` in the flutter directory:

```bash
$ cd my_project/my_project_flutter
$ flutter run -d chrome
```

This will start the Flutter app in your browser. It should look like this:

![Example Flutter App](/img/getting-started/flutter-example-web.png)

## Next steps

The quickest way to learn Serverpod is to follow our 30-minute **[Build your first app](/docs/03-build-your-app/index.md)** tutorial. This will give you an excellent overview of creating endpoints and models and working with the database. You will create a fun app that magically creates recipes from the ingredients you have in your fridge.
