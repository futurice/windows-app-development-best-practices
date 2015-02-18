# Best practices in developing (universal) apps for Windows Runtime

This repository is maintained by [Futurice](http://futurice.com/), but contributions from anyone are highly encouraged! If you are interested in iOS or Android development, be sure to check our [**iOS**](https://github.com/futurice/ios-good-practices) and [**Android**](https://github.com/futurice/android-best-practices) documents as well.

To keep this document easily approachable, it aims to be concise and practical: Each subtitle is an actual practice and contains short, but very practical description of what to do and what not to do. Some reasoning is included, but more detailed explanations and discussions are only included as external links. The listing tries to start of by taking care of the most common issues and end with the rarest ones.

Feedback and contributions are wholeheartedly welcomed! Feel free to fork and send a pull request, or just participate in the discussion at [Issues](https://github.com/futurice/win-client-dev-good-practices/issues).

-----------------------------

### Use Visual Studio Community/Pro or greater

Visual Studio is the de facto IDE for developing Windows apps. The [Community edition](http://www.visualstudio.com/products/visual-studio-community-vs) gives you the same features as the [Pro editon](http://www.visualstudio.com/products/visual-studio-professional-with-msdn-vs) for free, but has some [restrictions on use in organizations](http://www.visualstudio.com/support/legal/dn877550). [Premium](http://www.visualstudio.com/products/visual-studio-premium-with-msdn-vs) mainly adds built in testing support beyond simple unit testing, and [Ultimate](http://www.visualstudio.com/products/visual-studio-ultimate-with-MSDN-vs) adds enhanced debugging, architecture, and code analysis tools. 

The free express versions are good for getting started, but lack some important features, such as support for [extensions](https://visualstudiogallery.msdn.microsoft.com/), the ability to have all the different project types in a single installation and some team collaboration features. Since the Community editon has all the features of the Express editions we recommend not using the Express editions.

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

### Refer to the right documentation

When searching for the official documentation for a class, it's easy to end up somewhere else than the documentation for Universal Apps. Generally Universal Apps utilize the new WinRT API (available for all languages) and the .NET for Universal Apps (available for managed languages). Many of the classes used in Universal Apps have existed (possible with differences) in incompatible .NET versions. Therefore, if you search e.g. for UIElement, you might end up at http://msdn.microsoft.com/en-us/library/system.windows.uielement%28v=vs.110%29.aspx, while the correct documentation for the WinRT class can be found at http://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.uielement.aspx.

You know that you are in the correct documentation if it lists Windows Store Apps or Windows Runtime Apps as a supported platform.  

The landing base for Universal Apps API documentation cant be found at: http://msdn.microsoft.com/en-us/library/windows/apps/br211369.aspx

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

### Use [CultureInfo.InvariantCulture](http://msdn.microsoft.com/en-us/library/system.globalization.cultureinfo.invariantculture) for serializations

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

### Unblock downloaded DLLs before referencing them in your projects 

For security reasons, Windows usually 'blocks' files downloaded from the internet. If you try to add a reference to such a DLL in Visual Studio, you get an incorrect error message: _"A reference to a higher version or incompatible assembly cannot be added to the project."_ Whenever you get the error, go to Properties of the DLL file and click Unblock. You should now be able to add the reference.

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

### Don't make forward references with StaticResource or ThemeResource keywords

Altough writting something like the following might not fail, it carries a performance penalty compared to defining the MyColor resource before the MyBrush resource.
```XML
<ResourceDictionary>
    <SolidColorBrush x:Key="MyBrush" Color="{StaticResource MyColor}" />
    <Color x:Key="MyColor" Value="Black" />   
```

Additionally, in some cases a forward reference will throw a runtime exception. 

[Source](https://msdn.microsoft.com/en-us/library/dn263118.aspx)

### Use independent animations over dependent ones

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

If you aren't already using a library that offers you an easy way to bind into your reactive code from XAML, search for a ReactiveProperty and ReactiveCommand helper classes.

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

## License

[Futurice Oy](http://www.futurice.com)
Creative Commons Attribution 4.0 International (CC BY 4.0)
