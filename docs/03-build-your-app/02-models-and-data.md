---
sidebar_label: 2. Models and data
---

# 🧬 Models and Data

In Serverpod, a **model** is like a recipe card for your app's data — a structured format that defines what a piece of data looks like and how it's handled between your server and client.

Think of it like this:
- You define your model once (like writing a recipe).
- Serverpod automatically generates all the boilerplate code to move that data between your backend and frontend (no need to rewrite your recipe every time).

We’ll show you how to define a model using YAML, generate Dart classes, and use those classes in your server and client code.

## 🧾 Step 1: Define Your Recipe Model

Let’s create a `Recipe` model that contains:
- Author name
- The full recipe text
- The date it was created
- The ingredients used

Create a file: `magic_recipe_server/lib/src/recipes/recipe.spy.yaml`

<!--SNIPSTART 02-typed-endpoint-model-->
```yaml
### Our AI generated Recipe
class: Recipe
fields:
  ### The author of the recipe
  author: String
  ### The recipe text
  text: String
  ### The date the recipe was created
  date: DateTime
  ### The ingredients the user has passed in
  ingredients: String
```
<!--SNIPEND-->

You can use most primitive Dart types here or any other models you have specified in other YAML files. You can also use typed `List`, `Map`, or `Set`. For detailed information, see [Working with models](../06-concepts/02-models.md)

## ⚙️ Step 2: Generate the Code

Run the Serverpod generator to create the Dart classes for your models:

```bash
$ cd magic_recipe/magic_recipe_server
$ serverpod generate
```
This will generate the code for the model and create a new file called `recipe.dart` in the `lib/src/generated` directory. It will also update the client code in `magic_recipe/magic_recipe_client` so you can use it in your Flutter app.

## 🧠 What’s a Model, Really?

A **model** is a plain Dart object that can be sent over the network or stored in the database. Serverpod takes care of:
- Serialization (turning the object into data that can travel)
- Deserialization (turning that data back into an object)
- Type safety between client and server

You can use primitive Dart types like `int`, `String`, `bool`, `DateTime`, or even collections (`List<String>`, `Map<String, int>`) and other models.
 
## 🛠 Step 3: Return the Model from Your Endpoint

Update your existing `RecipeEndpoint` so it returns a `Recipe` instead of just a `String`.

Update `lib/src/recipes/recipe_endpoint.dart`:

<!--SNIPSTART 02-typed-endpoint  {"selectedLines": ["4", "10-12", "39-48"]}-->
```dart
// ...
import 'package:magic_recipe_server/src/generated/protocol.dart';
// ...
class RecipeEndpoint extends Endpoint {
  /// Pass in a string containing the ingredients and get a recipe back.
  Future<Recipe> generateRecipe(Session session, String ingredients) async {
// ...
    final recipe = Recipe(
      author: 'Gemini',
      text: responseText,
      date: DateTime.now(),
      ingredients: ingredients,
    );

    return recipe;
  }
}
```
<!--SNIPEND-->

<details>

<summary>Click to see the full code</summary>
<p>

<!--SNIPSTART 02-typed-endpoint-->
```dart
import 'dart:async';

import 'package:google_generative_ai/google_generative_ai.dart';
import 'package:magic_recipe_server/src/generated/protocol.dart';
import 'package:serverpod/serverpod.dart';

/// This is the endpoint that will be used to generate a recipe using the
/// Google Gemini API. It extends the Endpoint class and implements the
/// generateRecipe method.
class RecipeEndpoint extends Endpoint {
  /// Pass in a string containing the ingredients and get a recipe back.
  Future<Recipe> generateRecipe(Session session, String ingredients) async {
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

    final recipe = Recipe(
      author: 'Gemini',
      text: responseText,
      date: DateTime.now(),
      ingredients: ingredients,
    );

    return recipe;
  }
}
```
<!--SNIPEND-->

</p>
</details>

Update our generated client by running `serverpod generate`.

```bash
$ cd magic_recipe/magic_recipe_server
$ serverpod generate
```
## 📱 Step 4: Use the Model in Flutter

Now that we have created the `Recipe` model we can use it in the client. We will do this in the `magic_recipe/magic_recipe_flutter/lib/main.dart` file. Let's update our `RecipeWidget` so that it displays the author and year of the recipe in addition to the recipe itself.

Update your client to reflect that the server now returns a full `Recipe` object.

In `magic_recipe_flutter/lib/main.dart`:

```dart
Recipe? _recipe;

void _callGenerateRecipe() async {
  try {
    setState(() {
      _errorMessage = null;
      _recipe = null;
      _loading = true;
    });
    final result =
        await client.recipe.generateRecipe(_textEditingController.text);
    setState(() {
      _errorMessage = null;
      _recipe = result;
      _loading = false;
    });
  } catch (e) {
    setState(() {
      _errorMessage = '$e';
      _recipe = null;
      _loading = false;
    });
  }
}
```

Then update your widget to display the result:

```dart
ResultDisplay(
  resultMessage: _recipe != null
    ? '${_recipe?.author} on ${_recipe?.date} says:\n${_recipe?.text}'
    : null,
  errorMessage: _errorMessage,
)
```
<details>

<summary>Click to see the full code</summary>
<p>

<!--SNIPSTART 02-typed-endpoint-flutter-->
```dart
class MyHomePageState extends State<MyHomePage> {
  // Rename _resultMessage to _recipe and change the type to Recipe.

  /// Holds the last result or null if no result exists yet.
  Recipe? _recipe;

  /// Holds the last error message that we've received from the server or null if no
  /// error exists yet.
  String? _errorMessage;

  final _textEditingController = TextEditingController();

  bool _loading = false;

  void _callGenerateRecipe() async {
    try {
      setState(() {
        _errorMessage = null;
        _recipe = null;
        _loading = true;
      });
      final result =
          await client.recipe.generateRecipe(_textEditingController.text);
      setState(() {
        _errorMessage = null;
        _recipe = result;
        _loading = false;
      });
    } catch (e) {
      setState(() {
        _errorMessage = '$e';
        _recipe = null;
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
                child:
                    // Change the ResultDisplay to use the Recipe object
                    ResultDisplay(
                  resultMessage: _recipe != null
                      ? '${_recipe?.author} on ${_recipe?.date}:\n${_recipe?.text}'
                      : null,
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

</p>
</details>
## ▶️ Step 5: Run Everything

Start your server:

```bash
$ cd magic_recipe/magic_recipe_server
$ docker compose up -d
$ dart bin/main.dart
```

Run the app:

```bash
$ cd magic_recipe/magic_recipe_flutter
$ flutter run -d chrome
```

This will start the Flutter app in your browser. It should look something like this:
![Flutter Recipe App](/img/getting-started/flutter-web-ingredients.png)

Click the button to get a new recipe. The app will call the endpoint on the server and display the result in the app.

## 🍰 Next Steps

Right now, our recipe lives only in memory. In the next step, we’ll learn how to store it in a real database using Serverpod’s ORM — no SQL needed!
