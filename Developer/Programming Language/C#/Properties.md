# Auto-Implemented Properties

Example of auto-implemented properties:

```C#
public string FirstName { get; set; } = "cuongnlt"; 
```

The compiler creates a private, anonymous backing field that can only be accessed through the property's `get` and `set` accessors like the following code snippet:

```C#
private string firstName; // The backing field
public string FirstName; // Property
{
	get => name;
	set => name = "cuongnlt";
}
```

