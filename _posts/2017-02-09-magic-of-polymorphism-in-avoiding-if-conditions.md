---
layout: post
title: "Magic of Polymorphism in Avoiding If conditions"
date: 2017-02-09
author: ehsan
categories: [Software Architecture, OOP]
excerpt_separator: <!--more-->
---

You may have faced with situations that you need to make a lot of decisions based on an unknown (runtime) situation. You might have used if-then-else or switch-case statements to implement the code.

Probably the worst thing about conditional statements is that they are difficult to maintain. For any future changes, you have to come and alter the code.

> Objects or entities should be open for extension, but closed for modification.
> 
> **Open-closed Principle** 

Conditional statements are easy to write because that’s how our brains work but are often difficult to read. Especially when there are many conditions or when we make them nested.
<!--more-->
#### OOP solution

OOP concepts can be difficult to grasp and one needs lots of practices in order to master it. Polymorphism which is considered one of the pillars of OOP can provide a neat solution to many decision-making situations.

It doesn’t mean you will write less code. On the contrary, you will often find yourself writing more lines of code. After all, OOP is famous for being verbose (Banana-Gorilla-Jungle story). But we will achieve a better extensibility and maintainability position.

The classic example of using polymorphism instead of if-statement is the Shape class and it’s Draw() method. But I am going to demonstrate a more practical instance.

#### Example: decision-making matrix

In one case, I had a matrix of statuses that an entity could have. The entity had a current status, and this status could be changed to something else but not everything else (actually it was more complex than this but I skip those for sake of simplicity).

The matrix was a guideline to which other statuses we could change the current one. Normally we would write many many if-statements to cover the requirement, though there is a better approach to do that.

You see, there are lots of Statuses, but eventually, all of them are status. So, you can create a Base class of Status and all the required methods that the derived classes can inherit.

```csharp
public class Status : IEquatable<Status>
{
    private readonly StatusNames _status;

    public StatusNames Status
    {
        get { return _status; }
    }

    public Status(StatusNames status)
    {
        _status = status;
    }

    public bool IsChangeAllowedTo(Status status)
    {
        return GetAllowedChangeToList().Contains(status);
    }

    public virtual ICollection<Status> GetAllowedChangeList()
    {
        return new List<Status>();
    }

    public virtual Status Next()
    {
        return null;
    }

    public virtual Status Previous()
    {
        return null;
    }

    #region IEquatable

    public bool Equals(Status other)
    {
        if (ReferenceEquals(null, other)) return false;
        if (ReferenceEquals(this, other)) return true;
        return _status == other._status;
    }

    public override bool Equals(object obj)
    {
        if (ReferenceEquals(null, obj)) return false;
        if (ReferenceEquals(this, obj)) return true;
        if (obj.GetType() != this.GetType()) return false;
        return Equals((Status)obj);
    }

    public override int GetHashCode()
    {
        unchecked
        {
            return ((int)_status* 397) ^ (int)_status;
        }
    }

    #endregion

}
```

First of all, my class implements IEquatable because later I need to compare its instances.
*IsChangeAllowedTo()* method is very generic and can be shared between all derived classes, but derived classes can implement their own *GetAllowedChangeList()* collection or otherwise rely on the empty collection of the base class.

Now, we can create as many Status derived class as we want:

```csharp
public class InProgress : Status
{
    public InProgress()
        : base(StatusNames.InProgress)
    {
    }

    public override ICollection<Status> GetAllowedChangeToList()
    {
        return new List<Status>
            {
                    new Completed(),
                    new Rejected()
            };
    }

    public override Status Next()
    {
        return new Completed();
    }

    public override Status Previous()
    {
        return new NotStarted();
    }
}
```

#### Factory Pattern

So, how do we create a specific instance of *Status* class? Our calling code should be ignorant of the underlying structure, isn’t it?

A simple Factory pattern can be used here to make this abstraction of code:

```csharp
public class StatusFactory
{
    public static Status GetStatus(StatusNames status)
    {
        var Status = new Status(status);

        var StatusList = typeof(Status)
            .Assembly.GetTypes()
            .Where(a => a.IsSubclassOf(typeof(Status)) && !a.IsAbstract)
            .Select(a => (Status)Activator.CreateInstance(a));

        return StatusList.FirstOrDefault(Status.Equals);
    }
}
```

The calling code simply calls *StatusFactory.GetStatus()* and passes whatever status it has got (based on our Enum) and get the right derived class from this factory method.

#### How to use?

You saw that every derived class has its own collection of allowed changes, and it also knows it’s Previous and Next status.

Therefore, in the main application – instead of writing 1000 lines of if-then-else – you just write:

```csharp
var currentStatus = StatusFactory.GetStatus(StatusNames.InProgress);
var nextStatus = currentStatus.Next();
```

Done!
