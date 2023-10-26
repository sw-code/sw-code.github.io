---
layout: post
title:  "The Unity Engine is your worst coworker - Here's strategy #1 to improve it"
date:   2023-10-25 10:30:00
categories: unity
tags: unity, csharp, workflow, testing, reliability
# image: /assets/article_images/unity/worst-colleague.png
author_name: Johannes Vollmer
author_link: /authors/johannes-vollmer
author_image: /assets/images/authors/johannes-vollmer/thumbnail.jpg
published: false
# target publish date: december
---

The sun creates beautiful orange patterns on your desk, as your project finally seems complete. Finally, it's done! Such a relief. Only one single thing to check:

```md
> you
hi jeff, have you received 
the RX C# script i sent you?

> jeff
yup

> you
does it work?

> jeff
it was already in the project. in `scripts/rx.cs`

> you
nice. is it the same script?

> jeff
it is the same script. no bugs

> you 
alright, thansk :)
```

What a great friday. Time for a beer!
The weekend passes like a breeze, and you're happy to present your progress on monday.

On Sunday night, you open the project once again. Shocked from the insights, you realize something is horribly wrong! Fouriously, you open the chat in the glorios teams app.

```md
> you
JEFF

> jeff
what?

> you
JFEFF THE SCRIPT IS NOT THE SAME

> jeff
the script is the same, look at `scripts/rx.cs`

> you
BUT IT'S NOT THE SAME CODE

> jeff
of course it's not

> you 
WHAT?? I ASKED YOU ON FIRDAY
YOU SAID ITS THE SAME!!!1

> jeff
it's the same script file name. i didn't check the code inside

> microsoft teams note:
it appears you are blocked in this chat
```

### WTjeFf?!

You take a deep breath. Isn't that wonderful. What a bold move, Jeff.

Think about it for a second: If your human colleague was doing what Unity does, you'd [REDACTED].

Let's shift perspectives: What would you have done in Jeff's position?

![graphic to make them pause and actually think about it](TODO)

## What could have been

The programming languages we use today largely stem from an older era than you might think.

Very few programming languages invent new features. Most of the features can be found in languages from the 70s, languages that have been lost in the meantime. In 2023, for example, many languages start to add features from the ML family of languages.

There is a saying that every programming language, adding more and more features, eventually converges into a slightly buggy version of LISP, which is 60 years old. Or so. [Here's an inspiring read for you that will make you want to learn LISP.](http://paulgraham.com/avg.html)

C, for example, is over 50 years old, but it is used in so many places. The language of Unity, C#, is only about 20 years old, but it borrowed a lot from older languages. The age shows in many situations. All the new shiny features they added cannot hide the numerous scars of a long life. But before we have a look at those, we gently start with some easy Unity code, for our comfort.

# Why so series
This blog post is part of a series: Making Unity a decent employee. Well, at least a little less horrible, I admit.

These posts are largely independent. However, you should go read the [introduction here] if you haven't done that yet, and then come back. Otherwise, you might be thinking "wtf, why are we doing all of this again?" when reading the wonderfully ridiculous code sections later in this post.

> Disclaimer: I'm working in a rather unconventional project environment. For example, we embed a Unity View into an existing Android/iOS App. That's because we at [SWCode](https://swcode.io) build an app that uses AR, called [SoesTour](https://www.so-ist-soest.de/de/tourismus/sehenswertes/soestour.php). The app aims to revive historical sites that long vanished, by digitally showing them at their exact locations in the real world. Due to this complicated setup, I had to touch with some nasty Unity bits that most people might not have to touch. I want this article to be generally applicable, so don't worry about it. It might be fun either way!

Here's where we are at:

The Four Strategies
-------------------
<!-- TODO copy from introduction -->


# I. Writter Better Code in Unity (duh!)
<!-- maybe this should be a new article? but let's not publish the teaser without at least one follow up article immediately -->
Ye be warned, this might contain some opinionated advice. If you know better, quickly slash together a comment to explain why.

At SWCode, we declare our Coding Bible be [Clean Code by Bob C. Martin](https://a.co/d/e3EVrb3). Even though I don't agree with every little detail, it's still a great book. A related website is [the cleancoders blog](https://cleancoders.com/blog). As you can see, clean coding is a widely covered topic. So I'll keep this section short and focused on Unity, I promise.

So, here's some examples for Unity-Specific clean code strategies:

## Raising the Level of Abstraction

Use Extension functions extensively. I mean it!
We're going to take whatever Unity currently is, and make it be what we actually needed.
Extend the LINQ, extend Unity, extend System.File, extend everything!

Working on a higher level allows you to make changes quickly. Move the code around like a sculptor moves clay. Change the large-scale behaviour without touching a single loop. Be the GOD in Age of Empires, not the little guy slamming a pile of rubbish with a hammer until it becomes a tower. Stay in control.

## Utilize basic C# language features because Unity doesn't

- Add helper functions that allow the use of high-level functional programming, for example LINQ.
- Embrace Exceptions instead of silent failure

- `allActiveChildren = gameObject.AllChildren().Where(IsActive)`

- `activeDirectChildren = gameObject.DirectChildren().Where(IsActive)`

- `allRootTransforms = scene.AllRootObjects().Select(root => root.ExpectComponent<Transform>())`

- `ALLRenderers = scene.AllObjects().Select(obj => obj.ExpectComponent<Renderer>())`


### Paths
Another feature missing from Unity is path handling. In our project, there is a ton of code that needs to deal with Assets, consequtively, paths. By default, simply Unity uses strings instead of a distinct path type. I say simply, because it looks easier at first, but writing code is actually harder that way. 

Problem:
- Want to concatenate two path strings? Use the verbose `Paths.Combine(folder, file)` method.
- Want to check whether a file is in a specific folder? Too bad, there's nothing, mate. Better not rely on `Substring` for such an operation.

These path/string fiddling issues turned into bugs more often than I'd like to admit. Until it was one bug too much for me. I created a strongly typed and well-tested `Path` class, none of those bugs ever came again.

I have looked for existing path libraries, but I couldn't find any specifically for Unity. Maybe I'm an idiot. Maybe none for this niche existed. On the other hand, this class precisely fills the need of handling Unity Assets, with all the neat Unity features built-in. It's not even perfect, it doesn't handle paths with driver letters and definitely not URLS, but Unity Assets don't use those, so it's not a big problem. What matters is that this new type reduced the number of path bugs I've had by 100%.

One use case where Path operations are requred, is automating the workflows you might do in the Editor by hand.
For example, see this wonderful function, that plays a role in assigning asset bundles to assets:
```cs
/// removes all assets except those in the specified folder from a given asset bundle 
private static void RestrictAssetBundleToFolder(string bundleName, Path assetBundleFolder) {
    EditorPaths.OfAssetsInBundle(bundleName)
        .ExceptWhere(asset => asset.IsChildOf(assetBundleFolder))
        .LoggingEachAs($"Removing asset outside of folder `{assetBundleFolder}` from bundle `{bundleName}`")
        .ForEach(EditorPaths.UnsetAssetBundle);
}
```

I hope you see how this code is on a whole new abstraction level, compared to the vanilla Unity code you would have to write. Let's see what makes this code possible: The custom Path class. Take a look at this piece of code, to that Unity simply does not provide an equivalent:

```cs
[Serializable]
public class Path {
    
    [SerializeField] 
    private string[] segments;

    // --- snip ---

    public static bool IsChildOf(Path maybeParent) => 
        maybeParent.IsParentOf(this);

    public bool IsParentOf(Path child) {
        var parent = this;

        if (child.segments.Length <= parent.segments.Length)
            return false;

        return child.segments.Take(parent.segments.Length)
            .SequenceExactlyEquals(parent.segments);
    }
}

/// used like this: pathStringFromUnity.ParsePath().IsChildOf(root)
public static Path ParsePath(this string path) => Path.ParseFilePath(path);
```


This class is easily testable, and tests there are. The consequence is, that I now could be confident in handling paths.
Any algorithm that uses stringy paths would have needed more testing, because all those string operations might contain a bug.

Of course, I didn't stop there. I my best to avoid handling stringy paths everywhere else. For example, by using the following helper functions:


```cs
#if UNITY_EDITOR
public static class EditorPaths {
    
    public static Path Assets = "Assets/".ParsePath();

    public static Path Packages = "Packages/".ParsePath();


    /// reads the file contents
    public static string ReadString(this Path path) =>
        File.ReadAllText(path.ToString());
    

    // --- finding assets in the unity editor ---

    public static IEnumerable<Path> FindProjectAssetsOfType<T>() 
        where T: UnityEngine.Object =>
            EditorPaths.FindProjectAssets("t:" + typeof(T).Name);

    public static IEnumerable<Path> FindProjectAssets(string filter) =>
        EditorPaths.FindProjectAssetsGuids(filter).GuidsToAssetPaths();
        
    public static IEnumerable<string> FindProjectAssetsGuids(string filter) =>
        EditorPaths.Assets.ChildAssetsGuids(filter);

    public static IEnumerable<string> ChildAssetsGuids(this Path path, string filter = null) =>
        AssetDatabase.FindAssets(filter, new []{ path.ToString() });


    public static void DeleteAsset(this Path asset) =>
        AssetDatabase.DeleteAsset(asset.ToString())
            .ThrowIfFalse("Could not delete the asset " + asset);

    // --- many, MANY more ---
}
```

> Are you interested in using this code? With a bit of luck, and if the comments show us that you want it, we might publish this as a library. 

### Exceptions

Throw Exceptions instead where Unity would return null, because this makes errors easier to track down and control flow easier to predict. Again, because errors are visible immediately, as soon as possible, and not some time later.

#### Scene Hierarchy
For example, these simple helper functions are so useful:
- `... = gameObject.ExpectFindInChildren<MyComponent>()`
- `... = gameObject.ExpectFindInParent<MyComponent>()`

#### Asset Handling
Look at this code from earlier:
```cs
public static void DeleteAsset(this Path asset) =>
    AssetDatabase.DeleteAsset(asset.ToString())
        .ThrowIfFalse("Could not delete the asset " + asset);
```

If something goes wrong deleting assets, I'd rather know immediately. Not some time later when seeing them in the assets window, when I thought they are gone.

Unity often returns booleans to indicate success. But it's easy to forget checking the value. If something can be forgotten, someone WILL eventually forget to do it. <!-- maybe make this a slogan to sing with all the children -->
It can get lost during refactoring. So we make use of the very recently added C# functionality called Exceptions, that is all the hotness right now.

#### Textures

```cs
// --- inside a helper function that loads a texture from bytes
var myTexture2D = CreateEmptyTexture();

myTexture2D.LoadImage(bytes)
    .ThrowIfFalse("Cannot load texture: " + disposableWebRequest.error);

return myTexture2D;
```

Sure, this allocates a string even if no errors happens. So what? I'll complicate it up, as soon as we need to load thousands of tiny textures, which is most lileky never. 

This code actually lives in a function that notifies you when the texture contents actually got loaded, using `UniRx`, which is the Unity version of the C# reactive extensions. We'll talk about that one later.

#### Scene Loading
The most spicy case of missing Exceptions in Unity must be scene loading. Let's load a scene using the well-designed `SceneManager`:

```cs
// attempt to load the scene
SceneManager.LoadScene("owo");
var scene = SceneManager.GetSceneByName("owo"); 
// FAILURE: this returns a real scene object, but it has no name and no path!
Debug.Log(scene.name); 
```

```cs
// LoadScene, as opposed to LoadSceneAsync, loads immediately,
// which means it loads on the next frame, obviously
SceneManager.LoadScene("owo");
yield return null; // wait 1 frame

// now we want to inspect the scene and find out whether loading was successfull:
var realScene = SceneManager.GetSceneByName("owo");
Debug.Log(scene.name); 
// FAILURE if the scene does not exist or didn't load somehow
// again, we get a scene object, but it has no name
```

When will you notice that it failed? I hope as soon as it happens, but who knows. Fortunately, the Unity developers have thought of this, and provided a neat little `scene.IsValid()` for us. Silly you just forgot to call it! It's your fault, you're just not good enough!!!1

I just wish there was a builtin C# language feature to indicate failure that won't silently continue if something goes wrong.

We are better than that. We code up our own helper functions.
```cs

public static Scene? OrNullIfInvalid(this Scene sceneOrInvalid) =>
sceneOrInvalid.IsValid() ? (Scene?) sceneOrInvalid : null;

public static Scene OrThrowIfInvalid(this Scene sceneOrInvalid, string message) =>
sceneOrInvalid.OrNullIfInvalid().OrThrow(message);
```

But don't just stop there! We go on:

```cs
static class SceneExtensions {
    
    public static Scene ExpectLoadedSceneByName(string name) => 
        FindLoadedSceneByNameOrNull(name)
                .OrThrow($"scene `{name}` is invalid or not loaded");
        
    public static Scene? FindLoadedSceneByNameOrNull(string name) => 
        SceneManager.GetSceneByName(name).OrNullIfInvalid();


    public static IEnumerator WaitForLoadScene(string scene, LoadSceneMode mode) {
        SceneManager.LoadScene(scene, mode);
        
        while (!FindLoadedSceneByPathOrNull(scene).HasValue) 
            yield return null;
    }

// ...there are more variants for loading by name or by build index ...
}
```

Actually, scene loading can be very complex. If you want to hear another story about it, have a look at [my other blog post about that](TODO).

#### Worst Case: Loading a Scene in an Editor Script
This is a true story. I was writing an script that opens scenes in the Editor. To make performance acceptable, I was using the `DataBase.StartAssetEditing()`, which avoids reloading the project after every individual file.

Later, deep down in the code, the script needed to copy a scene file. Why? Because Unity does not support opening scene files from an external package, or your own embedded package, so the only solution is to temporarily copy it to your assets folder.

[a clown developer](<!-- TODO insert image of a clown -->)

Anyways, that's [a different story](TODO). The script was trying to load the contents of the scene file that was just being copied. Quick quiz! What's going to happen?

- the scene loads opens as expected (haha) 
- the scene doesn't load, and an exception is thrown (ha)
- the scene doesn't load, and an invalid scene is returned
- THE SCENE DOESN'T LOAD, BUT A VALID EMPTY SCENE IS RETURNED

Wat.

Unity, are you serious? You know how long it took for me to find out that my scene files actually were correct, but Unity just pretendet that everything is okay? It took a whole day. And the solution now implies that the asset database needs to be reloaded after every single scene, wich unfortunately we only have a few of.

For additional fun, the Asset Database knows exactly whether it currently is in this suspended mode, but it WILL NOT TELL YOU. The docs even tell you that you must keep track of it on your own. This means that the script has no way of detecting whether the copy will actually work. Fantastic.


### Null Coalescing
Unity doesn't support the C# null coalescing operator (`?`), but we can code up our own, kind of.


Problem: 
Solution: Logging. Disclaimer: Use AR Foundation Remote or Unity Remote, and the Editor as much as possible!! Debug your running game in scene view! Use a debugger!

Problem: Null Reference Exception
Problem: 


Problem: 
Solution: Logging. Disclaimer: Use AR Foundation Remote or Unity Remote, and the Editor as much as possible!! Debug your running game in scene view! Use a debugger!

## Add obvious missing C# language features using black magic

Adding missing stuff to Unity is adventurous. Adding stuff to C# however is a pretty bold move. You think you know better than 10+ years of Microsoft's best Engineers? Well, I am, as you certainly already have guessed, based on my extensive use of high quality memes in this professional blog article series.

#### C# Equals is useless

Especially if you don't like writing unit tests, you should try to make it as painless as possible to write one. 

A good test looks as simple as this:

```cs
[Test]
void IsSorted() => Expect.Equals(
    new int[]{ 4, 0, -5, 0 }.Sorted(), 
    new int[]{ -5, 0, 0, 4 }
)
```

If you say: "well my code doesn't look like that", then you will need to start writing code that looks like `Sorted`. Move your logic into [pure](https://betterprogramming.pub/what-is-a-pure-function-3b4af9352f6f) helper functions. I know this is hard - don't give up.

But even then, there will still be more problems. See, in C#, arrays are only considered equal if you give it the **same array twice, not if you have two arrays with the same content**. This being the default is ... slightly questionable.

But even outside of tests, you will want to compare equality all over the place. 
I cannot understate how much of a fundamental language feature is missing here. Seriously. <!-- make it a joke? --> 

What are our options? Code up a new for loop each time we want to compare two arrays? That's just infuriating! Adding `Equals` implementations to your classes? That's error prone! Even if your IDE can generate that once, how do you know that anyone modifying the class will not forget to update that method?

My rule of thumb, coming from a UX Design standpoint and professional experience, is this:
If it's possible to 'forget' to do a thing, someone WILL forget to do that sooner or later. That's 
just how humans work. Gosh, I just wish I had a computer that would automatically take care of such repetitive tasks for me[!](https://eev.ee/blog/2016/12/01/lets-stop-copying-c/)



#### The Code Solution
Here's what I did to improve the testing situation:

- Unlock `a.Equals(b)` for all types using Reflection
- Unlock `a.ToString()` for all types using Reflection

```cs
public static class DataExtensions {

    /// Compares two objects for equality, treating both objects as data.
    /// Compares Arrays and other Collections based on content, not only reference, across subtypes.
    /// All other types are compared by their public members, but never across subtypes,
    /// and only if they do not override the `Equals` method.
    public static bool DataEquals<T>(this T self, T other) => 
        DataExtensions.ReferenceEqualsOr(
            (object)self, (object)other, 
            DataExtensions.NonNullDataEqualsUntyped
        );
    
    /// Subtypes are not considered equal. Also not equal for generic types with different type arguments.
    private static bool NonNullDataEqualsUntyped(object first, object second) {
        // note: this could compare across sub-typed collections
        if (first is IDictionary dictA && second is IDictionary dictB)
            return DataExtensions.NonNullDictEntriesEqualUntyped(dictA, dictB);

        // note: this could compare across sub-typed collections. this branch also handles arrays.
        else if (first is IEnumerable enumerableA && second is IEnumerable enumerableB)
            return enumerableA.Cast<object>().SequenceEqual(enumerableB.Cast<object>(), new DataEqualityComparer<object>()); // this comparer just calls a.DataEquals(b)

        else return DataExtensions.CompareExcludingInheritance(first, second);
    }

    // --- snip ---
}
```

Pretty straight forward so far. 
As you can see, this method also has another benefit: By using generics instead of the `object`, the compiler warns us, if we attempt to compare incompatible objects. This catches many obvious mistakes already at compile time:

```cs
var array = Array.Empty<int>();
var dict = new Dictionary<int, string>();

array.DataEquals(dict); // error: cannot infer common type
array.DataEquals((IEnumerable) dict.Keys) // being explicit works
```

Now we get to the meat of it, using Reflection:

```cs
public static class DataExtensions {

    // --- snip ---
    
    private static bool CompareExcludingInheritance(object first, object second) {
        var type = first.GetType();

        // any other types should not equal any subtype
        if (type != second.GetType())
            return false;

        if (type.IsPrimitive)
            return first.Equals(second); // this uses approximate equality for floats

        // if the type does not override ToString, iterate all public fields and properties
        return DataExtensions.EqualsOverriddenOrEqualProperties(first, second, type);
    }

    private static bool EqualsOverriddenOrEqualProperties(object first, object second, Type type) {
        var equalsMethod = type.GetMethod("Equals", new [] { typeof(object) });

        var subjectOverridesEquals = equalsMethod!.DeclaringType != typeof(object);
        var equalsMethodIsAutoGenerated = type.IsValueType;

        if (subjectOverridesEquals) {
            var isExactlyEqual = first.Equals(second);
            if (equalsMethodIsAutoGenerated) { // this is true for tuples and structs
                if (isExactlyEqual) return true; // fast forward if both structs are equal on a byte level
                
                // otherwise continue to compare field by field (required for comparing floats approximately inside value types)
                else return DataExtensions.PublicPropertiesEqualReflective(first, second, type);
            }

            else return isExactlyEqual;
        }

        else return DataExtensions.PublicPropertiesEqualReflective(first, second, type);
    }
    
    private static bool PublicPropertiesEqualReflective(object first, object second, Type type) {
        var fieldsMatch = type.GetFields(BindingFlags.Instance | BindingFlags.Public)
            .Select(field => field.GetValue(first).DataEquals(field.GetValue(second)));

        var propertiesMatch = type.GetProperties(BindingFlags.Instance | BindingFlags.Public)
            .Where(property => property.CanRead && property.GetMethod.IsPublic) // only properties with public accessor
            .Select(property => property.GetValue(first).DataEquals(property.GetValue(second)));

        return fieldsMatch.Concat(propertiesMatch).All();
    }
}
```

This implementation does not attempt to be the definitive silver bullet. For example, it will never consider a derived object equal any base object, except for collections. In fact, the intention of this method is to be used with plain old data types, such as arrays and simple classes. It has not been a problem in our project, as I try to avoid inheritance anyways.

Neat bonus: Because this is an extension function, it can also be called on null, so you never have to worry about that again:
```cs
object nothing = null;
nothing.DataEquals((object)5); // works as expected: just returns false
```

#### REFLECTION???
In case it wasn't clear until now, or if you've skipped previous articles:
Is it slow? Maybe. Not so slow that I needed to change it yet. Does it matter? No, it doesn't matter 99% of the time. Developer time is the single most valuable resource.

Only if you need to use this in the `Update` function, and you notice that your framerate drops, do something about it. If you really need that, you should probably override `Equals` for that type, and that type only!

#### Going Further

I also implemented a few custom testing helper methods on top of that:

```cs
public static class Expect {

    public static void Equal<T>(T expected, T actual, string context = null) => Expect.Condition(
        expected.DataEquals(actual),
        () => $"Expected `{expected.DataToString()}`, but it was `{actual.DataToString()}`",
        context
    );

    private static void Condition(bool passes, Func<string> assertion, string context) {
        if (!passes) Expect.FailAssertion(assertion(), context);
    }

    // --- snip ---
}
```

This function is also used for runtime assertions in our code. Performance did not pose to be a problem yet. Using `IL2CPP` in Unity, this could in theory compile down to something more efficient, as the compiler knows all the types at compile time (which is not the case in interpreted .NET code).

The `DataToString` function works similarly, but I won't torture you with more code. You can have a look [here](TODO). @osca can we publish this code?

## Create your high level Domain, instead of fiddling with unnecessary details all the time

- Add strongly typed measurement units such as Seconds or Metres
- Add stronlgy typed quantities such as Angles, so you never have to write `Mathf.Pi*2` again ever.
- Add strongly typed paths instead of using string-based paths. I'll get to that later. It's fantastic, trust me.


<!-- WTF Johannes, you promised to be focused on unity? we've seen this advice a thousand times

#### Raise the Level of Abstraction

Problem Statement: You find yourself fiddling with details in the code to handle all the special cases. Moving code around results in errors. You have a hard time understanding what you wrote a few days ago.


Don't be afraid to use abstractions because of performance cost. I'm saying this now because this question might also pop up later.

```c#
void Start(){
    for (int i = 0; i < elements.Length; i++){
        if (elements[i].isActive){
            elements[i].active = true;
            return;
        }

        Debug.LogError("failed");
    }
}
```

If, at the end of reading a complicated for loop, you think "aaah, this just finds the next active element in a list and then activates it", you should ask, why does the code hide this fact? Instead, try to put into code the words you just thought:

```c#
void Start() { 
    ActivateFirstElementOrThrow();
}

void ActivateFirstElementOrThrow() =>
    this.elements.First(IsInactive).Activate();

bool IsInactive(Element elem) => 
    !(elem.isActive);
```

But won't using LINQ allocations destroy your Performance? The answer is: most likely not. Only very few methods in your codebase will be called in a critical tight loops, yes, even in games. The answer you dont want to hear: The #1 resource is developer time. Build code that is flexible, and make it fast only when needed.

Similarly, initially use Lists instead of Arrays, as they are more flexible, and only replace if proven slow.

But we're not going to stop here. We're going to make the code our own!

Add a static extension function, which is a fantastic C# feature:
```c#
/// The first element that does NOT satisfy the predicate.
static IENumerable<T> FirstNotBeing<T>
    (this IENumerable<T> elements, Func<T, bool> predicate) =>
        elements.First(element => !predicate(element));
```

This function is now available on all your collections, use it everywhere in your Project.

Our old code becomes simpler:
```c#
void Start() { 
    ActivateFirstElementOrThrow();
}

void ActivateFirstElementOrThrow() =>
    this.elements.FirstNotBeing(Element.IsActive)
        .Activate();
```
The `bool IsActive()` should have been on the Element class all along. -->