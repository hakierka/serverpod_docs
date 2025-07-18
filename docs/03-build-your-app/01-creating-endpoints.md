---
sidebar_label: 1. Creating endpoint methods
---

# 🍳 Creating Endpoint Methods

Let’s get cooking! In this tutorial, we’ll prepare a **Recipe Generator** – your very first Serverpod-powered backend method. Think of your server as a kitchen and each endpoint like a station – it takes in ingredients (data), works its magic (logic), and serves you a dish (a response) back in your app.

We’ll walk you through:
- Creating a project
- Setting up an endpoint
- Talking to an external API (Google Gemini)
- Calling the method from Flutter

Let’s start mixing the ingredients!

:::info
On the server, you can do things you don't want to do in the app, like calling an API secured by a secret key or accessing a database. The server can also do things that are impossible in the app, like sending push notifications or emails.
:::

## 🥣 Set Up Your Kitchen (Create a Project)

Run this to scaffold your workspace:

```bash
serverpod create magic_recipe
```

This creates three packages:
- `magic_recipe_server`
- `magic_recipe_client`
- `magic_recipe_flutter`

:::tip
Always open the root folder in your IDE to ensure smooth navigation between components.
:::

## 🧂 Add Ingredients (Gemini API)

1. Get a free Gemini API key from [Google AI Studio](https://aistudio.google.com/app/apikey)
2. Add it to your server secrets config:

```yaml
# config/passwords.yaml
# This file is ignored by Git

development:
  gemini: '--- Your Gemini API Key ---'
```
3. Add Gemini as a dependency:

```bash
cd magic_recipe/magic_recipe_server
dart pub add google_generative_ai
```

## 🔪 Prep Your Station (Create an Endpoint)
An **endpoint** is like a named door into your backend — you open it from the app to get something done on the server.

A **method** inside the endpoint is the specific action you want to run – in our case, “generate a recipe.”

Serverpod will auto-generate the API layer so you can call these methods as if they were local functions in Dart.

Let’s create a `RecipeEndpoint` with a method `generateRecipe()` that calls Google’s Gemini API to cook up a recipe.


### Create the File
Create a file at:

```txt
magic_recipe_server/lib/src/recipes/recipe_endpoint.dart
```
:::info
This is where you will define your endpoint and its methods. With Serverpod, you can choose any directory structure you want to use. E.g., you could also use `src/endpoints/` if you want to go layer first or `src/features/recipes/` if you have many features.
:::

Add this code:

<!--SNIPSTART 01-getting-started-endpoint-->
```dart
import 'dart:async';

import 'package:google_generative_ai/google_generative_ai.dart';
import 'package:serverpod/serverpod.dart';

/// This is the endpoint that will be used to generate a recipe using the
/// Google Gemini API. It extends the Endpoint class and implements the
/// generateRecipe method.
class RecipeEndpoint extends Endpoint {
  /// Pass in a string containing the ingredients and get a recipe back.
  Future<String> generateRecipe(Session session, String ingredients) async {
    // Serverpod automatically loads your passwords.yaml file and makes the passwords available
    // in the session.passwords map.
    final geminiApiKey = session.passwords['gemini'];
    if (geminiApiKey == null) {
      throw Exception('Gemini API key not found');
    }
    final gemini = GenerativeModel(
      model: 'gemini-1.5-flash-latest',
      apiKey: geminiApiKey,
    );

    // A prompt to generate a recipe, the user will provide a free text input with the ingredients
    final prompt =
        'Generate a recipe using the following ingredients: $ingredients, always put the title '
        'of the recipe in the first line, and then the instructions. The recipe should be easy '
        'to follow and include all necessary steps. Please provide a detailed recipe.';

    final response = await gemini.generateContent([Content.text(prompt)]);

    final responseText = response.text;

    // Check if the response is empty or null
    if (responseText == null || responseText.isEmpty) {
      throw Exception('No response from Gemini API');
    }

    return responseText;
  }
}
```
<!--SNIPEND-->
:::info
For methods to be generated, they need to return a typed `Future`, where the type should be `void` `bool`, `int`, `double`, `String`, `UuidValue`, `Duration`, `DateTime`, `ByteData`, `Uri`, `BigInt`, or  [serializable models](../06-concepts/02-models.md). The first parameter should be a `Session` object. You can pass any serializable types as parameters, and even use `List`, `Map`, or `Set` as long as they are typed.
:::

### Generate the Code

```bash
cd magic_recipe/magic_recipe_server
serverpod generate
```

This step does a lot behind the scenes:
- Registers your endpoint so the server knows about it
- Generates typed client code so you can call it from Flutter

:::note
When writing server-side code, in most cases, you want it to be "stateless". This means you want to avoid using global or static variables. Instead, think of each endpoint method as a function that does stuff in a sub-second timeframe and returns data or a status message to your client. If you want to run more complex computations, you can schedule a [future call](../06-concepts/14-scheduling.md), but you usually shouldn't keep the connection open for longer durations. The `Session` object contains all the information you need to access the database and other features of Serverpod. It is similar to the `BuildContext` in Flutter.
:::

## 🍽️ Serve It to the App (Call from Flutter)

Now that you have created the endpoint, you can call it from the Flutter app. Do this in the `magic_recipe_flutter/lib/main.dart` file. Rename the `_callHello` method to call your new endpoint method to `_callGenerateRecipe`. It should look like this; feel free to just copy and paste:

<!--SNIPSTART 01-getting-started-flutter-->
```dart
class MyHomePageState extends State<MyHomePage> {
  /// Holds the last result or null if no result exists yet.
  String? _resultMessage;

  /// Holds the last error message that we've received from the server or null if no
  /// error exists yet.
  String? _errorMessage;

  final _textEditingController = TextEditingController();

  bool _loading = false;

  void _callGenerateRecipe() async {
    try {
      setState(() {
        _errorMessage = null;
        _resultMessage = null;
        _loading = true;
      });
      final result =
          await client.recipe.generateRecipe(_textEditingController.text);
      setState(() {
        _errorMessage = null;
        _resultMessage = result;
        _loading = false;
      });
    } catch (e) {
      setState(() {
        _errorMessage = '$e';
        _resultMessage = null;
        _loading = false;
      });
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(widget.title),
      ),
      body: Padding(
        padding: const EdgeInsets.all(16),
        child: Column(
          children: [
            Padding(
              padding: const EdgeInsets.only(bottom: 16.0),
              child: TextField(
                controller: _textEditingController,
                decoration: const InputDecoration(
                  hintText: 'Enter your ingredients',
                ),
              ),
            ),
            Padding(
              padding: const EdgeInsets.only(bottom: 16.0),
              child: ElevatedButton(
                onPressed: _loading ? null : _callGenerateRecipe,
                child: _loading
                    ? const Text('Loading...')
                    : const Text('Send to Server'),
              ),
            ),
            Expanded(
              child: SingleChildScrollView(
                child: ResultDisplay(
                  resultMessage: _resultMessage,
                  errorMessage: _errorMessage,
                ),
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```
<!--SNIPEND-->

## 🔥 Fire Up the Stove (Run the Server)

:::tip
Before you start your server, ensure no other Serverpod server is running. Also, ensure that Docker containers from other Serverpod projects aren't running to avoid port conflicts. You can see and stop containers in the Docker Desktop app.
:::

Let's try our new recipe app! First, start the server:

```bash
$ cd magic_recipe/magic_recipe_server
$ docker compose up -d
$ dart bin/main.dart --apply-migrations
```
## 🧁 Taste Test (Run the App)
Now, you can start the Flutter app:

```bash
$ cd magic_recipe/magic_recipe_flutter
$ flutter run -d chrome
```

This will start the Flutter app in your browser:

![Example Flutter App](/img/getting-started/endpoint-chrome-result.png)

Enter ingredients, click the button, and get a custom recipe served directly from your own Dart server.

## 🍽️ What’s Next?

Right now, you're returning a plain string from the backend. Next, we’ll show you how to use a custom model – a structured object with a title, steps, ingredients, and more.

[Go to Models & Data →](./models-and-data.md)
