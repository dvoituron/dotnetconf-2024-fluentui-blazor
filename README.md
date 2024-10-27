# DotNetConf 2024 - Fluent UI Blazor - Quick Start

Demo used in the [Exploring the New Fluent UI Blazor Library](https://www.dotnetconf.net/) session at .NET Conf 2024

This project demonstrates how to use the [Fluent UI Blazor Library](https://www.fluentui-blazor.net/).

> **note**: This code is for step-by-step learning only.
> It does not respect development rules and/or best practices.
> This project presents only a small part of the possibilities offered by **Fluent UI Blazor library**.
> Some shortcuts have been applied to improve understanding of the project/code.

## Agenda

|Step|Branch|
|---|---|
|[1. FluentUI Blazor Template](#step1)|[branch 1](https://github.com/dvoituron/dotnetconf-2024-fluentui-blazor/tree/steps/01-template)|
|[2. Weather page](#step2)|[branch 2](https://github.com/dvoituron/dotnetconf-2024-fluentui-blazor/tree/steps/02-weather)|
|[3. Add a Registration page](#step3)|[branch 3](https://github.com/dvoituron/dotnetconf-2024-fluentui-blazor/tree/steps/03-register)|
|[4. Add a confirmation Dialog](#step4)|[branch 4](https://github.com/dvoituron/dotnetconf-2024-fluentui-blazor/tree/steps/04-dialog)|
|[5. Replace by a Toast message](#step5)|[branch 5](https://github.com/dvoituron/dotnetconf-2024-fluentui-blazor/tree/steps/05-toast)|
|[6. Add an Autocomplete with countries](#step6)|[branch 6](https://github.com/dvoituron/dotnetconf-2024-fluentui-blazor/tree/steps/06-autocomplete-country)|
|[7. Use the AppBar](#step7)|[branch 7](https://github.com/dvoituron/dotnetconf-2024-fluentui-blazor/tree/steps/07-app-bar)|
|[8. Theme and Dark mode](#step8)|[branch 8](https://github.com/dvoituron/dotnetconf-2024-fluentui-blazor/tree/steps/08-theme)|

## After completing all the steps, the final result will look like this:

![Final result](doc/final.gif)

## 1. FluentUI Blazor Template
<a name="step1"></a>

- Install the latest version of the [FluentUI Blazor Template](https://www.nuget.org/packages/Microsoft.FluentUI.AspNetCore.Templates).

  ```bash
  dotnet new install Microsoft.FluentUI.AspNetCore.Templates
  ```

- Open [Visual Studio 2022](https://visualstudio.microsoft.com/)
  and create a new Fluent Blazor Web App.

  > This template contains exactly the same pages and functions as the default Blazor template.
  > A **Home** page, a **Counter** page and a **Weather** page containing a data grid.
  > All these screens have been graphically adapted using these FluentUI Blazor components.

   
  This template automatically includes the following NuGet packages:
    - [Microsoft.FluentUI.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.FluentUI.AspNetCore.Components)
    - [Microsoft.FluentUI.AspNetCore.Components.Icons](https://www.nuget.org/packages/Microsoft.FluentUI.AspNetCore.Components.Icons)

## 2. Weather page
<a name="step2"></a>

We're going to adapt the **Weather data grid** using a **Progress Ring**, customising a **column** with icons 
and adding a few **filtering** and data **sorting** functions.

1. Replace the 'Loading...' label with this component to display a **Progress Ring** during the loading phase.

   ```xml
   <FluentProgressRing>Loading...</FluentProgressRing>
   ```

2. Add a **TemplateColumn** including icons.

   ```xml
   <FluentDataGrid Id="weathergrid" Items="@forecasts" GridTemplateColumns="1fr 1fr 1fr 1fr 2fr" TGridItem="WeatherForecast">
       <!--                                                                             ^^^                               -->
       <PropertyColumn Title="Date" Property="@(c => c!.Date)" Align="Align.Start"/>
       <PropertyColumn Title="Temp. (C)" Property="@(c => c!.TemperatureC)" Align="Align.Center"/>
       <PropertyColumn Title="Temp. (F)" Property="@(c => c!.TemperatureF)" Align="Align.Center"/>
       <!-- ***************************************** -->
       <TemplateColumn Title="Icon">
           @if (context.TemperatureC > 10)
           {
               <FluentIcon Value="@(new Icons.Regular.Size24.WeatherHailNight())"
                           Color="@Color.Neutral" />
           }
           else
           {
               <FluentIcon Value="@(new Icons.Regular.Size24.TimeAndWeather())"
                           Color="@Color.Neutral" />
           }
       </TemplateColumn>
       <!-- ***************************************** -->
       <PropertyColumn Title="Summary" Property="@(c => c!.Summary)" Align="Align.End"/>
   </FluentDataGrid>
   ```

   Update the **GridTemplateColumns** attribute (adding a `1fr`) to define the width of this new column.

   > This attribute respect the syntax used by the 
   > CSS [grid-template-column](https://developer.mozilla.org/en-US/docs/Web/CSS/grid-template-columns) attribute.

   ```xml
   GridTemplateColumns="1fr 1fr 1fr 1fr 2fr"
   ```

3. Allow resizing and sorting behaviors

   As descrive on the page: This page is rendered in **SSR mode**, so the `FluentDataGrid` component 
   does not offer any interactivity (like sorting)

   To solve that, add this attribute in the top of the page.

   ```razor
   @rendermode RenderMode.InteractiveServer
   ```

   > You can find more details on Render Modes on this [Blog page](https://dvoituron.com/2024/01/23/blazor-render-modes/).

   Next, add the :
   - The `ResizableColumns="true"` attribute to the **FluentDataGrid** element.
   - The `Sortable="true"` to the **Temp. (C)** column.


## 3. Add a Registration page
<a name="step3"></a>

1. Add a new Register.razor page with this content.

    ```xml
    @page "/register"
    @rendermode RenderMode.InteractiveServer

    <PageTitle>New user</PageTitle>

    <FluentLabel Typo="Typography.PageTitle">Register</FluentLabel>

    <FluentLabel Style="margin-bottom: 24px;">
        Let's get you all set up so you can verify your personal account and begin
        setting up your profile.
    </FluentLabel>

    <EditForm Model="@Data" OnValidSubmit="ValidHandlerAsync">
        <FluentTextField Label="First name:"
                        Placeholder="Enter your first name"
                        Required
                        @bind-Value="@Data.FistName" />
        <FluentTextField Label="Last name:"
                        Placeholder="Enter your last name"
                        @bind-Value="@Data.LastName" />
        <FluentDatePicker Label="Birth date:"
                          @bind-Value="@Data.BirthDate" />
        <FluentTextField Label="EMail:"
                        TextFieldType="TextFieldType.Email"
                        Required
                        Placeholder="Enter your email"
                        @bind-Value="@Data.Email" />
        <br />
        <FluentCheckbox Label="I agree to all terms, conditions, privacy policy."
                        @bind-Value="@AcceptToCreate" />

        <div style="margin: 24px 0px;" />

        <FluentButton Appearance="Appearance.Accent"
                      Loading="@Loading"
                      Disabled="@(!AcceptToCreate)"
                      Type="ButtonType.Submit">
            Create account
        </FluentButton>
    </EditForm>

    <style>
        form fluent-text-field {
            margin-bottom: 24px;
        }
    </style>
    ```

    ```csharp
    @code {
        private RegisterUser Data = new();
        private bool AcceptToCreate = false;
        private bool Loading = false;

        private async Task ValidHandlerAsync()
        {
            Loading = true;

            // Simulate asynchronous loading
            await Task.Delay(1000);

            Loading = false;
        }

        public class RegisterUser
        {
            public string? LastName { get; set; }
            public string? FistName { get; set; }
            public DateTime? BirthDate { get; set; }
            public string? Email { get; set; }
        }
    }
    ```

2. Add a new item in the Left Navigation menu.

    ```xml
    <FluentNavLink Icon="@(new Icons.Regular.Size20.PlugConnectedSettings())"
                Href="/Register">Register</FluentNavLink>
    ```

## 4. Add a confirmation Dialog
<a name="step4"></a>

1. Update the **App.razor** file, adding `@rendermode="RenderMode.InteractiveServer"` to define the entire application as using **InteractiveServer** mode.

```xml
<body>
    <Routes @rendermode="RenderMode.InteractiveServer" />
    <script src="_framework/blazor.web.js"></script>
</body>
```

2. At the end of the **MainLayout.razor** file, add this line.
   These Providers define where the HTML code will be generated. By placing it at the end of the file, these components will be rendered 'above' all the other elements of your web page.

    ```xml
    <FluentToastProvider />
    <FluentDialogProvider />
    <FluentTooltipProvider />
    <FluentMessageBarProvider />
    <FluentMenuProvider />
    ```

4. In the **Registry.razor** page, inject this service and add the **Dialog Message**.
 
    ```csharp
    @inject IDialogService DialogService

    private async Task ValidHandlerAsync()
    {
        Loading = true;

        // Simulate asynchronous loading
        await Task.Delay(1000);

        // Confirmation
        await DialogService.ShowInfoAsync(
                    message: "Your account has been successfully created",
                    title: "New user");

        Loading = false;
    }
    ```

5. Add this new **AccountCreatedDialog.razor** file , implementing `IDialogContentComponent`, to customize the Dialog content.

    ```xml
    @implements IDialogContentComponent

    @* Header *@
    <FluentDialogHeader ShowDismiss="true">
        <FluentStack VerticalAlignment="VerticalAlignment.Center">
            <FluentIcon Value="@(new Icons.Regular.Size24.PersonAdd())" />
            <FluentLabel Typo="Typography.PaneHeader">
                @Dialog.Instance.Parameters.Title
            </FluentLabel>
        </FluentStack>
    </FluentDialogHeader>

    @* Footer *@
    <FluentDialogFooter>
        <FluentButton Appearance="Appearance.Accent"
                    OnClick="@SaveAsync">
            Close
        </FluentButton>
    </FluentDialogFooter>

    @* Body *@
    <FluentDialogBody>
        <FluentLabel Typo="Typography.Subject" MarginBlock="10px;">
            Thanks for being awesome!
        </FluentLabel>
        <FluentLabel>
            Thank you for registering your new request.
            If your request is urgent, please use our telephone number to speak to one
            of our customer service representatives.
            You can also reach us via <a href="https://dotnet.microsoft.com" 
                                        target="_blank">our documentation page</a>.
        </FluentLabel>
    </FluentDialogBody>

    @code {
        [CascadingParameter]
        public required FluentDialog Dialog { get; set; }

        private async Task SaveAsync()
        {
            await Dialog.CloseAsync();
        }
    }
    ```

4. Replace the previous `DialogService.ShowInfoAsync` method by this new `ShowDialogAsync<AccountCreatedDialog>`, to call the **customized dialog box**.

    ```csharp
    // await DialogService.ShowInfoAsync("Your account has been successfully created",
    //                                   "New user");

    await DialogService.ShowDialogAsync<AccountCreatedDialog>(new DialogParameters()
    {
        Title = "New user",
    });
    ```

## 5. Replace by a Toast message
<a name="step5"></a>

1. In the **Registry.razor** page, inject this service and add the Toast Message.

    ```csharp
    @inject IToastService ToastService
    ```

    ```csharp
    ToastService.ShowSuccess("Your account has been successfully created");
    ```

## 6. Add an Autocomplete with countries
<a name="step6"></a>

1. Add this class **Country.cs** to have a list of all countries.
  
   > note: Copy the full code from [this file](https://github.com/dvoituron/dotnetconf-2023-fluentui-blazor/blob/steps/07-with-autocomplete-country/src/DemoFluentUI/Components/Pages/Country.cs).

    ```csharp
    using Microsoft.AspNetCore.Components;

    public record Country(string Code, string Name)
    {
        public string Flag => 
        $"https://fluentui-blazor.net/_content/FluentUI.Demo.Shared/flags/{Code}.svg";

        public MarkupString HtmlFlagAndName => (MarkupString)
            @$"<div style=""display: flex; gap: 10px;"">
                <img src=""{Flag}"" width=""24"" />
                <div>{Name}</div>
            </div>";

        public static IEnumerable<Country> All
        {
            get
            {
                // List generated using https://chat.openai.com/
                return new Country[]
                {
                    new Country("af", "Afghanistan"),
                    new Country("al", "Albania"),
                    new Country("dz", "Algeria"),
                    new Country("as", "American Samoa"),
                    new Country("ad", "Andorra"),

                    ... // Copy All Countries from the GitHub source code (see the note above).

                    new Country("ye", "Yemen"),
                    new Country("zm", "Zambia"),
                    new Country("zw", "Zimbabwe"),
                };
            }
        }
    }
    ```

2. Update the **Register.razor** file to add the **FluentAutocomplete** component

    ```xml
    <EditForm Model="@Data" OnValidSubmit="ValidHandlerAsync">
        ...
        @* Country *@
        <FluentAutocomplete TOption="Country"
                            Label="Select a country"
                            Width="250px"
                            Placeholder="Select countries"
                            OnOptionsSearch="@OnSearchAsync"
                            MaximumSelectedOptions="1"
                            OptionText="@(i => i.Name)"
                            @bind-SelectedOptions="@Data.Countries">

            <OptionTemplate Context="country">
                @country.HtmlFlagAndName
            </OptionTemplate>

            <MaximumSelectedOptionsMessage>
                Please select only one country.
            </MaximumSelectedOptionsMessage>
        </FluentAutocomplete>
        ...
    </EditForm>
    ```

    ```csharp
    @code {
        ...

        // Add this new method
        private async Task OnSearchAsync(OptionsSearchEventArgs<Country> e)
        {
            e.Items = Country.All.Where(i => i.Name.StartsWith(e.Text, 
                                                    StringComparison.OrdinalIgnoreCase))
                                .OrderBy(i => i.Name);

            await Task.CompletedTask;
        }

        public class RegisterUser
        {
            public string? LastName { get; set; }
            public string? FistName { get; set; }
            public DateTime? BirthDate { get; set; }
            public string? Email { get; set; }

            // Add this new line
            public IEnumerable<Country> Countries { get; set; } = Array.Empty<Country>();
        }
    }
    ```

## 7. Use the AppBar
<a name="step7"></a>

You can replace the NavMenu with an AppBar to conform more closely to certain applications.

1. In the **NavMenu.razor** file, delete the `NavMenu` elements and use this code.

    ```xml
    <FluentAppBar Style="height: 300px;">
        <FluentAppBarItem Href="/" Text="Home"
                            Match="NavLinkMatch.All"
                            IconRest="@(new Icons.Regular.Size20.Home())"
                            IconActive="@(new Icons.Filled.Size20.Home())"/>

        <FluentAppBarItem Href="counter" Text="Counter"
                            IconRest="@(new Icons.Regular.Size20.NumberSymbolSquare())"
                            IconActive="@(new Icons.Filled.Size20.NumberSymbolSquare())" />

        <FluentAppBarItem Href="weather" Text="Weather"
                            IconRest="@(new Icons.Regular.Size20.WeatherPartlyCloudyDay())"
                            IconActive="@(new Icons.Filled.Size20.WeatherPartlyCloudyDay())" />

        <FluentAppBarItem Href="register" Text="Register"
                            IconRest="@(new Icons.Regular.Size20.PlugConnectedSettings())"
                            IconActive="@(new Icons.Filled.Size20.PlugConnectedSettings())" />

    </FluentAppBar>
    ```

    > You may need to adapt the reduced menu for mobile devices.

## 8. Theme and Dark mode
<a name="step8"></a>

1. in the **MainLayout.razor** file, add a button in the page Header to switch between Dark and Light mode.

    ```xml
    <FluentHeader>
        DemoFluentUI
        <FluentSpacer />
        <FluentButton Appearance="Appearance.Accent"
                      IconStart="@(new Icons.Regular.Size20.DarkTheme())"
                      OnClick="@SwitchTheme"
                      Title="Theme" />
    </FluentHeader>
    ```

2. Add the `FluentDesignTheme` component and the method `SwitchTheme` called by the button.

    ```csharp
    <FluentDesignTheme Mode="@ThemeMode" OfficeColor="OfficeColor.Word" />

    @code
    {
        private DesignThemeModes ThemeMode = DesignThemeModes.System;

        private void SwitchTheme()
        {
            ThemeMode = ThemeMode != DesignThemeModes.Dark ? DesignThemeModes.Dark: DesignThemeModes.Light;
        }
    }
    ```

