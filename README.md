win-client-dev-good-practices
=============================

A collection windows phone and windows 8 app development good practices from Futurice

-----------------------------

## General C# Programming

### Use Visual Studio Pro or greater

Visual Studio is the defacto IDE for developing Windows apps. The free express versions are okay for getting started, but lacks some important features such as support for [extensions](https://visualstudiogallery.msdn.microsoft.com/). Pro version adds support for all the different project types in a single installation, additional productivity features, support for extensions, and some team collaboration features. Premium mainly adds built in testing support beyond simple unit testing, and Ultimate adds enhanced debugging, architecture, and code analysis tools.

### Use IEnumerables and [yield](http://msdn.microsoft.com/en-us/library/9k7k7cf0.aspx) whenever possible

Rather than writing something like:
```groovy
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
write this instead:
```groovy
public System.Collections.Generic.IEnumerable<Galaxy> Galaxies {
    get {
      yield return new Galaxy { Name = "Tadpole", MegaLightYears = 400 };
      yield return new Galaxy { Name = "Pinwheel", MegaLightYears = 25 };
      yield return new Galaxy { Name = "Milky Way", MegaLightYears = 0 };
      yield return new Galaxy { Name = "Andromeda", MegaLightYears = 3 };
    }
}
```
It automatically returns empty IEnumreable if no "yield return" is called. This will avoid null reference exceptions when you're expecting to get an IEnumerable. The yield approach also only runs as far as the caller requires. For example:
```groovy
var firstGalaxy = Galaxies.First();
```
only creates the first Galaxy instance, avoiding unnecessary processing.

### Split code into small methods

Code is often split into small methods for reusability. However, there are reasons to split your methods even if you don't plan to reuse them. Method name documents the intent of the code it encloses. Additonally, you get more informative stacktraces from your exceptions when your code is split into smaller methods.

### Use [caller information attributes](http://msdn.microsoft.com/en-us/library/hh534540(v=vs.110).aspx) for tracing

When you add CallerMemberName, CallerFilePath, or CallerLineNumber attributes for optional parameters, the parameters get set with the file path, line number, and member name of the caller. The values are set into the method call at compile time, don't have a performance penalty, and are not affected by obfuscation.

[Example from msdn:](http://msdn.microsoft.com/en-us/library/system.runtime.compilerservices.callermembernameattribute(v=vs.110).aspx?cs-save-lang=1&cs-lang=csharp#code-snippet-2)
```groovy
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

Out of the different APIs available, HttpClient is the new one that supports async/await and automatic request decompression. 

### Know the timers

Ther are different timers for different purposes. For example [DispatcherTimer for WinRT](http://msdn.microsoft.com/en-us/library/windows/apps/xaml/windows.ui.xaml.dispatchertimer.aspx), [DispatcherTimer for WP Silverlight](http://msdn.microsoft.com/en-us/library/windows/apps/system.windows.threading.dispatchertimer(v=vs.105).aspx) and [ThreadPoolTimer](http://msdn.microsoft.com/en-us/library/windows/apps/windows.system.threading.threadpooltimer.aspx).

### #1 thing to know about LINQ

LINQ creates a query that is re-executed whenever the collection is accessed. Use ToArray, ToList, etc. extension methods to avoid re-execution when utilizing the collection multiple times.

## Windows App Development 

### Do not hardcode a name for your custom controls (as in set x:Name)

When creating custom or user controls, do not set the x:Name for the control itself.

Each dependency object in a PresentationFrameworkCollection has to have an unique Name, and if you end up adding two controls with the same name into the same PresentationFrameworkCollection, you'll end up with:
```
{System.ArgumentException: Value does not fall within the expected range.
   at MS.Internal.XcpImports.CheckHResult(UInt32 hr)
   at MS.Internal.XcpImports.Collection_AddValue[T](PresentationFrameworkCollection`1 collection, CValue value)
   at MS.Internal.XcpImports.Collection_AddDependencyObject[T](PresentationFrameworkCollection`1 collection, DependencyObject value)
   at System.Windows.PresentationFrameworkCollection`1.AddDependencyObject(DependencyObject value)
```

When you don't set the name yourself, the framework will generate an unique name for each instance.

### Be very careful when binding into multiple dependency properties of a dependency object

There are two possible suprises:

#### Order matters

For example: [1](http://www.weseman.net/blog/development/c/order-in-xaml-is-important-when-using-data-binding/) and [2](http://discoveringdotnet.alexeyev.org/2011/03/order-in-xaml-is-important.html)

#### Binding to DataContext changes the default binding source

For example, the following xaml looks for the AdVisiblity in MyAdViewModel, not in the MyPageViewModel. Changing the order of the bindings doesn't make a difference. (However, does it first look in MyPageViewModel and then in MyAdViewModel).

```groovy
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

This happens because "{Binding PropertyName}" is short for "{Binding Path=DataContext.PropertyName, Source={RelativeSource Self}". It actually binds to the property PropertyName in the object in the DataContext property of its self. When DataContext is not set, it's automatically inherited from the Parent.

### Application.Resources

### AppTheme.xaml

### Use [CallerMemberName](http://msdn.microsoft.com/en-us/library/system.runtime.compilerservices.callermembernameattribute(v=vs.110).aspx) attribute or a [LINQ expression](http://msdn.microsoft.com/en-us/library/system.linq.expressions.expression(v=vs.110).aspx) to help with notifying property changes.

Many MVVM frameworks already help you with notifying property changes from your viewmodels. However, if you don't use any of those, create a base viewmodel class for yourself.

For example:
```groovy
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

## Visual Studio Extensions

### Productivity Power Tools ([2013](https://visualstudiogallery.msdn.microsoft.com/dbcb8670-889e-4a54-a226-a48a15e4cace))

A free visual studio extension from Microsoft. It lacks some features of the commercial JustCode or ReSharper, but doesn't seem to slow your IDE down at all either.
