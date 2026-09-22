# Avae.Services

**Platform-agnostic service contracts** for the [Avae](https://github.com/cedric56/Avae.Abstractions) stack.

ViewModels and shared code depend on these interfaces. Avalonia, MAUI, Blazor, and other hosts provide the implementations.

> **Status:** preview (`1.0.0-preview.1`)  
> **TFM:** `net11.0` · no UI framework dependency · AOT-friendly

---

## Install

```xml
<PackageReference Include="Avae.Services" Version="1.0.0-preview.1" />
```

Zero package dependencies beyond the BCL.

---

## What’s inside

| Contract | Role |
|----------|------|
| **`IDialogService`** | Generic modal / dialog show-await API |
| **`IContentDialogService`** | Content-style dialogs (title, content, primary/secondary commands) |
| **`ITaskDialogService`** | Task-dialog style prompts (standard results: OK/Cancel, Yes/No, …) |
| **`INotificationService`** | In-app notifications / snackbars / toasts (UI chrome, not OS) |
| **`ISystemNotificationService`** | OS / system tray-style notifications |
| **`IRequestedThemeService`** | Request Light / Dark / Default theme |

Also included:

- Supporting types: dialog params, notification models, `SystemNotificationEventArgs`, `RequestedTheme`, etc.

Implementations live in other packages, for example:

| Interface | Typical implementation package |
|-----------|--------------------------------|
| Dialogs / content / task / in-app notifications | **Avae.Avalonia**, **Avae.Razor**, … |
| `ISystemNotificationService` | **Avae.Notifications** |
| Theme | Host-specific (Avalonia / MAUI / Blazor adapters) |

---

## Quick examples

### System notifications (interface only here)

```csharp
public partial class HomeViewModel(ISystemNotificationService? systemNotifications)
{
    public async Task NotifyAsync()
    {
        var n = await systemNotifications?.CreateNotification("default");
        if (n is null) return;
        n.Title = "Hello";
        n.Message = "From shared ViewModel";
        await n.Show(); // depending on interface shape in your version
    }
}
```

Register the **platform** implementation in the host (`UseNotifications`, `WithSystemNotifications`, …) — not in this package.

### Theme

```csharp
themeService.Request(RequestedTheme.Dark);
// or RequestedTheme.Default → follow OS when the host supports it
```

### Dialogs

```csharp
var result = await taskDialog.ShowAsync(
    new TaskDialogParams { Title = "Confirm", /* … */ },
    TaskDialogStandardResult.Yes,
    TaskDialogStandardResult.No);
```

Exact parameter types are documented on each interface in source XML comments.

---

## Design rules

1. **No UI types** in this package — no Avalonia, MAUI, or Blazor references.
2. **ViewModels** depend only on `Avae.Services` (+ `Avae.ViewModels` if used).
3. **Hosts** register concrete services at startup.
4. Optional services (e.g. system notifications on WASM) should be registered only when available, or resolved with `GetService` / nullable injection.

---

## Project layout

```
IDialogService.cs
IContentDialogService.cs
ITaskDialogService.cs
INotificationService.cs
ISystemNotificationService.cs
IRequestedThemeService.cs
```

---

## Versioning

Semver **preview**. Interface changes may occur until 1.0 stable. Prefer depending on this package from shared ViewModel projects so hosts can swap implementations without rewriting business code.

---

## License

MIT — see [LICENSE.txt](LICENSE.txt).

---

## Related packages

- [Avae.Abstractions](https://github.com/cedric56/Avae.Abstractions) — samples & solution overview  
- [Avae.ViewModels](https://github.com/cedric56/Avae.Abstractions) — navigation / MVVM core  
- [Avae.Notifications](https://github.com/cedric56/Avae.Notifications) — `ISystemNotificationService` implementations  
- [Avae.Avalonia](https://github.com/cedric56/Avae.Abstractions) / **Avae.Razor** — dialog & in-app notification adapters  
