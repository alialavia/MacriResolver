# MacroResolver

A simple C# solution for resolving native macros on *nix systems.

## Dependencies
* gcc

## Why do I need this?
* Although many *nix system calls have the same signature and functionality, I figured that macro values differ widely on different platforms. Hence, if you want to PInvoke a native function, use MacrorResolver for maximum portability, instead of hard coding macro values.

## How to use

* Define a static class.
* Define and initialize a MacroHelper instance, and pass the type of the macro and list of header files to the constructor.
* Define the desired macros as public static int, readonly properties, and return GetMacro() method in the getter.

Example:

```csharp
static class MyMacros
{
	private static MacroHelper macroHelper = new MacroHelper(typeof(MyMacros), new String[]{"termios.h", "unistd.h", "sys/ioctl.h", "fcntl.h"});
	public static int ICANON {get { return macroHelper.GetMacro(); }}
	public static int ISIG {get { return macroHelper.GetMacro(); }}		
}

class MainClass
{
	public static void Main (string[] args)
	{
	  Console.WriteLine ("ICANON = {0}", MyMacros.ICANON);
	  Console.WriteLine ("ISIG = {0}", MyMacros.ISIG);
	}
}
```

## Limitations
* Only supports integer macros.
* Throws an ArgumentNullException if the macro doesn't exist on the paltfrom or in the given header files.
* stdio.h macros are always included.

## How it works
* It creates a small C code with the provided header files included, and prints the values of macros with names similar to those of defined public properties on the given type, if such macro exists. It then compiles the C code, executes it and store the results.

## Performance
* There would be an initial macro resolving time (on Ubuntu 14, with 100 macros, and on an i&-2600, 3.4 GHz CPU with 16 GB memory, it's 100ms), which happens only on the first access to your class (MyMacros in the example above). After that it would be only resolving values from an in-memory dictionary.



