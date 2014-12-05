# Good practices in Windows apps development

Lessons learned from Windows 8.x and Windows Phone 8.x app developers at Futurice. Avoid reinventing the wheel by following these guidelines. If you are interested in iOS or Android development, be sure to check our [**iOS**](https://github.com/futurice/ios-good-practices) and [**Android**](https://github.com/futurice/android-best-practices) documents as well.

Feedback is welcomed, but check the [guidelines](https://github.com/futurice/android-best-practices/tree/master/CONTRIBUTING.md) first.

-----------------------------

## General C# development with Visual Studio

### Use Visual Studio Pro or greater

Visual Studio is the de facto IDE for developing Windows apps. The free express versions are good for getting started, but lack some important features, such as support for [extensions](https://visualstudiogallery.msdn.microsoft.com/). Pro version adds support for all the different project types in a single installation, additional productivity features, support for extensions, and some team collaboration features. Premium mainly adds built in testing support beyond simple unit testing, and Ultimate adds enhanced debugging, architecture, and code analysis tools.

### Use Productivity Power Tools ([2013](https://visualstudiogallery.msdn.microsoft.com/dbcb8670-889e-4a54-a226-a48a15e4cace))

A free visual studio productivity extension from Microsoft. It lacks some features of the commercial counterparts like [JustCode](http://www.telerik.com/products/justcode.aspx) or [ReSharper](https://www.jetbrains.com/resharper/), but doesn't seem to slow the IDE down at all either.

### Use [NuGet](http://www.nuget.org/)

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

### Split code into small methods to improve stacktraces and the callstack view

Code is often split into small methods for reusability. However, there are reasons to split your methods even if you don't plan to reuse them. Method name documents the intent of the code it encloses. This gives you more informative callstack view while debugging and better stacktraces from your exceptions. The stacktraces part applies especially to release builds, where source code line information is lost.

### Use [caller information attributes](http://msdn.microsoft.com/en-us/library/hh534540(v=vs.110).aspx) when tracing

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

### Use [HttpClient](http://msdn.microsoft.com/en-us/library/system.net.http.httpclient(v=vs.118).aspx) for your HTTP needs

Out of the different HTTP APIs available, HttpClient is the new one that supports async/await and automatic request decompression. Using it the async/await -way keeps your code short and readable.

### Know the timers

There are different timers for different purposes. For example [DispatcherTimer for WinRT](http://msdn.microsoft.com/en-us/library/windows/apps/xaml/windows.ui.xaml.dispatchertimer.aspx), [DispatcherTimer for WP Silverlight](http://msdn.microsoft.com/en-us/library/windows/apps/system.windows.threading.dispatchertimer(v=vs.105).aspx) and [ThreadPoolTimer](http://msdn.microsoft.com/en-us/library/windows/apps/windows.system.threading.threadpooltimer.aspx). Additionally there are [Observable.Timer](http://msdn.microsoft.com/en-us/library/system.reactive.linq.observable.timer(v=vs.103).aspx), [Task.Delay](http://msdn.microsoft.com/en-us/library/system.threading.tasks.task.delay(v=vs.110).aspx), and last (and least) [Thread.Sleep](http://msdn.microsoft.com/en-us/library/system.threading.thread.sleep(v=vs.110).aspx).

### Use [yield](http://msdn.microsoft.com/en-us/library/9k7k7cf0.aspx) when returning an IEnumerable

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

### Don't be fooled by the IObservable<TSource> Timeout<TSource, TTimeout>(this IObservable<TSource> source, IObservable<TTimeout> firstTimeout, Func<TSource, IObservable<TTimeout>> timeoutDurationSelector)

Now, this is an interface, so different implementations could behave differently. The following applies at least to the implementation in System.Reactive.Linq.Observable.

It's easy to think that you should just:
```C#
.Timeout(Observable.Return(TimeSpan.FromSeconds(10)), vm => Observable.Return(TimeSpan.FromSeconds(1)))
```
However, that will simply timeout immediately. The correct way to use it is:
```C#
// Notice the .Timer
.Timeout(Observable.Timer(TimeSpan.FromSeconds(10)), i => Observable.Timer(TimeSpan.FromSeconds(1)))
```
So, in practice the timeout occurs when the passed IObservable completes, not after the duration of the passed TimeSpan.

### When rethrowing an exception use just "throw" or include the original exception in the new exception

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

### Log Exception.ToString()

Do not just log Exception.Message, it lacks a lot of useful details. Use Exception.ToString() instead, it contains all the necessary information, including exception type, message, stacktrace, and inner exceptions.

### Catch exactly the exception you expect

If you expect a NetworkException, write _"catch (NetworkException e)"_, not just _"catch (Exception e)"_. By catching a more general exception, you hide programming errors inside the try-block. For example, a NullReferenceException would be swallowed, and make it a lot harder to notice. Additionally, any recovery code you have in the catch-block might not work as intented for other than the specific exception it was written for.

[A good article on handling exceptions](http://www.codeproject.com/Articles/9538/Exception-Handling-Best-Practices-in-NET)

### Only throw exceptions in _exceptional_ cases

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

## Windows App Development 

### Do not hardcode a Name for your custom controls

When writing custom or user controls, do not set the control's Name property with a fixed value. In XAML this would mean setting the x:Name attribute for the root element.

Each dependency object in a PresentationFrameworkCollection has to have an unique Name, and if you end up adding two controls with the same name into the same PresentationFrameworkCollection, you'll end up with:
```groovy
{System.ArgumentException: Value does not fall within the expected range.
   at MS.Internal.XcpImports.CheckHResult(UInt32 hr)
   at MS.Internal.XcpImports.Collection_AddValue[T](PresentationFrameworkCollection`1 collection, CValue value)
   at MS.Internal.XcpImports.Collection_AddDependencyObject[T](PresentationFrameworkCollection`1 collection, DependencyObject value)
   at System.Windows.PresentationFrameworkCollection`1.AddDependencyObject(DependencyObject value)
```

When you don't set the Name yourself, the framework will generate a unique name for each instance.

### Be very careful when binding into multiple dependency properties of a dependency object

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

### Use [CallerMemberName](http://msdn.microsoft.com/en-us/library/system.runtime.compilerservices.callermembernameattribute(v=vs.110).aspx) attribute or a [LINQ expression](http://msdn.microsoft.com/en-us/library/system.linq.expressions.expression(v=vs.110).aspx) to help with notifying property changes.

Many MVVM frameworks already help you with notifying property changes from your viewmodels. However, if you don't use any of those, create a base viewmodel class for yourself. Be aware of the performance overhead in creating a LINQ expressions though.

For example:
```C#
//using System;
//using System.ComponentModel;
//using System.Linq.Expressions;
//using System.Runtime.CompilerServices;

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

### If you're using Rx in your ViewModels, use ReactiveProperties and ReactiveCommands as well

If you aren't already using a library that offers you an easy way to bind into your reactive code from XAML, search for a ReactiveProperty and ReactiveCommand helper classes. Or just pick up the ones in [this](https://github.com/tomaszpolanski/Utilities/tree/master/Utilities.Reactive) little reactive utilities library by Futurice's Tomasz Polanski.

## License

[Futurice Oy](http://www.futurice.com)
Creative Commons Attribution 4.0 International (CC BY 4.0)
