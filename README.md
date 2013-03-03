# Caliburn Bindable AppBar

This is a small extension to Caliburn Micro (until it's in Caliburn.Micro proper? :)) 
that adds a bindable appbar, with full support for Pivot and Panorama conductors.

## Features

* Full message functionality
* Bind icons
* Bind text
* Bind bar, button, and menu item visibility
* Support for automatic `Click` convention

## Known Issues

* The Panorama control aborts transitions when you change the ApplicationBar's visibility (`null`, `Opacity = 0`, etc.)
  so to workaround it, there's a small background sleep of a configurable wait time (`PanoramaWaitThreshold`)

## Installation

Install via Nuget (also will install Caliburn.Micro):

	PM> Install-Package Caliburn.Micro.BindableAppBar

Once installed, you can wireup the default `Click` convention in your bootstrapper:

	static void AddCustomConventions()
    {

        // App Bar Conventions
        ConventionManager.AddElementConvention<BindableAppBarButton>(
            Control.IsEnabledProperty, "DataContext", "Click");
        ConventionManager.AddElementConvention<BindableAppBarMenuItem>(
            Control.IsEnabledProperty, "DataContext", "Click");

		// ... the rest of your conventions
	}

## Usage

### Single View

If you only need to bind an appbar on one page, it's very straightforward:

    <bab:BindableAppBar x:Name="AppBar">
        <bab:BindableAppBarButton
            x:Name="Add"
            Text="{Binding AddButtonText}"
            Visibility="{Binding ShowAddButton, Converter={StaticResource BooleanToVisibilityConverter}}"
            IconUri="{Binding ButtonIconUri}"/>
            
        <bab:BindableAppBarMenuItem
            x:Name="Pin"
            Text="{Binding PinItemText}" />
    </bab:BindableAppBar>

### Conducted Page

This is where things get interesting. Often times, you have a conductor with multiple pages.
It's simple to wireup your conductor to the appbar:

    public class SampleConductorBase : Conductor<Screen>.Collection.OneActive {

        public SampleConductorBase() {
                  
        }

        protected override void OnInitialize() {
            base.OnInitialize();
                                 
            Items.Add(new Item1ViewModel());
            Items.Add(new Item2ViewModel() { ShowAppBar = true });
            Items.Add(new Item2ViewModel());

            ActivateItem(Items[0]);

            // Initialize appbar, the views have been attached by this point
            AppBarConductor.Mixin(this);
        }        
    }

There are a few requirements:

1. Your conducted page must contain a `Pivot` or `Panorama` control
2. When you attach the `AppBarConductor`, it is after you have initialized the default items	

You can adjust the `Panorama` animation wait threshold easily:

	AppBarConductor.Mixin(this, panoramaWaitThreshold: 1000);

## More Information

Please see the Sample project in the source for some examples of usage.