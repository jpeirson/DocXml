# DocXml - C# XML documentation reader 

[![Build status](https://ci.appveyor.com/api/github/webhook?id=06wykx631o8vs4wc)]

This component is a set of helper classes and methods for compiler-generated XML documentation retrieval.
XML documentation as described here https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/xmldoc/xml-documentation-comments
is generated by compiler from source code comments and saved in a XML file where each
documented item is identified by its unique name. These names sometimes are quite complicated especially
when templates and generic types are involved. DocXml makes retrieval of XML documentation easy. For example
here is the class with generic method and the piece of code that retrieves method documentation.

```csharp
// Source code for XML documentation
namespace LoxSmoke.DocXmlUnitTests
{
    class MyClass2
    {
        /// <summary>
        /// TemplateMethod description
        /// </summary>
        /// <returns>Return value description</returns>
        public List<T> TemplateMethod<T>()
        { return null; }
    }
}
```

```csharp
var minfo = typeof(MyClass2).GetMethod("TemplateMethod");

DocXmlReader reader = new DocXmlReader("DocXmlUnitTests.xml");
var comments = reader.GetMethodComments(minfo);
Console.WriteLine(comments.Summary);
Console.WriteLine(comments.Returns);
```

## How to use

DocXml is available as LoxSmoke.DocXml nuget package. It is built as .net standard 2.0 class library.
Current version is 1.1.0

## Classes and methods

The main class is **DocXmlReader**. It reads XML file and returns documentation/comments objects.

Comment classes represent one or more comments associated with each item in the source code. Simple summary 
comments are returned as strings and more complex comments are returned as comments objects. Here is the list of 
comments classes:
* **CommonComments** is the base class of all comments classes.
* **TypeComments** is documentation of the class or struct. 
* **MethodComments** is documentation of the method, constructor, operator or property with parameter descriptions.
* **EnumComments** is documentation of the Enum type. Comments of enum values are included here as well.

Static **XmlDocId** class is a set of static methods that generates IDs used for retrieval of documentation from 
XML file. This class is used by **DocXmlReader**.   

## Documentation comments examples

Simple summary comment can be located next to class definition, property, method, field, etc.
```csharp
/// <summary>
/// This is class comment
/// </summary>
```
**DocXmlReader** method **GetMemberComment** returns this comment as string.
This comment is also available as **Summary** property in all comments classes.

Delegate type may have comments for each parameter. 
```csharp
/// <summary>
/// Delegate type description 
/// </summary>
/// <param name="parameter">Parameter description</param>
public delegate void DelegateType(int parameter);
```
**DocXmlReader** method **GetTypeComment** returns this comment as **TypeComments** object with **Summary** property and the list of parameters as **Parameters** property. **Parameters** property is the list of tuples where **Item1** is the name of the parameter and **item2** is the parameter description comment.

Enum comment with documented values.
```csharp
/// <summary>
/// Enum type description
/// </summary>
public enum TestEnum
{
    /// <summary>
    /// Enum value one
    /// </summary>
    Value1,
     
    /// <summary>
    /// Enum value two
    /// </summary>
    Value2
};
```
**DocXmlReader** method **GetEnumComments** returns this comment as **EnumComments** object with **Summary** property and the list of **ValueComments**. Each element in **ValueComments** is the tuple where **Item1** is the name of the value e.g. **Value1** and **Item2** is the summary comment of the value. If none of values has any summary comment then **ValuesComments** list is empty.

Method comment with parameters and return value.
```csharp
/// <summary>
/// Member function description.
/// </summary>
/// <param name="one">Parameter one</param>
/// <param name="two">Parameter two</param>
/// <returns>Return value description</returns>
public int MemberFunction2(string one, ref int two)
```
**DocXmlReader** method **GetMethodComments** returns this comment as **MethodComments** object with **Summary** and **Returns** properties and the list of **Parameters** tuples. Each element in **Parameters** is the tuple where **Item1** is the name of the parameter e.g. **one** and **Item2** is the comment of the parameter value.


Web controller method comment with response codes.
```csharp
/// <summary>
/// Member function description
/// </summary>
/// <returns>Return value description</returns>
/// <response code="200">OK</response>
```
**DocXmlReader** method **GetMethodComments** returns this comment as **MethodComments** object with **Summary** and **Returns** properties and the list of **Responses** tuples. Each element in **Responses** is the tuple where **Item1** is the code of the response e.g. **200** and **Item2** is the comment of the value e.g. **OK**.






