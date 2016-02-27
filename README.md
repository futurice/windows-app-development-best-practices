# Best practices in developing (universal) apps for Windows Runtime

This repository is maintained by [Futurice](http://futurice.com/), but contributions from anyone are highly encouraged! If you are interested in iOS or Android development, be sure to check our [**iOS**](https://github.com/futurice/ios-good-practices) and [**Android**](https://github.com/futurice/android-best-practices) documents as well.

To keep this document easily approachable, it aims to be concise and practical: Each subtitle is an actual practice and contains short, but very practical description of what to do and what not to do. Some reasoning is included, but more detailed explanations and discussions are only included as external links. The listing tries to start of by taking care of the most common issues and end with the rarest ones.

Feedback and contributions are wholeheartedly welcomed! Feel free to fork and send a pull request, or just participate in the discussion at [Issues](https://github.com/futurice/win-client-dev-good-practices/issues).

-----------------------------

#### Tags
Tags indicate the context in which the practice applies in.

- #W81: Windows Phone 8.1 and Windows 8.1 store apps
- #UWP: Universal Windows Apps (Win 10 apps)
- #VS13: Visual Studio 2013
- #VS15: Visual Studio 2015
- #C5: C# 5
- #C6: C# 6

-----------------------------
## Table of contents
###Tooling and Documentation
- [Use Visual Studio Community/Pro or greater](#use-visual-studio-communitypro-or-greater)
- [Use Productivity Power Tools (2013)](#use-productivity-power-tools-2013-2015)
- [Use NuGet](#use-nuget)
- [Use Package Restore](#use-package-restore)
- [Refer to the right documentation](#refer-to-the-right-documentation)
- [Use the Live Visual Tree and the Live Property Explorer](#use-the-live-visual-tree-and-the-live-property-explorer)

###Debugging and Exceptions
- [Use caller information attributes when tracing](#use-caller-information-attributes-when-tracing)
- [Log Exception.ToString()](#log-exceptiontostring)
- [To log and handle unhandled exceptions subscribe to App.UnhandledException and TaskScheduler.UnobservedTaskException](#to-log-and-handle-unhandled-exceptions-subscribe-to-appunhandledexception-and-taskschedulerunobservedtaskexception)
- [Only throw exceptions in exceptional cases](#only-throw-exceptions-in-exceptional-cases)
- [Catch exactly the exception you expect](#catch-exactly-the-exception-you-expect)
- [Set Visual Studio to break debugger every time a CLR exception is thrown](#set-visual-studio-to-break-debugger-every-time-a-clr-exception-is-thrown)
- [When rethrowing an exception use just "throw" or include the original exception in the new exception](#when-rethrowing-an-exception-use-just-throw-or-include-the-original-exception-in-the-new-exception)
- [Use ContinueWith and Task.Exception to handle exceptions from async methods in expected cases](#use-continuewith-and-taskexception-to-handle-exceptions-from-async-methods-in-expected-cases)
- [Utilize the memory dumps when debugger doesn't cut it](#utilize-the-memory-dumps-when-debugger-doesnt-cut-it)

###Architecture
- [Use Windows.Web.Http.HttpClient for your HTTP needs](#use-windowswebhttphttpclient-for-your-http-needs)
- [Use CultureInfo.InvariantCulture for serializations](#use-cultureinfoinvariantculture-for-serializations)
- [Know the timers](#know-the-timers)
- [Use yield when returning an IEnumerable](#use-yield-when-returning-an-ienumerable)
- [Explicitly convert LINQ queries into collections to avoid unnecessary re-evaluation](#explicitly-convert-linq-queries-into-collections-to-avoid-unnecessary-re-evaluation)
- [Be very careful when binding into multiple dependency properties of a dependency object](#be-very-careful-when-binding-into-multiple-dependency-properties-of-a-dependency-object)
- [Use ItemsStackPanel over VirtualizingStackPanel](#use-itemsstackpanel-over-virtualizingstackpanel)
- [Use independent animations over dependent ones](#use-independent-animations-over-dependent-ones)
- [Put XAML in Class Libraries into their own ResourceDictionary](#put-xaml-in-class-libraries-into-their-own-resourcedictionary)
- [If you're using Rx in your ViewModels, use ReactiveProperties and ReactiveCommands as well](#if-youre-using-rx-in-your-viewmodels-use-reactiveproperties-and-reactivecommands-as-well)
- [Don't be fooled by the IObservable duration parameters in IObservable extension methods](#dont-be-fooled-by-the-iobservable-duration-parameters-in-iobservable-extension-methods)

###Code Cleanliness
- [Split code into small methods to improve stacktraces and the callstack view](#split-code-into-small-methods-to-improve-stacktraces-and-the-callstack-view)
- [Use await for asynchronous execution](#use-await-for-asynchronous-execution)
- [Use CallerMemberName attribute](#use-callermembername-attribute)
- [Use LINQ expression when notifying changes to other properties](#use-linq-expression-when-notifying-changes-to-other-properties)
- [Use the nameof operator when notifying changes to other properties](#use-the-nameof-operator-when-notifying-changes-to-other-properties)
- [Don't make forward references with StaticResource or ThemeResource keywords](#dont-make-forward-references-with-staticresource-or-themeresource-keywords)

###Troubleshooting
- [Uninstall the app installed from the store before trying to sideload the same app](#uninstall-the-app-installed-from-the-store-before-trying-to-sideload-the-same-app)
- [Unblock downloaded DLLs before referencing them in your projects](#unblock-downloaded-dlls-before-referencing-them-in-your-projects)
- [If your app crashes only when NOT debugging, check your App.OnSuspending/OnResuming](#if-your-app-crashes-only-when-not-debugging-check-your-apponsuspendingonresuming)
- [If you have problems deploying to the emulator, try disabling your anti-virus](#if-you-have-problems-deploying-to-the-emulator-try-disabling-your-anti-virus)
- [If you have problems with Visual Studio stability, try disabling the XAML designer](#if-you-have-problems-with-visual-studio-stability-try-disabling-the-xaml-designer) 

-----------------------------

##Tooling and Documentation
### Use Visual Studio Community/Pro or greater
| #VS13 #W81 #C5

Visual Studio is the de facto IDE for developing Windows apps. The [Community edition](http://www.visualstudio.com/products/visual-studio-community-vs) gives you the same features as the [Pro editon](http://www.visualstudio.com/products/visual-studio-professional-with-msdn-vs) for free, but has some [restrictions on use in organizations](http://www.visualstudio.com/support/legal/dn877550). [Premium](http://www.visualstudio.com/products/visual-studio-premium-with-msdn-vs) mainly adds built in testing support beyond simple unit testing, and [Ultimate](http://www.visualstudio.com/products/visual-studio-ultimate-with-MSDN-vs) adds enhanced debugging, architecture, and code analysis tools. 

The free express versions are good for getting started, but lack some important features, such as support for [extensions](https://visualstudiogallery.msdn.microsoft.com/), the ability to have all the different project types in a single installation and some team collaboration features. Since the Community editon has all the features of the Express editions we recommend not using the Express editions.

### Use Productivity Power Tools ([2013](https://visualstudiogallery.msdn.microsoft.com/dbcb8670-889e-4a54-a226-a48a15e4cace)) [(2015)](https://visualstudiogallery.msdn.microsoft.com/34ebc6a2-2777-421d-8914-e29c1dfa7f5d)
| #VS13 #VS15 #UWP #W81 #C6 #C5

A free visual studio productivity extension from Microsoft. It lacks some features of the commercial counterparts like [JustCode](http://www.telerik.com/products/justcode.aspx) or [ReSharper](https://www.jetbrains.com/resharper/), but doesn't seem to slow the IDE down at all either.

### Use [NuGet](http://www.nuget.org/)
| #VS15 #VS13 #UWP #W81 #C6 #C5

Nuget is Microsoft's take on a package manager. There's a Visual Studio extension called NuGet Package Manager preinstalled in newer Visual Studios. Bottom line: [Use it](http://docs.nuget.org/docs/start-here/managing-nuget-packages-using-the-dialog) for external references when you don't need to include the source code in your Solution.

#### Use [Package Restore](http://docs.nuget.org/docs/reference/package-restore)

According to [NuGet docs:](http://docs.nuget.org/docs/reference/package-restore)
>Beginning with NuGet 2.7, the NuGet Visual Studio extension integrates into Visual Studio's build events and restores missing packages when a build begins.
>
>This approach to package restore offers several advantages:
>
>- No need to enable it for your project or solution. Visual Studio will automatically download missing packages before your projects are built and team members don't need to understand NuGet Package Restore.
>- No additional directories and files are required within your project or solution.
>- Packages are restored before MSBuild is invoked by Visual Studio. This allows packages that extend MSBuild though targets/props file imports to be restored before MSBuild starts, ensuring a successful build.
>- Compatibility with ASP.NET Web Site projects created in Visual Studio.

You are using the old package restore if you have clicked the "Enable NuGet Package Restore" -button in Visual Studio. If so, you should migrate: [NuGet doc](http://docs.nuget.org/docs/workflows/migrating-to-automatic-package-restore) or [with pictures](http://www.xavierdecoster.com/migrate-away-from-msbuild-based-nuget-package-restore). 

### Refer to the right documentation
| #VS15 #VS13 #UWP #W81 #C6 #C5

When searching for the official documentation for a class, it's easy to end up somewhere else than the documentation for Universal Apps. Generally Universal Apps utilize the new WinRT API (available for all languages) and the .NET for Universal Apps (available for managed languages). Many of the classes used in Universal Apps have existed (possible with differences) in incompatible .NET versions. Therefore, if you search e.g. for UIElement, you might end up at http://msdn.microsoft.com/en-us/library/system.windows.uielement%28v=vs.110%29.aspx, while the correct documentation for the WinRT class can be found at http://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.uielement.aspx.

You know that you are in the correct documentation if it lists Windows Store Apps or Windows Runtime Apps as a supported platform.  

The landing base for Universal Apps API documentation cant be found at: http://msdn.microsoft.com/en-us/library/windows/apps/br211369.aspx

### Use the Live Visual Tree and the Live Property Explorer
| #VS15 #UWP #W81

The VS 2015 XAML inspection tools are a great asset when tweaking and debugging your UI. Unfortunately the Live Property Explorer doesn't save the changes you make to your XAML. However, after you find a good value for a property, you can click 'go to source' and modify the XAML while debugging. The XAML changes you make won't take effect until you relaunch the app, but at least you have now 'saved' the value you want to use. Notice that you have to be running your app on Windows 10 (device or emulator) to be able to use these tools.

You can find the official documentation on how to use them [here](https://msdn.microsoft.com/en-us/library/mt270227.aspx).

##Debugging and Exceptions
### Use [caller information attributes](http://msdn.microsoft.com/en-us/library/hh534540(v=vs.110).aspx) when tracing
| #VS15 #VS13 #UWP #W81 #C6 #C5

When you add CallerMemberName, CallerFilePath, or CallerLineNumber attributes for optional parameters, the parameters get set with the file path, line number, and member name of the caller. The values are set into the method call at compile time, don't have a performance penalty, and are not affected by obfuscation.

[Example from msdn:](http://msdn.microsoft.com/en-us/library/system.runtime.compilerservices.callermembernameattribute(v=vs.110).aspx?cs-save-lang=1&cs-lang=csharp#code-snippet-2)
```C#
// using System.Runtime.CompilerServices 
// using System.Diagnostics; 

public void DoProcessing()
{
    TraceMessage("Something happened.");
}

public void TraceMessage(string message,
        [CallerMemberName] string memberName = "",
        [CallerFilePath] string sourceFilePath = "",
        [CallerLineNumber] int sourceLineNumber = 0)
{
    Trace.WriteLine("message: " + message);
    Trace.WriteLine("member name: " + memberName);
    Trace.WriteLine("source file path: " + sourceFilePath);
    Trace.WriteLine("source line number: " + sourceLineNumber);
}

// Sample Output: 
//  message: Something happened. 
//  member name: DoProcessing 
//  source file path: c:\Users\username\Documents\Visual Studio 2012\Projects\CallerInfoCS\CallerInfoCS\Form1.cs 
//  source line number: 31
```

### Log Exception.ToString()
| #VS15 #VS13 #UWP #W81 #C6 #C5

Do not just log Exception.Message, it lacks a lot of useful details. Use Exception.ToString() instead, it contains all the necessary information, including exception type, message, stacktrace, and inner exceptions.

### To log and handle unhandled exceptions subscribe to App.UnhandledException and TaskScheduler.UnobservedTaskException
| #VS15 #VS13 #UWP #W81 #C6 #C5

Most of the unhandled exceptions end up in the App.UnhandledException handler, however if the "Always use or await the return value of an async method" -pratice is not followed, they end up in the TaskScheduler.UnobservedTaskException instead.  

Sources: [filipekberg.se](http://www.filipekberg.se/2012/09/20/avoid-shooting-yourself-in-the-foot-with-tasks-and-async/), [msdn](https://msdn.microsoft.com/en-us/library/windows/apps/dn263110.aspx)

### Only throw exceptions in _exceptional_ cases
| #VS15 #VS13 #UWP #W81 #C6 #C5

Exceptions are slow and are by definition _exceptions_. Additionally each exception thrown during the normal execution of the codebase makes using the Visual Studio feature to break the debugger when any or some exceptions are thrown a pain. Ideally you could always have the debugger break when a managed exception is thrown. Then, every time your debugger breaks, there's something noteworthy for you to look at. Either there's a programming error, something wrong with your debugging setup, or a device malfunction.

This applies to both designing APIs and using APIs. There's often a way to run a programmatic check to avoid _expected_ exceptions.

Do:
```C#
if (!closable.IsAlreadyClosed) {
  closable.Close()
} else {
  DoSomething();
}
```
Don't:
```C#
try {
  closable.Close()
} catch (AlreadyClosedException e) {
  DoSomething();
}
```
[MSDN on throwing exceptions](http://msdn.microsoft.com/en-us/library/seyhszts(v=vs.110).aspx)

### Catch exactly the exception you expect
| #VS15 #VS13 #UWP #W81 #C6 #C5

If you expect a NetworkException, write _"catch (NetworkException e)"_, not just _"catch (Exception e)"_. By catching a more general exception, you hide programming errors inside the try-block. For example, a NullReferenceException would be swallowed, and make it a lot harder to notice. Additionally, any recovery code you have in the catch-block might not work as intented for other than the specific exception it was written for.

[A good article on handling exceptions](http://www.codeproject.com/Articles/9538/Exception-Handling-Best-Practices-in-NET)

### Set Visual Studio to break debugger every time a CLR exception is thrown
| #VS15 #VS13 #UWP #W81 #C6 #C5

If you have followed the practices above, exceptions should only be thrown in/into your code as a result of a programming error or something unrecoverable such as an OutOfMemoryException. Generally, When you make an error, you want to be notified about it as loud and clear as possible. The default behavior for Visual Studio is to only break debugger on uncaught exceptions. Now, if you have some generic catches in place to swallow exceptions, for example from some of your secondary components, such as analytics, you'd miss the unwanted behavior.

On Visual studio 2013 go to: Debug -> Exceptions... and check the "Thrown" cloumn checkbox on the Common Language Runtime Exceptions.
On Visual studio 2015 go to: Debug -> Windows -> Exception Settings

If you are using any synchronous APIs that throw exceptions even in expected cases, you might have to leave those unchecked. Also, you might want to uncheck TaskCanceledException and OperationCanceledException.

### When rethrowing an exception use just "throw" or include the original exception in the new exception
| #VS15 #VS13 #UWP #W81 #C6 #C5

Sometimes you want to catch an exception and rethrow it as is or with additional information. For example:
```C#
try {
  ExceptionThrowingOperation();
} catch (Exception e) {
  if (CanRecover) {
    Recover();
  }
  else {
    // Different options for rethrowing
    throw e; // Don't do this. It basically throws a new exception, with a new stacktrace.
    throw; // Do this if you want to rethrow the same exception. It keeps the stacktrace (Except if the original exception occured in the same method. In which case you will loose the line information. There are ways around this however).
    throw new SpecificException("my message", e); // Do this if you want to throw a more specific exception with additional information. It sets the original exception as the inner exception of the new exception. Therefore, the stacktrace will be preserved.
  }
}
```

### Use ContinueWith and Task.Exception to handle exceptions from async methods in expected cases
| #VS15 #VS13 #UWP #W81 #C6 #C5

Some APIs like to throw exceptions even on expected cases. Check, for example, the next practice for explanation on why this is not necessarily the best kind of behavior. One good example of such API is Windows.Web.Http.HttpClient, which throws Exceptions on network errors. A network error is hardly an unexpected event. Especially if your app is supposed to fallback to a cached/alternative value in such cases. Fortunately, there's a way to utilize Task on asynchronous methods to avoid getting the exception thrown into your code while still handling it. For example, here we convert all network errors to  HttpStatusCode.RequestTimeout without letting an exception to be thrown:

```C#
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get, new Uri("http://www.futurice.com"));

var responseTcs = new TaskCompletionSource<HttpResponseMessage>();
await client.SendRequestAsync(request)
    .AsTask()
    .ContinueWith(task => {
        HttpResponseMessage response = null;

        if (task.Status == TaskStatus.Faulted) {
            // You will have to access the task.Exception property to mark the Exception 'observed' otherwise it'll end up in TaskScheduler.UnobservedTaskException
            // Use GetBaseException() to get the original exception from the AggregateException.
            var exception = task.Exception.GetBaseException();
            response = new HttpResponseMessage(HttpStatusCode.RequestTimeout);
            response.ReasonPhrase = exception.ToString();
            response.RequestMessage = request;
        }
        else {
            response = task.Result;
        }

        responseTcs.TrySetResult(response);
    });

HttpResponseMessage responseMessage = await responseTcs.Task;
```

### Utilize the memory dumps when debugger doesn't cut it
| #VS15 #VS13 #UWP #W81 #C6 #C5

On newer WP8 firmwares you can to got Settings->Feedback and turn on storing the dumps on the device. The dumps will be stored in Documents\Debug folder on your phone. On Windows 8 you can merge [this registry entry](https://github.com/futurice/windows-app-development-best-practices/blob/utilize_dumps/misc/EnableDumps.reg) to enable storing dumps into C:\crashdumps. Remember to replace the YOUR_EXE_NAME in the .reg with the name of your app's exe. On Window 8, you can also generate dumps manually by right clicking your app in the Task Manager and choosing "create dump file".

Dumps can be analyzed for example using [Visual Studio](https://msdn.microsoft.com/en-us/library/d5zhxt22.aspx) or [WinDbg](https://msdn.microsoft.com/en-us/library/windows/hardware/ff538058(v=vs.85).aspx).

##Architecture
### Use [Windows.Web.Http.HttpClient](https://msdn.microsoft.com/en-US/library/windows/apps/xaml/windows.web.http.httpclient) for your HTTP needs
| #VS15 #VS13 #UWP #W81 #C6 #C5

Out of the different HTTP APIs available, HttpClient(s) are the newest ones that support async/await and automatic request decompression. Notice that there are two of them now: System.Net.Http.HttpClient and Windows.Web.Http.HttpClient. The latter was added to Windows 8.1 and is a WinRT API instead of a .NET API. More importantly System.Net.Http might be made unavailable for Windows Store Apps in the future. Additionally, the Windows.Web.Http classes offer HTTP/2 support, better caching, and better configurability all around.

One useful thing to know about the Windows.Web.Http.HttpClient is that it throws an Exception for network errors that prevent a succesful HTTP-connection. You can get the actual reason with Windows.Web.WebError.GetStatus(HRESULT). For example:
```C#
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get, new Uri("http://www.futurice.com"));
try {
    // Http-errors are returned in the response, and no exception is thrown
    HttpResponseMessage response = await client.SendRequestAsync(request);
}
// It really is just an exception, can't catch a more specific type
catch (Exception ex) {
    WebErrorStatus error = WebError.GetStatus(ex.HResult);
    // For example, if your device could not connect to the internet at all, the error would be WebErrorStatus.HostNameNotResolved
}
```
### Use [CultureInfo.InvariantCulture](http://msdn.microsoft.com/en-us/library/system.globalization.cultureinfo.invariantculture) for serializations
| #VS15 #VS13 #UWP #W81 #C6 #C5

When ever you are serializing values that can be represented differently in different cultures, make sure that you serialize and deserialize them with the same culture. If you don't define the culture explicitly, the APIs normally use the culture of the current thread, which is often set by a setting in the operating system. CultureInfo.InvariantCulture is an IFormatProvider that exists exactly for the purpose of hardocoding the culture for serializations.

For example, serialization of DateTime, double, float, and decimal depend on culture. If you serialize these values using the OS provided culture, and the culture has changed when you deserialize, you are likely to run into runtime exceptions. as the expected date format, or the decimal separator might've changed.

So, don't do this:
```C#
string serializedDateTime = dateTime.ToString();
// Write serializedDateTime to a presisten storage.
...
// Load serializedDateTime from presistent storage.
// If the thread culture was, let's say, en-us when you serialized the datetime, and now it has been changed for example to fi-fi, you would get an FormatException: "String was not recognized as a valid DateTime".
DateTime dateTime = DateTime.Parse(serializedDateTime);
```
Do this:
```C#
string serializedDateTime = dateTime.ToString(System.Globalization.CultureInfo.InvariantCulture);
// Write serializedDateTime to a presisten storage.
...
// Load serializedDateTime from presistent storage.
// No matter what the current thread culture during the serialization and deserialization, it will use the special InvariantCulture date formatting settings, and just work.
DateTime dateTime = DateTime.Parse(serializedDateTime, System.Globalization.CultureInfo.InvariantCulture);
```

Sources: [MSDN](https://msdn.microsoft.com/en-us/library/windows/apps/xaml/Dn469431.aspx), [Channel9](https://channel9.msdn.com/Events/Build/2013/4-092)

### Know the timers
| #VS15 #VS13 #UWP #W81 #C6 #C5

There are different timers for different purposes. For example [DispatcherTimer for WinRT](http://msdn.microsoft.com/en-us/library/windows/apps/xaml/windows.ui.xaml.dispatchertimer.aspx), [DispatcherTimer for WP Silverlight](http://msdn.microsoft.com/en-us/library/windows/apps/system.windows.threading.dispatchertimer(v=vs.105).aspx) and [ThreadPoolTimer](http://msdn.microsoft.com/en-us/library/windows/apps/windows.system.threading.threadpooltimer.aspx). Additionally there are [Observable.Timer](http://msdn.microsoft.com/en-us/library/system.reactive.linq.observable.timer(v=vs.103).aspx), [Task.Delay](http://msdn.microsoft.com/en-us/library/system.threading.tasks.task.delay(v=vs.110).aspx), and last (and least) [Thread.Sleep](http://msdn.microsoft.com/en-us/library/system.threading.thread.sleep(v=vs.110).aspx).

### Use [yield](http://msdn.microsoft.com/en-us/library/9k7k7cf0.aspx) when returning an IEnumerable
| #VS15 #VS13 #UWP #W81 #C6 #C5

Rather than writing something like:
```C#
public System.Collections.Generic.IEnumerable<Galaxy> Galaxies {
    get {
      return new List<Galaxy>() {
        new Galaxy { Name = "Tadpole", MegaLightYears = 400 },
        new Galaxy { Name = "Pinwheel", MegaLightYears = 25 },
        new Galaxy { Name = "Milky Way", MegaLightYears = 0 },
        new Galaxy { Name = "Andromeda", MegaLightYears = 3 },
      };
    }
}
```
Write this instead:
```C#
public System.Collections.Generic.IEnumerable<Galaxy> Galaxies {
    get {
      yield return new Galaxy { Name = "Tadpole", MegaLightYears = 400 };
      yield return new Galaxy { Name = "Pinwheel", MegaLightYears = 25 };
      yield return new Galaxy { Name = "Milky Way", MegaLightYears = 0 };
      yield return new Galaxy { Name = "Andromeda", MegaLightYears = 3 };
    }
}
```
It will automatically return empty IEnumreable if no "yield return" is called. This will avoid null reference exceptions when you're expecting to get an IEnumerable. The yield approach also only runs as far as is required by the possible iterator. For example, the following only creates one Galaxy instance, avoiding unnecessary processing.
```C#
var firstGalaxy = Galaxies.First();
```

### Explicitly convert LINQ queries into collections to avoid unnecessary re-evaluation
| #VS15 #VS13 #UWP #W81 #C6 #C5

Probably the #1 thing to know about LINQ is that it creates a query that is executed whenever its items are accessed. Sometimes this is what you actually want. However, often you just want to run the query once but access the resulting items multiple times. To avoid unnecessary re-evaluation and bugs resulting from the query result changing, use ToArray, ToList, etc. extension methods to run the query and store the results into a collection.

So rather than:
```C#
var sItems = MyItems.Where(i => i.Name.Contains("s"));
var firstSItem = sItems.First(); // Query executed
var lastSItem = sItems.Last(); // Query executed
MyItems.Add(new MyItem("spoon"));
Handle(sItems.Last()); // Query executed, returns the spoon item
```
Do this instead:
```C#
var sItems = MyItems.Where(i => i.Name.Contains("s")).ToList(); // Query executed
var firstSItem = sItems.First(); 
var lastSItem = sItems.Last();
MyItems.Add(new MyItem("spoon"));
Handle(sItems.Last()); // returns the lastSItem
```

### Be very careful when binding into multiple dependency properties of a dependency object
| #VS15 #VS13 #UWP #W81 #C6 #C5

There are two possible suprises:

#### Order matters

For example: [1](http://www.weseman.net/blog/development/c/order-in-xaml-is-important-when-using-data-binding/) and [2](http://discoveringdotnet.alexeyev.org/2011/03/order-in-xaml-is-important.html)

#### Binding to DataContext changes the default binding source

For example, the following xaml looks for the AdVisiblity in MyAdViewModel, not in the MyPageViewModel. Changing the order of the bindings doesn't make a difference. (However, does it first look in MyPageViewModel and then in MyAdViewModel).

```XML
<Grid>
    <Grid.DataContext>
        <MyPageViewModel AdVisiblity="Collapsed">
            <MyPageViewModel.AdContext>
                <MyAdViewModel Url="www.bystuff.com" Text="Buy Stuff!"/>
            </MyPageViewModel.AdContext>
        </MyPageViewModel>
    </Grid.DataContext>
    
    <AdControl Visibility="{Binding AdVisibility}" DataContext="{Binding AdContext}"/> 
</Grid>
```
This happens because "{Binding PropertyName}" is short for:
```XML
"{Binding Path=DataContext.PropertyName, Source={RelativeSource Self}"
```
It actually binds to the property PropertyName in the object in the DataContext property of its self. When DataContext is not set, it's automatically inherited from the Parent.

### Use ItemsStackPanel over VirtualizingStackPanel
| #VS15 #VS13 #UWP #W81 #C6 #C5

ItemsStackPanel was added into Windows 8.1 and should be used over VirtualizingStackPanel. If item grouping is used, VirtualizingStackPanel realizes the whole group of items even if only the first one was required. ItemsStackPanel handles items virtualization correctly also when groups are used and will therefore offer better performance.

Source: [MSDN Blog](http://blogs.msdn.com/b/alainza/archive/2014/09/04/listview-basics-and-virtualization-concepts.aspx) 

### Use independent animations over dependent ones
| #VS15 #VS13 #UWP #W81 #C6 #C5

Dependent animations are animations that depend on the UI thread, the major drawback is performance. Independent animations are animations that can be run independent of the UI thread and therefore don't burden it and remain smooth even if the UI thread is blocked.

For implementing animations you should use [Storyboards](http://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.media.animation.storyboard.aspx).

According to [MSDN](http://msdn.microsoft.com/en-us/library/windows/apps/hh994638.aspx), all of the following types of animations are guaranteed to be independent:
* [Object animations using key frames](http://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.media.animation.objectanimationusingkeyframes.aspx)
* Zero-duration animations
* Animations to the Canvas.Left and Canvas.Top properties
* Animations to the UIElement.Opacity property
* Animations to properties of type Brush when targeting the SolidColorBrush.Color subproperty
* Animations to the following UIElement properties when targeting subproperties of the return value types
 * [RenderTransform](http://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.uielement.rendertransform.aspx): For example, set RenderTransform to ScaleTransform and animate it's ScaleX instead of animating UIElement.Width
 * [Projection](http://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.uielement.projection.aspx): E.g. for 3D-effects 
 * [Clip](http://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.uielement.clip.aspx)

Additionally you should use animations from the [Windows.UI.Xaml.Media.Animation](http://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.media.animation.aspx) namespace when possible. The animations have "Theme" in their class name, and are described in [Quickstart: Animating your UI using library animations](http://msdn.microsoft.com/en-us/library/windows/apps/xaml/hh452703.aspx).

### Put XAML in Class Libraries into their own ResourceDictionary
| #VS15 #VS13 #UWP #W81 #C6 #C5

In some cases it might be tempting to load a XAML snippet with System.Windows.Markup.XamlReader.Parse(yourXamlString) directly in C#. However, if your XAML references for example a control in another class library, the user of your library has to add a reference to that library as well. Otherwise an exception will be thrown when the XamlReader tries to parse the XAML.

First add for example a Resources.xaml into your project, and add the ResourceDictionary with it's contents into the file. You can then load the ResourceDictionary by creating a new ResourceDictionary instance and setting it's source as follows:
```C#
var libraryResources = new ResourceDictionary {
    Source = new Uri($"ms-appx:///NameOfMyClassLibrary/Resources.xaml", UriKind.Absolute)
};
```

Now you can also merge the ResourceDictionary to the app's App.xaml dictionary. Then the named resources can be accessed via Application.Current.Resources and be overridden in the App.xaml.

```C#
// Merging the library resources into the app's main ResourceDictionary
Application.Current.Resources.MergedDictionaries.Add(libraryResources);

// Accessing the resource (or it's overridden counterpart)
Application.Current.Resources["libraryResourceKey"]
```

### If you're using Rx in your ViewModels, use ReactiveProperties and ReactiveCommands as well
| #VS15 #VS13 #UWP #W81 #C6 #C5

If you aren't already using a library that offers you an easy way to bind into your reactive code from XAML, [here's](https://github.com/runceel/ReactiveProperty) one that offers ReactiveProperty, ReactiveCommand and some other useful classes.

### Don't be fooled by the IObservable duration parameters in IObservable extension methods
| #VS15 #VS13 #UWP #W81 #C6 #C5

In System.Reactive.Linq, at least Delay, Timeout, Throttle, and GroupByUntil extension methods for IObservable have overloads that take an IObservable parameter of type TDelay, TTimeout, TThrottle, or TDuration. These parameters are always used to indicate a duration and the duration is considered to be passed when the given IObservable completes. The actual type of the IObservable doesn't matter, but most of the time you'll want to use Observable.Timer to create the parameter.

For example, you could think that the following code makes all items of the observable timeout after one second:
```C#
.Timeout(vm => Observable.Return(TimeSpan.FromSeconds(1)))
```
However, that will simply timeout immediately. A correct way to use these parameters is for example:
```C#
// Notice the .Timer
.Timeout(i => Observable.Timer(TimeSpan.FromSeconds(1)))
```

##Code Cleanliness
### Split code into small methods to improve stacktraces and the callstack view
| #VS15 #VS13 #UWP #W81 #C6 #C5

Code is often split into small methods for reusability. However, there are reasons to split your methods even if you don't plan to reuse them. Method name documents the intent of the code it encloses. This gives you more informative callstack view while debugging and better stacktraces from your exceptions. The stacktraces part applies especially to release builds, where source code line information is lost.

### Use await for asynchronous execution
| #VS15 #VS13 #UWP #W81 #C6 #C5

Prefer using awaitable methods comparing to synchronous methods. Awaitable method should return Task or Task<>. Avoid using void awaitable methods unless it's really needed. Follow Microsoft [best practices for asynchronous programming](https://msdn.microsoft.com/en-us/magazine/jj991977.aspx?f=255&MSPPError=-2147217396)
#### Handle exceptions in awaitable methods
Exceptions from synchronous methods propagate up the call stack regardless if you use the possible return value or not. Awaitable methods work a bit differently. When an unhandled exception is thrown within an awaitable method, the exception is wrapped into the task object returned by the method. The exception is only propagated when you either await the task/method, or try to access a completed task's Result. When you access the Result or await the method within a try block, you can catch the unhandled exceptions from the awaitable method normally. Additionally, you can observe the exception by accessing the task's Exception property. However, be aware that reading the Exception property effectively 'catches' the exception. Be careful to not unintentionally swallow the exception this way. If you let the exception go through unobserved, the [TaskScheduler.UnobservedTaskException](https://msdn.microsoft.com/en-us/library/system.threading.tasks.taskscheduler.unobservedtaskexception%28v=vs.110%29.aspx) will be fired when the task is garbage collected.

There is also another way for handling exceptions. Use the method [Task.ContinueWith();](https://msdn.microsoft.com/en-us/library/dd270696(v=vs.110).aspx) for executing an action on finished task. In case an exception in awaitable method, this method will be called anyway and it's possible to get an exception details inside. It's possible to specify on which context this method will be executed.
#### Prevent deadlocks in await methods
In some cases using awaited methods can produce deadlocks ([link 1](http://blog.stephencleary.com/2012/07/dont-block-on-async-code.html), [link 2](http://blog.ciber.no/2014/05/19/using-task-configureawaitfalse-to-prevent-deadlocks-in-async-code/)). In order to prevent deadlocks the method `Task.ConfigureAwait(false);`.

### Use [CallerMemberName](http://msdn.microsoft.com/en-us/library/system.runtime.compilerservices.callermembernameattribute(v=vs.110).aspx) attribute
| #VS15 #VS13 #UWP #W81 #C6 #C5

Many MVVM frameworks already help you with notifying property changes from your viewmodels. However, if you don't use any of those, create a base viewmodel class for yourself.

For example:
```C#
public class ViewModelBase : INotifyPropertyChanged
{
	public event PropertyChangedEventHandler PropertyChanged;

	/// <summary>
	/// Use this from within a property's setter
	/// For example: if (SetProperty(ref _propertysBackingField, value)){ OptionallyDoThisIfValueWasNotEqual(); }
	/// </summary>
	protected bool SetProperty<T>(ref T storage, T value, [CallerMemberName]String propertyName = "")
	{
		if (object.Equals(storage, value)) {
			return false;
		}

		storage = value;
		NotifyPropertyChanged(propertyName);
		return true;
	}
	
	
	/// <summary>
	/// You can use this from within a property's setter when you don't want to set a backing field. 
	/// For example: NotifyPropertyChanged();
	/// </summary>
	protected void NotifyPropertyChanged([CallerMemberName]string propertyName = "")
	{
		PropertyChangedEventHandler handler = PropertyChanged;
		if (handler != null) {
			handler(this, new PropertyChangedEventArgs(propertyName));
		}
	}	
}
```

### Use [LINQ expression](http://msdn.microsoft.com/en-us/library/system.linq.expressions.expression(v=vs.110).aspx) when notifying changes to other properties
| #VS15 #VS13 #W81 #C5

When notifying a change to a property outside of the property setter, you can't use CallerMemberName. Just hardcoding the property name into a string is dangerous as you might misspell it, or the property name might get changed later on. There's a performance overhead in creating a LINQ expressions, but atleast it gives you compile time checking, intellisense, and refactoring support for the property name.

For example:
```C#
public class ViewModelBase : INotifyPropertyChanged
{
	public event PropertyChangedEventHandler PropertyChanged;

	/// <summary>
	/// Use this to notify a property change from outside of the property's setter.
	/// For example: NotifyPropertyChanged(() => MyPropertyWhoseGetterShouldNowReturnNewValue);
	/// </summary>
	protected void NotifyPropertyChanged<T>(Expression<Func<T>> memberExpression)
	{
		var lambda = (memberExpression as LambdaExpression);
		if (lambda == null) return;
		
		var expr = lambda.Body as MemberExpression;
		if (expr == null) return;
	
		NotifyPropertyChanged(expr.Member.Name);
	}
}
```

### Use the nameof operator when notifying changes to other properties
| #VS15 #UWP #W81 #C6

When notifying a change to a property outside of the property setter, you can't use CallerMemberName. Just hardcoding the property name into a string is dangerous as you might misspell it, or the property name might get changed later on. Use the C# 6 nameof operator instead to get the name of the property as a string.

For example:
```C#
NotifyPropertyChanged(nameof(MyPropertyWhoseGetterShouldNowReturnNewValue));
```

### Don't make forward references with StaticResource or ThemeResource keywords
| #VS15 #VS13 #UWP #W81 #C6 #C5

Altough writting something like the following might not fail, it carries a performance penalty compared to defining the MyColor resource before the MyBrush resource.
```XML
<ResourceDictionary>
    <SolidColorBrush x:Key="MyBrush" Color="{StaticResource MyColor}" />
    <Color x:Key="MyColor" Value="Black" />   
```

Additionally, in some cases a forward reference will throw a runtime exception. 

[Source](https://msdn.microsoft.com/en-us/library/dn263118.aspx)

##Troubleshooting
### Uninstall the app installed from the store before trying to sideload the same app
| #VS15 #VS13 #UWP #W81 #C6 #C5

You don't actually always need to uninstall it first, but sometimes you can get an unrelated error when trying to sideload app that is already installed to the same device from store. So, if you get an error that doesn't seem to make sense when sideloading, make sure the app isn't already installed to the device.

### Unblock downloaded DLLs before referencing them in your projects
| #VS15 #VS13 #UWP #W81 #C6 #C5

For security reasons, Windows usually 'blocks' files downloaded from the internet. If you try to add a reference to such a DLL in Visual Studio, you get an incorrect error message: _"A reference to a higher version or incompatible assembly cannot be added to the project."_ Whenever you get the error, go to Properties of the DLL file and click Unblock. You should now be able to add the reference.

### If your app crashes only when NOT debugging, check your App.OnSuspending/OnResuming
| #VS15 #VS13 #UWP #W81 #C6 #C5

When your app is attached to the debugger, it doesn't get suspended as it normally does. This means that App.OnSuspending and App.OnResuming don't get called when for example using any of the APIs that open a system UI and push your app to background. Now, if you have a bug that causes a crash in either of these methods, you might not get the behavior you expect when NOT debugging.

Easy way to check is to place a breakpoint into the methods and use the Lifecycle Events tool in Visual Studio to get your app suspended while debugging.

### If you have problems deploying to the emulator, try disabling your anti-virus
| #VS15 #VS13 #UWP #W81 #C6 #C5

At least Symantec Endpoint Protection is known to cause an issue in which the Visual Studio just keeps on deploying your app to the emulator forever and never completes.

### If you have problems with Visual Studio stability, try disabling the XAML Designer
| #VS15 #VS13 #W81 #C6 #C5

In some cases XAML designer seems to cause Visual Studio to crash and freeze quite a bit. It might be helpful to disable the designer completely and just use Blend when you need to.

On Visual Studio 2015, go to Tools -> Options -> XAML Designer -> Enable XAML Designer.

On Visual Studio 2013, follow [these](http://blog.spinthemoose.com/2013/03/24/disable-the-xaml-designer-in-visual-studio/) instructions.

## License

[Futurice Oy](http://www.futurice.com)
Creative Commons Attribution 4.0 International (CC BY 4.0)
