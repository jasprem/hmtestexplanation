
```csharp
public static class ListExtensions
{
    public delegate void SelectorDelegate<in T>(T source);
    public delegate TOut SelectorDelegate<in T, out TOut>(T source);

    public static void ModifyUsingDelegate<T>(this List<T> source, SelectorDelegate<T> selectorDelegate)
    {
        foreach (var s in source)
        {
            selectorDelegate(s);
        }
    }

    public static IEnumerable<TOut> ModifyUsingDelegateAndReturnNew<T, TOut>(this List<T> source, SelectorDelegate<T, TOut> selectorDelegate)
    {
        return source.Select(s => selectorDelegate(s));
    }

    public static void Modify<T>(this List<T> source, Action<T> selector)
    {
        if (source == null)
        {
            throw new ArgumentNullException("source");
        }

        if (selector == null)
        {
            throw new ArgumentNullException("selector");
        }

        foreach (var s in source)
        {
            selector(s);
        }
    }

    public static IEnumerable<TOut> ModifyAndReturnNew<T, TOut>(this List<T> source, Func<T, TOut> selector)
    {
        if (source == null)
        {
            throw new ArgumentNullException("source");
        }

        if (selector == null)
        {
            throw new ArgumentNullException("selector");
        }

        return source.Select(s => selector(s));
    }
}
```

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }

    public Person(string name, int age)
    {
        Name = name;
        Age = age;
    }
}
```
```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var persons = new List<Person>
        {
            new("prem", 10)
        };

        //1
        static void SelectorDelegate(Person p)
        {
            p.Age += 1;
        }
        persons.ModifyUsingDelegate(SelectorDelegate);
        Print(persons);

        //2
        persons.ModifyUsingDelegate(p => p.Age += 1);
        Print(persons);

        //3
        persons.Modify(p => p.Age += 1);
        Print(persons);

        //4
        var personsList = persons.ModifyUsingDelegateAndReturnNew(p =>
        {
            p.Age += 1;
            return p;
        });
        Print(personsList);
        Print(persons);

        //5
        var personsList1 = persons.ModifyAndReturnNew(p =>
        {
            p.Age += 1;
            return p;
        });
        Print(personsList1);
        Print(persons);
    }

    private static void Print(IEnumerable<Person> persons)
    {
        foreach (var person in persons)
        {
            Console.WriteLine($"{person.Name} age is {person.Age}");
        }
    }
}
```
