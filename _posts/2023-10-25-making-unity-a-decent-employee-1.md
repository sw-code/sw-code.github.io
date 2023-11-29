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
> You
hi jeff, have you received 
the RX C# script i sent you?

> Jeff
yup

> You
does it work?

> Jeff
it was already in the project. in `scripts/rx.cs`

> You
nice. is it the same script?

> Jeff
yes, it is the same script. no bugs

> You 
alright, thansk :)
```

What a great friday. Time for a beer!
The weekend passes like a breeze, and you're happy to present your progress on monday.

On Sunday night, you open the project once again, just to feel the satisfaction again. But then you notice something weird... you realize something is horribly wrong! Fouriously, you open the chat in the glorios teams app.

```md
> You
JEFF

> You
JEFF!!!!

> Jeff
what?

> You
JFEFF THE SCRIPT IS NOT THE SAME

> Jeff
the script is the same, look at `scripts/rx.cs`

> You
BUT IT'S NOT THE SAME CODE

> Jeff
of course it's not the same code

> You 
WHAT?? I ASKED YOU ON FIRDAY
YOU SAID ITS THE SAME!!!1

> Jeff
it's the same script file name. 
i didn't check the code inside. 
you didn't tell me to compare each line in the file!!?

> You have blocked 'Jeff (jefferson_magnotastic_x3000)'. 
> Unblock to see their new messages.
```

### WTjeFf?!

You take a deep breath. Isn't that wonderful. What a bold move, Jeff.

Think about it for a second: If your human colleague was doing what Unity does, you'd place a few pieces of raw fish and meat in their curtain poles and let it rot over months. Well, at least I would.

Let's shift perspectives: What would you have done in Jeff's position?

![graphic to make them pause and actually think about it](TODO)

## Why are you like this, C#?
Did you know? Pretty much all of the features that your favorite language recently added were already invented in the 70s, or even earlier.

Yes, this includes C#: Records, Tuples, Non-Nullable, Pattern Matching, Immutability, Ranges. All of these concepts are multiple __centuries__ old.
Unfortunately, now it's too late to glue those things on top of the outdated core. C# is only about 20 years old, but it still has [a lot of cruft from Java (and by extension from C)](https://eev.ee/blog/2016/12/01/lets-stop-copying-c/).

LISP is one of the oldest programming languages, being 60 years old, yet it hat a lot of features. Some might say, it has __all__ the features. There is a saying that every programming language, adding more and more features, eventually converges into a (likely slightly buggy) version of Lisp. Or so. [Here's an inspiring read that might make you want to learn Lisp.](http://paulgraham.com/avg.html)

But before we have a look at how and why to make C# a better Lisp, we gently start with some easy Unity code, for our comfort.

# Why so series
This blog post is part of a series: Making Unity a decent employee. Well, at least a little less horrible, I admit.

These posts are largely independent. However, you should go read the [introduction here](TODO) if you haven't done that yet, and then come back. Otherwise, you might be thinking "wtf, why are we doing all of this again?" when reading the wonderfully ridiculous code sections later in this post.

> Disclaimer: I'm working in a rather unconventional project environment. For example, we embed a Unity View into an existing Android/iOS App. That's because we at [SWCode](https://swcode.io) build an app that uses AR, called [SoesTour](https://www.so-ist-soest.de/de/tourismus/sehenswertes/soestour.php). The app aims to revive historical sites that long vanished, by digitally showing them at their exact locations in the real world. Due to this complicated setup, I had to touch with some nasty Unity bits that most people might not have to touch. I want this article to be generally applicable, so don't worry about it. It might be fun either way!

Here's where we are at:

The Four Strategies
-------------------
(Copied from Introduction)
<!-- TODO copy from introduction -->


# Strategy I: Make it your own, make it your home!

"Give me 6 hours to chop down a tree and I’ll spend the first four sharpening the axe." - [probably not Abrahalm Lincoln](https://www.velaction.com/sharpen-the-axe/).

This article is the equivalent of choosing and sharpening the right axe for the job. Some might say it's boring, but without it, everything that follows will take a lot longer. We will explore some simple helpers to write and debug the real code faster. 

At the end of this blog post, you might almost feel like coding in a simpler C#, made just for your project. You should feel at home in your code.

Some of the code in this post might seem weird or unnecessary to you. That's because this code has been developed with a Clean Code mantra. At SWCode, we declare our Coding Bible be [Clean Code by Bob C. Martin](https://a.co/d/e3EVrb3). Even though I don't agree with every little detail, it's still a great book, and I recommend every coder to read it. 

#### Ye be warned
<!-- remove this seciton? just not important? -->
This post contains a whole lot of code. I hope that's why you're here. 
While this code is used in production, it is by no means exhaustive, and will fail in other contexts. That's why I won't publish it as a library at this time. By only showing you snippets, I hope to inspire you to write your own helper code. You can copy and paste from this blog post, but you might have to insert some special cases for your project.

Also, this article might contain some opinionated advice. Of course, you should use only what you find useful, as you see fit. If you think I'm absolutely wrong about something, quickly slash together a furious comment to explain why.

In this post
------------
<!-- TODO: collect all the headlines into a table of contents, if possible with anchors -->
(All the Headlines)

## Raising the Level of Abstraction

Working on a higher level allows you to make changes quickly. Move the code around like a sculptor moves clay. Change the large-scale behaviour without touching a single loop. Be the GOD in Age of Empires, not the little guy slamming a pile of rubbish with a hammer until it becomes a tower. Stay in control.

Why are we doing this? Let me convince you with an example that compares two different approaches of the same algorithm.

```cs

```

```cs

```


<!-- ## Create your high level Domain, instead of fiddling with unnecessary details all the time -->

The topic of your game is your "domain". But another domain you code in is the spatial world, so you should make it effortless to code. For example, add units for real world distances.

- Add strongly typed measurement units such as Seconds or Metres
- Add stronlgy typed quantities such as Angles, so you never have to write `Mathf.Pi*2` again ever. Also, why didn't you use `Mathf.Deg2Rad`?
- Add strongly typed paths instead of using string-based paths. I'll get to that later. It's fantastic, trust me.

### Extension Methods
How often do you attempt to use an API in Unity and think: "Gosh, I wish there was just this one slightly different function instead of this mess!"? C# has a wonderuful feature called [extension methods](https://www.tutorialsteacher.com/csharp/csharp-extension-method), and they allow us to make this place our own. They don't even have any kind of performance overhead!

Use Extension functions extensively. I mean it!
We're going to take whatever Unity currently is, and make it be what we actually needed.
Extend the LINQ, extend Unity, extend System.File, extend everything!!!1


### Five Reasons to use Extension Methods
<!-- or -->
### Top 20 Extension Methods in our Unity Project that have proven their value

Let's start with some simple quality of life improvements. Some of them do as little as aliasing an existing function. 
You might disagree about the necessity of these helpers. I think they improve the code I write significantly, without a lot of effort, because it allows the code to read like prose and less like maths.

#### Extension Methods can be Suggested by Your Editor
There is another indispensable big advantage of extension function: They are easier to discover than static helper functions.
For example `array.ToStringSeparated(", ")` will be suggested by your editor, whereas you will have to remember `string.Join(array, ", ")` manually before typing. And while typing, you don't have to go back to the start of the expression. Furthermore, appending a method call is much more flexible than surrounding your possibly complex expression with a static call.
<!-- dedup -->
Extension methods allow us to use the dot-based notation, instead of having to remember which class they are implemented in.
Why didn't they make these helpers an extension method in the first place?
<!-- maybe because extension methods didnt exist back then? -->

```cs
// example usage:
prefabPaths.Concat(scenePaths).ToStringSeparated("\n");
linePointsArray.Fill(transform.position);

if (this.audioFilePath.IsNullOrEmptySpace())
    Debug.LogWarning("Annotation does not contain any audio.");

if (gameObject.name.ExactlyEndsWith("%ß"))
    Debug.Log("Found it!");

// -- the extension method defined somewhere else --

public static string ToStringSeparated<T>(this IEnumerable<T> items, string separator = ", ") => 
    string.Join(separator, items);

public static bool IsNullOrEmpty(this string value) =>
    string.IsNullOrEmpty(value);

public static bool IsNullOrEmptySpace(this string value) => 
    string.IsNullOrWhiteSpace(value);  // includes empty strings

public static void Fill<T>(this T [] array, T value) => 
    Array.Fill(array, value);
    
public static int IndexOf<T>(this T [] array, T item) => 
    Array.IndexOf(array, item);

public static bool ExactlyEndsWith(this string value, string suffix) =>
    // you don't want to spell this out every time, do you?
    // ordinal is the only correct method for "programmer strings" if you want exact matches
    // because you wouldn't want it to behave differently in japan or sweden
    value.EndsWith(suffix, StringComparison.Ordinal); 
```


#### Extension Methods can fix a badly named method
The `GetDirectChildren` extension method has a rather clear meaning. In contrast, the implicit iteration over a transform object doesn't explain a lot of what it does.

```cs
// without extension method (the explicit variable type `Transform` is required for the C# compiler)
foreach(Transform transform in gameObject.transform)
    SceneTests.CheckGameObjectForMissingPrefabs(transform.gameObject);

// with our method:
gameObject.GetDirectChildren()
    .ForEach(SceneTests.CheckGameObjectForMissingPrefabs);


// -- the extension method defined somewhere else --

public static IEnumerable<Transform> GetDirectChildren(this Transform transform) => 
    // the transform can enumerate all its direct children,
    // but it implements IEnumerable<object>,
    // so we need to cast each child 
    transform.Cast<Transform>();

public static IEnumerable<GameObject> GetDirectChildren(this GameObject gameObject) => 
    gameObject.transform.GetDirectChildren().Select(childTransform => childTransform.gameObject);
```

When was the last time you had to lookup the documentation for `Transform` and `TransformInverse`, because you might mix them up? Just imagine if another developer has to read your code. Will they also have to look it up? No! Rename it!

```cs

// -- the extension method defined somewhere else --

public static Vector3 ChildToWorldPoint(this Transform transform, Vector3 worldPoint) =>
    transform.TransformPoint(worldPoint);

public static Vector3 WorldToChildPoint(this Transform transform, Vector3 worldPoint) =>
    transform.InverseTransformPoint(worldPoint);

public static Quaternion ChildToWorldRotation(this Transform transform, Quaternion worldRotation) =>
    transform.rotation * worldRotation; // FIXME TODO unit test pls!!!!<33

public static Quaternion WorldToChildRotation(this Transform transform, Quaternion worldRotation) =>
    Quaternion.Inverse(transform.rotation) * worldRotation; // FIXME TODO unit test pls!!!!<33
```


#### Extension Methods can handle `null`
One little advantage extension methods have over normal methods is that they can work with the annoying `null` value.
This is especially useful for functions like `Equals` or `ToString`, where null is an expected value.
You might have noticed this in the earlier example mentioning `bool IsNullOrEmptySpace(this string value)`. 

```cs
// example. anchor will never be null
var anchor = this.GetAnchorOrNull().ThrowIfDead("uploading anchor");

// -- the extension method defined somewhere else --

    
/// Checks whether the `UnityObject` is alive and not null. Throws an exception otherwise.
public static T ThrowIfDead<T>(this T unityObject, string subjectDescription = "instance") where T: UnityEngine.Object => 
    unityObject.IsAlive() ? unityObject : throw new MissingReferenceException(
        typeof(T).Name + " " + subjectDescription + " is not set or not alive anymore"
    );

/// Enumerate this value if it is present. If it is null, enumerate nothing.
public static IEnumerable<T> EnumerateIfNotNull<T>(this T value) where T: class => 
    value != null ? value.Enumerate() : Enumerable.Empty<T>();

/// Enumerate this value if it is present. If it is null, enumerate nothing.
public static IEnumerable<T> EnumerateIfHasValue<T>(this T? unityObject) where T: struct => 
    unityObject.HasValue ? unityObject.Value.Enumerate() : Enumerable.Empty<T>();
        
```

#### Extension Methods can improve the Signal to Noise Ratio
Functions are the single most important programming primitive. 
They can even be surpass than built-in syntax constructs.
For example, the `ForEach` helper function allows us to use method references,
which is more concise, once you are used to the thought. This way,
your code looks more like pseudo code, which is great, because
it allows you to make high-level decisions instead of wrestling with all the details.

```cs
// without `ForEach`. means the same, but with more visual noise.
public static void DeleteFiles(this IEnumerable<Path> files) {
    foreach(var file in files)
        file.DeleteFile();
}

// with `ForEach`
public static void DeleteFiles(this IEnumerable<Path> files) =>
    files.ForEach(Paths.DeleteFile);


// -- the extension method defined somewhere else --

/// Do something for every element in the enumerable.
/// Executes the action immediately, consuming the enumerable. 
public static void ForEach<T>(this IEnumerable<T> enumerator, Action<T> action) {
    foreach (var element in enumerator) 
        action(element);
}
```

Similarly, I often found myself negating a condition in a filter clause.
Having a negated version `ExceptWhere` in addition to `Where` allows us to use method references again.
```cs
// raw logic
importedAssetPathStrings
    .Where(pathString => !string.IsNullOrWhiteSpace(pathString))
    .Select(Paths.Parse)
    .ToArray();

// meaningful phrases
importedAssetPathStrings
    .ExceptWhere(string.IsNullOrWhiteSpace)
    .Select(Paths.Parse)
    .ToArray();

// -- the extension method defined somewhere else --
public static IEnumerable<T> ExceptWhere<T>(this IEnumerable<T> values, Func<T, bool> discardIf) =>
    values.Where(value => discardIf(value).Not());
```

Which one was easier to read? Speak for yourself.


Simple abstractions that convey a high-level meaning instead of just "doing the thing".
This prevents simple typos and is easier to read, almost like prose.
When tracking down a bug, which version do you have to read more carefully: The raw logic or the meaningful word?
```cs
// raw logic
if (settings.bundledAssetsPaths.Length == 0)
    GUILayout.Label("(None)");

// meaningful
if (settings.bundledAssetsPaths.IsEmpty())
    GUILayout.Label("(None)");

// -- the extension method defined somewhere else --

public static bool IsEmpty<T>(this ICollection<T> list) => list.Count == 0;
public static bool IsEmpty<T>(this T[] list) => list.Length == 0;

// similar, but sometimes less clear:
public static bool None<T>(this IEnumerable<T> elements) => elements.Any().Not();
public static bool None<T>(this IEnumerable<T> elements, Func<T, bool> condition) => elements.Any(condition).Not();
```

How often did you attempt to use `Math.Max` to impose an upper range to your float value? BUG! Here's a more concise helper:
```cs
transform.scale = originalScale.WithY(
    cylinderHeight.Max(2.0f).Min(0.01f)
);

// -- the extension method defined somewhere else --

public static int AtLeast(this int value, int min) => Mathf.Max(value, min);
public static int AtMost(this int value, int max) => Mathf.Min(value, max);

public static Vector3 WithY(this Vector3 vec, float y) => new Vector3(vec.x, y, vec.z);
public static Vector3 WithZ(this Vector3 vec, float z) => new Vector3(vec.x, vec.y, z);
public static Vector2 WithoutZ(this Vector3 vec) => new Vector2(vec.x, vec.y);

public static Color WithAlpha(this Color col, float a) => new Color(col.r, col.g, col.b, a);

```
By the way, if you were startled by the indentation of the parentheses, read [another one of my blog articles](https://johannesvollmer.com/2017/dont-be-bracist/).


Here's a few more extension methods that are used across our project:
<!-- TODO: Find an example for each of those or throw the rest away -->
```c#


/// Converts points to the same coordinate-space as `transform.localPosition`.
public static Vector3 WorldToSiblingPoint(this Transform transform, Vector3 worldPoint) =>
    transform.parent ? transform.parent.InverseTransformPoint(worldPoint) : worldPoint;

/// Converts points from same coordinate-space as `transform.localPosition` to world space.
public static Vector3 SiblingToWorldPoint(this Transform transform, Vector3 siblingPoint) =>
    transform.parent ? transform.parent.TransformPoint(siblingPoint) : siblingPoint;

public static Transform FindRoot(this Transform transform) =>
    transform.parent ? transform.parent.FindRoot() : transform;

public static GameObject FindRoot(this GameObject subject) =>
    subject.transform.FindRoot().gameObject;
    
public static Vector3 Lerp(this Vector3 self, Vector3 other, float factor) =>
    Vector3.Lerp(self, other, factor);

    
[NotNull]
public static T ExpectComponent<T>(this GameObject gameObject, string context = null) where T: class =>
    gameObject.GetComponent<T>().ThrowIfNull(() =>
        $"Component of type {typeof(T).Name} cannot be found in GameObject {gameObject.name}. " + (context ?? "")
    );
    
[NotNull]
public static T ExpectComponentInChildren<T>(this GameObject gameObject, string context = null) where T: class =>
    gameObject.GetComponentInChildren<T>().ThrowIfNull(() =>
        $"Component of type {typeof(T).Name} cannot be found in children of GameObject {gameObject.name}. " + (context ?? "")
    );

public static void DestroyComponent<C>(this GameObject gameObject) where C: Component => 
    Object.Destroy(gameObject.GetComponentOrNull<C>());

    
public static Task DebugLogExceptionsInAsync(UnityEngine.Object context, Func<Task> asyncOperation) =>
    DebugExtensions.DebugLogExceptionsInAsync(context, () => asyncOperation().ReturnNull());

public static async Task<T> DebugLogExceptionsInAsync<T>(UnityEngine.Object context, Func<Task<T>> asyncOperation) {
    try { return await asyncOperation(); }
    catch (Exception exception) {
        Debug.LogError($"Unhandled {exception.GetType().Name} in async call", context);
        Debug.LogException(exception, context);
        throw;
    }
}


public static T LogAs<T>(this T self, string prefix)
    => self.Log(subject => $"{prefix}: `{subject.DataToString()}`");

public static T LogNameAs<T>(this T self, string prefix) where T: UnityEngine.Object
    => self.Log(subject => $"{prefix}: `{subject.name}`\n({subject.DataToString()})");

public static Scene LogNameAs(this Scene self, string prefix) 
    => self.Log(subject => $"{prefix}: `{subject.name}`\n({subject.DataToString()})");

public static T Log<T>(this T self, Func<T, string> toString)
    => self.Do(subject => Debug.Log(toString(subject).WithDebugStackTrace(3), subject as UnityEngine.Object));


/// To be used as a chained method, where the
/// `!` operator would have to be prepended to the whole expression,
/// possibly even requiring parentheses.
public static bool Not(this bool value) => !value;

/// Perform a statement and then returns itself.
/// This allows us to do something with an object,
/// and then chain another method call.
/// The builder pattern often utilizes this pattern.
/// This way we only have to create a new variable
/// and add curly braces if we really want to.
public static T Do<T>(this T self, Action<T> statement)
{
    statement(self);
    return self;
}


public static string LocationInHierarchy(this GameObject gameObject) {
    if (gameObject.IsAlive().Not())
        return "[invalid game object]";
    
    var parent = gameObject.transform.parent;

    var parentLocation = parent
        ? parent.gameObject.LocationInHierarchy()
        : gameObject.scene.name;
    
    return parentLocation + " > " + gameObject.name;
}

/// Returns null if the unity object does not exist anymore.
/// Calling this on a unity object allows us to use the `?` and `??` operators.
/// Does not throw an exception if it is already null.
// TODO rename to something shorter
public static T OrNullIfDead<T>(this T unityObject) where T: UnityEngine.Object => 
    unityObject.IsAlive() ? unityObject : null;


public static string ThrowIfNullOrEmptySpace(this string nullable, string errorMessage) {
    if (nullable.IsNullOrEmptySpace()) throw new NullReferenceException(errorMessage);
    return nullable;
}

/// Checks whether the object is null. Does not check whether the `UnityObject` is alive! 
public static T ThrowIfNull<T>(this T nullable, string objectName) where T: class => 
    nullable.ThrowExceptionIfNull(() => new NullReferenceException(objectName + " is absent"));


public static bool HasValue<T>(this T? nullable, out T value) where T:struct {
    value = nullable.GetValueOrDefault();
    return nullable.HasValue;
}

public static T OrThrow<T>(this T? nullable, string errorWhenMissing) where T: struct =>
    nullable ?? throw new NullReferenceException(errorWhenMissing);
    

/// Checks whether the `UnityObject` is alive and not null. Throws an exception otherwise.
public static T ThrowIfDead<T>(this T unityObject, string subjectDescription = "instance") where T: UnityEngine.Object => 
    unityObject.IsAlive() ? unityObject : throw new MissingReferenceException(
        typeof(T).Name + " " + subjectDescription + " is not set or not alive anymore"
    );

    
/// Includes a null check.
public static bool IsAlive(this UnityEngine.Object value) => value;

public static bool IsNullOrEmpty(this string value) => string.IsNullOrEmpty(value);

public static bool IsNullOrEmptySpace(this string value) => 
    value.IsNullOrEmpty() || string.IsNullOrWhiteSpace(value);

public static string OrNullIfEmpty(this string value) => value.IsNullOrEmpty() ? null : value;

/// Returns an enumerable of all the root objects of all the Scenes currently open in the hierarchy.
/// Does not include the `DontDestroyOnLoad` pseudo scene.
public static IEnumerable<GameObject> AllDestroyOnLoadRootObjects() => 
    SceneExtensions.AllActiveDestroyOnLoadScenes()
        .SelectMany(scene => scene.GetRootGameObjects());


public static Scene? OrNullIfInvalid(this Scene sceneOrInvalid) =>
    sceneOrInvalid.IsValid() ? (Scene?) sceneOrInvalid : null;


public static IEnumerable<T> GetComponentsInChildren<T>(this Scene scene) where T: Component =>
    scene.GetRootGameObjects().SelectMany(root => root.GetComponentsInChildren<T>());




public static bool ExactlyEquals(this string value, string other) =>
    object.ReferenceEquals(value, other) /*includes null == null */ || (
        value != null && value.Equals(other, StringComparison.Ordinal)
    );


/// Usage: 2.Clamp(-1, 3);
/// Throws an `ArgumentOutOfRangeException` where max and min contradict.
public static int Clamp(this int value, int inclusiveMin, int inclusiveMax) {
    // TODO use c#8 Range instead of throwing manually
    if (inclusiveMax < inclusiveMin) throw new ArgumentOutOfRangeException(
        nameof(inclusiveMax), $"{value} cannot be clamped to the empty range [{inclusiveMin}, {inclusiveMax}]"
    );

    return value.AtLeast(inclusiveMin).AtMost(inclusiveMax);
}



```



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
- Want to concatenate two path strings? __Don't forget to use__ `Paths.Combine(folder, file)` method instead of a simple string concatenation.
- Want to check whether a file is in a specific folder? Too bad, there's nothing, mate. Better not rely on `Substring` for such an operation.

These path/string fiddling issues turned into bugs more often than I'd like to admit. One day, it was one bug too much for me. I created a strongly typed and well-tested `Path` class, none of those bugs ever came again.

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

- the scene opens as expected (haha) 
- the scene doesn't load, and an exception is thrown (ha)
- the scene doesn't load, and an invalid scene is returned, forcing you to check `isValid()`
- the scene doesn't load, bUT A VALID EMPTY SCENE IS RETURNED???? ARE YOU FOR REAL?

Wat.

Unity, are you serious? You know how long it took for me to find out that my scene files actually were correct, but Unity just pretendet that everything is okay? It took a whole day. And the solution now implies that the asset database needs to be reloaded after every single scene, wich unfortunately we only have a few of.

For additional fun, the Asset Database knows exactly whether it currently is in this suspended mode, but it WILL NOT TELL YOU. The docs even tell you that you must keep track of it on your own. This means that the script has no way of detecting whether the copy will actually work. Fantastic.


### Expressive Logging

Imagine you have the following code (using a custom `Path` class):
```cs
var folder = assetBundleManifestPath
    .AbsoluteOsPathToAssetPath()
    .Parent();
```

Now you want to log it, for whatever reason. With the tools C# and Unity give us, the following code would have been necessary:
```cs
// this variable exists only for logging and is never used afterwards
var absoluteManifestPath = assetBundleManifestPath.AbsoluteOsPathToAssetPath();
Debug.Log($"manifest path: {absoluteManifestPath}");

var folder = absoluteManifestPath.Parent();
Debug.Log($"bundles path: {folder}");
```

Using the `LogAs()` extension method, quickly logging a value is a breeze, and is much less obtrusive.
```cs
var folder = assetBundleManifestPath
    .AbsoluteOsPathToAssetPath().LogAs("manifest path")
    .Parent().LogAs("bundles path");
```

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
<!-- TODO: MORE MEMES! -->

#### C#'s `Equals()` is... useless
<!-- this is the hidden jeff section. first letter of the header is first letter of the secret code. -->

Especially if you don't like writing unit tests, you should try to make writing tests as painless as possible. A good way to write tests is to use the following form:

```
assertEquals(
    theFunction(exampleInput), 
    expectedOutput
)
```

In C#, this might look like this:

```cs
[Test]
void SortsSmallIntegers() => Expect.Equals(
    new int[]{ 4, 0, -5, 0 }.Sorted(), 
    new int[]{ -5, 0, 0, 4 }
)
```

If you say: "well my code doesn't look like that", then you will need to start writing code that looks like `Sorted`. Move your logic into [pure](https://betterprogramming.pub/what-is-a-pure-function-3b4af9352f6f) helper functions. I know this is hard - don't give up. Those pure functions are easier to test. The more logic you are able to move there, the more logic you can test easily.

> Quick Tip: Put your tests as close as possible to your source code. In Rust, for example, we can put our tests in __the same file as our logic__. This is amazing! Things related to each other should be close to each other. 

Back to topic. You know what? I lied!! The C# example above will fail. See, in C#, arrays are only considered equal if you give it the **same array twice, not if you have two arrays with the same content**. This being the default is ... slightly questionable.

But even outside of tests, in your runtime code, you will want to compare equality all over the place. 
I cannot understate how much of a fundamental language feature is missing here. Seriously. <!-- make it a joke? --> 

What are our options? Code up a new for loop each time we want to compare two arrays? That's just infuriating! Adding `Equals` implementations to your classes? That's error prone! Even if your IDE can generate that once, how do you know that anyone modifying the class will not forget to update that method?

Calling an external method? Won't work if the array is inside another class you don't control, for example in another List. Furthermore, it will only work in specific circumstances, for example it won't work for nested arrays.

My rule of thumb, coming from a UX Design standpoint and professional experience, is this:
If it's possible to 'forget' to do a thing, someone WILL forget to do that sooner or later. That's 
just how humans work. Gosh, I just wish I had [some kind of machine]((https://eev.ee/blog/2016/12/01/lets-stop-copying-c/)) that would automatically take care of such simple repetitive tasks for me!


#### The Code Solution
Here's what I did to improve the testing situation:

- Unlock `a.Equals(b)` for all types using Reflection
- Unlock `a.ToString()` for all types using Reflection

Let's have a look at how to start to implement generic equality using Reflection. We create an extension functions, called `DataEquals`. The name emphasizes that this implementation is meant for data classes, not for complex inheritance hierarchies.

In modern C#, we would probably use Records, but Unity lags behind the official C# standard, and they definitely will not change their own types to Records. Also, arrays still don't implement the common equality interface.

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
    
    /// The objects types must be exactly the same for this function to ever return true, no inheritance respected!
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

    /// If the type implements equals, we call it, otherwise compare property by property.
    /// For structs with auto generated equals type, we also compare field by field: Their generated equality method will not compare float using approximate equality (lol thanks). 
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
    
    /// Our last resort - compare field by field, using DataEquals for each field again.
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

This implementation does not attempt to be the definitive silver bullet. For example, it will never consider a derived object equal any base object, except for collections. In fact, the intention of this method is to be used with plain old data types, such as arrays and simple classes, that have public fields, and not too much wizardry. It will also recurse indefinitely for circula data structures. It has not been a problem in our project, as we try to avoid inheritance anyways.

Neat bonus: Because this is an extension function, it can also be called on null, so you never have to worry about that again:
```cs
object nothing = null;
nothing.DataEquals((object)5); // works not as expected, but works as hoped for: just returns false
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

This function is also used for runtime assertions in our code. Performance did not pose to be a problem yet. Using `IL2CPP` in Unity, this could in theory compile down to something more efficient, as the compiler knows all the types at compile time (which is not the case in interpreted .NET code, where more code could always be loaded at runtime).

The `DataToString` function works similarly, but I won't torture you with more code. You can have a look [here](TODO). 
<!-- @osca can we publish this code? -->
