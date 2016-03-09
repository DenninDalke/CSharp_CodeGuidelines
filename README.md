# csharp-code-guidelines

Do:

```cs
public void AddInitListener(Type type, Action action)
{
    var variable = this;
    while (variable != null)
    {
        var objectType = system.GetType();
        if (objectType != type && !objectType.IsSubclassOf(type))
        {
        	variable = variable.Service;
        	continue;
        }

        if (variable.Initialized)
        {
            action();
            break;
        }

        variable.OnInitialize += action;
        break;
    }
}
```

Istead of:
```cs
public void AddInitListener(Type type, Action action)
{
    var variable = this;
    while (variable != null)
    {
        var objectType = system.GetType();
        if (objectType == type || objectType.IsSubclassOf(type))
        {
            if (variable.Initialized)
            {
                action();
            }
            else
            {
                variable.SomeAction += action;
            }
            break;
        }
        variable = variable.Service;
    }
}
```

Or maybe change to a recursive call instead of while:
```cs
public void AddInitListener(Type type, Action action)
{
    this.TryToAddFinishedListener(type, a, this);
}

private void TryToAddListener(Type type, Action action, IInitializable system)
{
    if (system == null)
    {
        return;
    }

    var systemType = system.GetType();
    if (systemType != type && !systemType.IsSubclassOf(type))
    {
        this.TryToAddFinishedListener(type, action, system.SubSystem);
        return;
    }

    if (system.InitScript.Inited)
    {
        action();
        return;
    }

    system.InitScript.OnInitialize += action;
}
```