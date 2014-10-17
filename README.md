win-client-dev-good-practices
=============================

A collection windows phone and windows 8 app development good practices from Futurice

-----------------------------

## General C# Programming

### Use IEnumerables and [yield](http://msdn.microsoft.com/en-us/library/9k7k7cf0.aspx) when ever possible

Rather than writing something like:

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

write this instead:

  public System.Collections.Generic.IEnumerable<Galaxy> Galaxies {
      get {
        yield return new Galaxy { Name = "Tadpole", MegaLightYears = 400 };
        yield return new Galaxy { Name = "Pinwheel", MegaLightYears = 25 };
        yield return new Galaxy { Name = "Milky Way", MegaLightYears = 0 };
        yield return new Galaxy { Name = "Andromeda", MegaLightYears = 3 };
      }
  }

Automatically returns empty IEnumreable if no "yield return" is called -> no nulls when expecting an IEnumerable.
Avoids unnecessary work when all the items are not enumerated.

## Split code into small methods

Code is often split into small methods for reusability. However, there are reasons to split your methods even if you don't plan to reuse them. Method name documents the intent of the code it encloses. Additonally, you get more informative stacktraces from your exceptions when your code is split into smaller methods.

## Windows App Development 

### Do not hardcode a name for your custom controls (as in set x:Name)

When creating custom or user controls, do not set the x:Name for the control itself.

Each dependency object in a PresentationFrameworkCollection has to have an unique Name, and if you end up adding two controls with the same name into the same PresentationFrameworkCollection, you'll end up with:

{System.ArgumentException: Value does not fall within the expected range.
   at MS.Internal.XcpImports.CheckHResult(UInt32 hr)
   at MS.Internal.XcpImports.Collection_AddValue[T](PresentationFrameworkCollection`1 collection, CValue value)
   at MS.Internal.XcpImports.Collection_AddDependencyObject[T](PresentationFrameworkCollection`1 collection, DependencyObject value)
   at System.Windows.PresentationFrameworkCollection`1.AddDependencyObject(DependencyObject value)

When you don't set the name yourself, the framework will generate an unique name for each instance.


