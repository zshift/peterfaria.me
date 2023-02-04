---
title: "F#"
date: 2023-02-04T12:39:25-05:00
draft: false
---

# F#

I started working at a job last year where we mainly use [F#][1]. It's a fairly unique language that takes some getting used to. After almost a year working in it, I've decided to start writing on my learning experience, in the hopes that future F# developers don't have to struggle as much as I did. I'd like to start with a more advanced topic, [Computation Expressions][2].

## Computation Expressions

Computation Expressions (CEs) are an incredibly useful feature of F#. They allow developers to write succinct code in a way that's easy to read, and importantly avoid long function callback chains (if you're familiar with promises in JavaScript, think back to the days of callback hell).

While many examples online--and even in Microsoft's own documentation--show you how to build a CE, they don't always show you what's happening under the hood, so that's what I'll focus on in this post. If you're looking for an introductory series, I highly recommend the [Computation Expression Series][3] by [F# For Fun and Profit][4]. 

## Demistifying CEs

The important thing to remember when trying to understand a CE, it's that they're just builders under the hood. In fact, CEs aren't even keywords, they are just an instance of a builder with a conventation based "interface"--wrapped in quotes, because there's no explicit interface definition. The functions and their behaviors are described [here][5].

Here's what the definitions of the [`task`][6] and [`backgroundTask`][6] CEs look like [(Source)][7].
```f#
let task = TaskBuilder()
let backgroundTask = BackgroundTaskBuilder()
```

Yup. That's it. 

## Breaking Down a Simple CE
Let's look at the first example from [Microsoft's documentation][2]

```f#
let fetchAndDownload url = async {
    let! data = downloadData url

    let processedData = processData data

    return processedData
}
```

If we were to write this same code without a CE, here's what it would look like

```f#
let fetchAndDownload url =
    downloadData url |> Async.map (fun data ->
        let processedData = processData data
        processedData
    )
```

It's clear to see from this example that the former is much easier to read, but that it's not all that complex to implement without the CE.

To show the real benefits, let's take a look at a much longer CE.

```f#
let downloadData () = task { return "data" }
let processData data = data
let notifySubscribers newData = task { () }
let checkForUpdates () = task { return true }
let installUpdates () = task { printfn "installing updates!" }

let doLotsOfStuff () = task {
    let! data = downloadData()

    let processedData = processData data

    do! notifySubscribers processedData

    let! hasUpdates = checkForUpdates()

    if hasUpdates then
        do! installUpdates()
}
```

And here's the closest "equivalent" of that without CEs that I could come up with

```f#
open System.Threading.Tasks

let downloadData () = Task.FromResult "data"
let processData data = data
let notifySubscribers newData = Task.FromResult ()
let checkForUpdates () = Task.FromResult true
let installUpdates () =
    printfn "Installing updates!"
    Task.FromResult ()

let doLotsOfStuff () =
    let dataTask = downloadData ()
    let nextTask = dataTask.ContinueWith (fun (dataTask: Task<string>) ->

        let data = dataTask.Result
        let processedData = processData data

        let notifySubscribersTask = notifySubscribers processData
        let innerTask = notifySubscribersTask.ContinueWith (fun (_: Task) -> 

            let updatesTask = checkForUpdates()
            let finalTask = updatesTask.ContinueWith (fun (updatesTask: Task<bool>) ->
                let hasUpdates = updatesTask.Result
                if hasUpdates then
                    installUpdates ()
                else
                    Task.FromResult ()
            )
            finalTask.Unwrap()
        )
        innerTask.Unwrap()
    )
    nextTask.Unwrap()
```

It should be obvious from the above example how much easier to read the former is when compared to the latter. Both return the same results and run to completion, outputing `"Installing updates!"`.

## Gotchas
CEs are only in scope for the blocks immediately within the CE (with a couple of exceptions, that we'll get to later in the post). For example, the following will _not_ compile.

```f#
let fetchAndLog url = task {
    let! results =
        let! data = downloadData url
        let processedData = processData data
        return processedData

    log.Info "Retrieved {count} results." (results |> Seq.length)

    return results
}
```

You'll see the following error: 

```
              let! data = downloadData url
  ------------^

error FS0750: This construct may only be used within computation expressions
```

The reason we see this error is because we began a new block underneath `let results =`, thus creating a new scope. In order to use the benefits of the CE, we'll have to invoke it again.

```f#
let fetchAndLog url = task {
    let! results = task {
        let! data = downloadData url
        let processedData = processData data
        return processedData
    }

    log.Info "Retrieved {count} results." (results |> Seq.length)

    return results
}
```

Generally, though, we want to avoid such code, as it can start becoming hard to read. The recommendation here would be to extract the logic of the nested scope out to a separate function. 

```f#
let downloadAndProcess url = task {
    let! data = downloadData url
    let processedData = processData data
    return processedData
}

let fetchAndLog url = task {
    let! results = downloadAndProcess url
    log.Info "Retrieved {count} results." (results |> Seq.length)
    return results
}
```

By the same token, you cannot mix and match CEs with each other. Once you open a new CE, the previous CE moves out of scope.

For example

```f#
let fetchAll urls = task {
    let! results = seq {
        for url in urls do
            let! data = downloadData url
            let processedData = processData data
            yield processedData
    }

    return results
}
```

Produces the following error

```
                      let! data = downloadData url
  --------------------^

error FS0795: The use of 'let! x = coll' in sequence expressions is not permitted. Use 'for x in coll' instead.
```

The compiler assumes we're still in the `seq` CE, so it doesn't understand that we're trying to call and await a `Task`. To fix this, we must again reintroduce the `task` CE

```f#
let fetchAll urls = task {
    let results = seq {
        for url in urls do
            let processedData = task {
                let! data = downloadData url
                let processedData = processData data
                return processedData
            }

            yield processedData
    }

    return results
}
```

However, this causes an unexpected side effect. Instead of `results` being a sequence of `processedData` values, what we actually get is `Task<seq<Task<'a>>>`, where `urls: seq<'a>`. 

In order to deal with such situations, you'll likely need to build a custom CE to handle the composition of multiple CEs together, rethink the structure of your code to avoid mixing the CEs (eg, using `mutable` to build out a mutable list, and get `Task<'a list>`), or use a 3rd-party solution, such as the [FSharp.Control.TaskSeq][8] library, which has already dealt with the complexity and bugs of combinind multiple CEs. 

```f#
#r "nuget: FSharp.Control.TaskSeq"

open FSharp.Control

let downloadData url = task { return url }
let processData data = data

let fetchAll (urls: seq<'a>) = taskSeq {
    for url in urls do
        let! results = task {
            let! data = downloadData url
            let processedData = processData data
            return processedData
        }
        yield results
}
```

**Note** the addition of the type hint `urls: seq<'a>` above. This is necessary, because `taskSeq` supports both `seq` and `taskSeq` types in `for ... in ... do` statements, so we need to be explicit so the compiler understands which overload of `for` to use. Also, we cannot use `yield! task { ... }`, but this is simply because `TaskSeqBuilder` doesn't implement `_.YieldFrom`.

<!--- References -->

[1]: https://dotnet.microsoft.com/en-us/languages/fsharp "https://dotnet.microsoft.com/en-us/languages/fsharp"
[2]: https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/computation-expressions "https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/computation-expressions"
[3]: https://fsharpforfunandprofit.com/series/computation-expressions/ "https://fsharpforfunandprofit.com/series/computation-expressions/"
[4]: https://fsharpforfunandprofit.com/ "https://fsharpforfunandprofit.com/"
[5]: https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/computation-expressions#creating-a-new-type-of-computation-expression "https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/computation-expressions#creating-a-new-type-of-computation-expression"
[6]: https://fsharp.github.io/fsharp-core-docs/reference/fsharp-control-taskbuildermodule.html "https://fsharp.github.io/fsharp-core-docs/reference/fsharp-control-taskbuildermodule.html"
[7]: https://github.com/dotnet/fsharp/blob/35971aac1809b160ac4adad5ecfff002d826cfe8/src/FSharp.Core/tasks.fs#L287 "https://github.com/dotnet/fsharp/blob/35971aac1809b160ac4adad5ecfff002d826cfe8/src/FSharp.Core/tasks.fs#L287"
[8]: https://github.com/fsprojects/FSharp.Control.TaskSeq "https://github.com/fsprojects/FSharp.Control.TaskSeq"