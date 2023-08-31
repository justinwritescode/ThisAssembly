![Icon](img/icon-32.png) ThisAssembly
============

[![Version](https://img.shields.io/nuget/vpre/ThisAssembly.svg?color=royalblue)](https://www.nuget.org/packages/ThisAssembly)
[![Downloads](https://img.shields.io/nuget/dt/ThisAssembly.svg?color=green)](https://www.nuget.org/packages/ThisAssembly)
[![License](https://img.shields.io/github/license/devlooped/ThisAssembly.svg?color=blue)](https://github.com//devlooped/ThisAssembly/blob/main/license.txt)
[![Build](https://github.com/devlooped/ThisAssembly/workflows/build/badge.svg?branch=main)](https://github.com/devlooped/ThisAssembly/actions)


Exposes project and assembly level information as constants in the ThisAssembly 
class using source generators powered by Roslyn.

The main generated entry point type is `ThisAssembly` in the global namespace, 
and is declared as partial so you can extend it too with manually created members.

Each package in turn extends this partial class to add their own nestes types 
and members.

The [ThisAssembly](https://nuget.org/packages/ThisAssembly) meta-package includes 
all the other packages for convenience.

> NOTE: for now, ThisAssembly only generates C# code.

## ThisAssembly.AssemblyInfo

[![Version](https://img.shields.io/nuget/vpre/ThisAssembly.AssemblyInfo.svg?color=royalblue)](https://www.nuget.org/packages/ThisAssembly.AssemblyInfo)
[![Downloads](https://img.shields.io/nuget/dt/ThisAssembly.AssemblyInfo.svg?color=green)](https://www.nuget.org/packages/ThisAssembly.AssemblyInfo)

<!-- include src/ThisAssembly.AssemblyInfo/readme.md#content -->
<!-- #content -->
This package generates a static `ThisAssembly.Info` class with public 
constants exposing the following attribute values generated by default for SDK style projects:

* AssemblyConfigurationAttribute
* AssemblyCompanyAttribute
* AssemblyTitleAttribute
* AssemblyDescriptionAttribute
* AssemblyProductAttribute
* AssemblyCopyrightAttribute

* AssemblyVersionAttribute
* AssemblyInformationalVersionAttribute
* AssemblyFileVersionAttribute

If your project includes these attributes by other means, they will still be emitted properly 
on the `ThisAssembly.Info` class.

![](https://raw.githubusercontent.com/devlooped/ThisAssembly/main/img/ThisAssembly.AssemblyInfo.png)

<!-- #content -->
<!-- src/ThisAssembly.AssemblyInfo/readme.md#content -->

## ThisAssembly.Constants

[![Version](https://img.shields.io/nuget/vpre/ThisAssembly.Constants.svg?color=royalblue)](https://www.nuget.org/packages/ThisAssembly.Constants)
[![Downloads](https://img.shields.io/nuget/dt/ThisAssembly.Constants.svg?color=green)](https://www.nuget.org/packages/ThisAssembly.Constants)

<!-- include src/ThisAssembly.Constants/readme.md#content -->
<!-- #content -->
This package generates a static `ThisAssembly.Constants` class with public
constants for `@(Constant)` MSBuild items in the project.

```xml
  <ItemGroup>
    <Constant Include="Foo.Bar" Value="Baz" Comment="Yay!" />
    <Constant Include="Foo.Hello" Value="World" Comment="Comments make everything better 😍" />
  </ItemGroup>
```


![](https://raw.githubusercontent.com/devlooped/ThisAssembly/main/img/ThisAssembly.Constants.png)

In addition to arbitrary constants via `<Constant ...>`, it's quite useful (in particular in test projects) 
to generate constants for files in the project, so there's also a shorthand for those:

```xml
  <ItemGroup>
    <FileConstant Include="@(Content)" />
  </ItemGroup>
```

Which results in:

![](https://raw.githubusercontent.com/devlooped/ThisAssembly/main/img/ThisAssembly.Constants2.png)

<!-- #content -->
<!-- src/ThisAssembly.Constants/readme.md#content -->

## ThisAssembly.Git

[![Version](https://img.shields.io/nuget/vpre/ThisAssembly.Git.svg?color=royalblue)](https://www.nuget.org/packages/ThisAssembly.Git)
[![Downloads](https://img.shields.io/nuget/dt/ThisAssembly.Git.svg?color=green)](https://www.nuget.org/packages/ThisAssembly.Git)

<!-- include src/ThisAssembly.Git/readme.md#content -->
<!-- #content -->
This package generates a static `ThisAssembly.Git` class with constants 
for the following Git properties from the current project:

* Commit
* Sha (first 9 chars from Commit)
* Root (normalized to forward slashes)
* Url (if PublishRepositoryUrl=true)
* Branch (from CI environment variables)

![](https://raw.githubusercontent.com/devlooped/ThisAssembly/main/img/ThisAssembly.Git.png)

This package relies on your project's installed
[Microsoft.SourceLink.*](https://www.nuget.org/packages?q=Microsoft.SourceLink) 
package reference according to your specific Git-based source control server 
(such as GitHub, Azure DevOps, BitBucket, etc).

The `Branch` property is populated from environment variables provided 
by the currently supported CI systems: GitHub Actions, Azure DevOps, 
AppVeyor, TeamCity, Travis CI, Circle CI, GitLab CI, Buddy, and Jenkins.

Whenever the CI system provides a pull request number, the branch name is 
`pr[NUMBER]`, such as `pr123`. This makes it easy to use it as a semver 
metadata label.

> Note: by default, the values of these constants are populated during 
"real" builds (that is, not IDE/design-time builds used to populate 
intellisense). This is to avoid negatively affecting the editor's 
performance. This means, however, that the properties will seem to 
always be empty when inspecting them in the IDE (although never at 
run-time). If you want to force population of these values for 
design-time builds, set the `EnableSourceControlManagerQueries` property to `true`. 
This property is defined and documented by 
[dotnet/sourcelink](https://github.com/dotnet/sourcelink/blob/main/src/SourceLink.Common/build/Microsoft.SourceLink.Common.props#L14).

At the MSBuild level, targets can take a dependency on the provided 
`InitializeGitInformation` target, which sets the equivalent properties
named:

* RepositoryCommit
* RepositorySha
* RepositoryRoot
* RepositoryUrl
* RepositoryBranch

The names of these properties were chosen on purpose to match the 
properties used by [nuget pack](https://learn.microsoft.com/en-us/nuget/reference/msbuild-targets#pack-target) 
and [nugetizer](https://github.com/devlooped/nugetizer) to populate
the relevant package metadata. 

So if you have a GitHub repository, installing these three packages 
will ensure you have the proper metadata out of the box and the simplest 
packaging experience possible:

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>netstandard2.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.SourceLink.GitHub" />
    <PackageReference Include="ThisAssembly.Git" />
    <PackageReference Include="NuGetizer" />
  </ItemGroup>
</Project>
```


<!-- #content -->
<!-- src/ThisAssembly.Git/readme.md#content -->

## ThisAssembly.Metadata

[![Version](https://img.shields.io/nuget/vpre/ThisAssembly.Metadata.svg?color=royalblue)](https://www.nuget.org/packages/ThisAssembly.Metadata)
[![Downloads](https://img.shields.io/nuget/dt/ThisAssembly.Metadata.svg?color=green)](https://www.nuget.org/packages/ThisAssembly.Metadata)

<!-- include src/ThisAssembly.Metadata/readme.md#content -->
<!-- #content -->
This package provides a static `ThisAssembly.Metadata` class with public 
constants exposing each `[System.Reflection.AssemblyMetadata(..)]` defined for 
the project.

![](https://raw.githubusercontent.com/devlooped/ThisAssembly/main/img/ThisAssembly.Metadata.png)

For an attribute declared (i.e. in *AssemblyInfo.cs*) like:

```csharp
[assembly: System.Reflection.AssemblyMetadataAttribute("Foo", "Bar")]
```

A corresponding `ThisAssembly.Metadata.Foo` constant with the value `Bar` is provided. 
The metadata attribute can alternatively be declared using MSBuild syntax in the project 
(for .NET 5.0+ projects that have built-in support for `@(AssemblyMetadata)` items):

```xml
  <ItemGroup>
    <AssemblyMetadata Include="Foo" Value="Bar" />
  </ItemGroup>
```

<!-- #content -->
<!-- src/ThisAssembly.Metadata/readme.md#content -->

## ThisAssembly.Project

[![Version](https://img.shields.io/nuget/vpre/ThisAssembly.Project.svg?color=royalblue)](https://www.nuget.org/packages/ThisAssembly.Project)
[![Downloads](https://img.shields.io/nuget/dt/ThisAssembly.Project.svg?color=green)](https://www.nuget.org/packages/ThisAssembly.Project)

<!-- include src/ThisAssembly.Project/readme.md#content -->
<!-- #content -->
This package generates a static `ThisAssembly.Project` class with public 
constants exposing project properties that have been opted into this mechanism by adding 
them as `ProjectProperty` MSBuild items in the project file, such as:

```xml
  <PropertyGroup>
    <!-- Some arbitrary MSBuild property declared somewhere -->
    <Foo>Bar</Foo>
  </PropertyGroup>
  <ItemGroup>
    <!-- Opt-in to emitting that property value as a constant in ThisAssembly.Project -->
    <ProjectProperty Include="Foo" />
  </ItemGroup>
```

![](https://raw.githubusercontent.com/devlooped/ThisAssembly/main/img/ThisAssembly.Project.png)

<!-- #content -->
<!-- src/ThisAssembly.Project/readme.md#content -->

## ThisAssembly.Resources

[![Version](https://img.shields.io/nuget/vpre/ThisAssembly.Resources.svg?color=royalblue)](https://www.nuget.org/packages/ThisAssembly.Resources)
[![Downloads](https://img.shields.io/nuget/dt/ThisAssembly.Resources.svg?color=green)](https://www.nuget.org/packages/ThisAssembly.Resources)

This package generates a static `ThisAssembly.Resources` class with public 
properties exposing shortcuts to retrieve the contents of embedded resources.

<!-- include src/ThisAssembly.Resources/readme.md#content -->
<!-- #content -->

This package generates a static `ThisAssembly.Resources` class with public 
properties exposing typed APIs to retrieve the contents of embedded resources.


```xml
  <ItemGroup>
    <EmbeddedResource Include="Content/Docs/License.md" />
  </ItemGroup>
```

![](https://raw.githubusercontent.com/devlooped/ThisAssembly/main/img/ThisAssembly.Resources.png)

Since markdown files are text files, the API will expose a `Text` property property 
for it that will read its content once and cache it:

![](https://raw.githubusercontent.com/devlooped/ThisAssembly/main/img/ThisAssembly.Resources2.png)

The `$(EmbeddedResourceStringExtensions)` MSBuild property allows customizing which 
file extensions get treated as text files. By default, it's defined as:

```xml
  <PropertyGroup>
    <EmbeddedResourceStringExtensions>.txt|.cs|.sql|.json|.md</EmbeddedResourceStringExtensions>
  </PropertyGroup>
```

You can append additional file extensions to this list, or override it completely.
The list must be pipe-separated.

You can always use the provided `GetStream` and `GetBytes` for more advanced scenarios (or for 
non-text resources).

Optionally, you can specify the `Kind` metadata for a specific `EmbeddedResource` you want 
treated as a text file:

```xml
    <EmbeddedResource Include="query.kql" Kind="Text" />
```

You can also add a `Comment` item metadata attribute, which will be used as the `<summary>` XML 
doc for the generated member.

<!-- #content -->
<!-- src/ThisAssembly.Resources/readme.md#content -->

## ThisAssembly.Strings

[![Version](https://img.shields.io/nuget/vpre/ThisAssembly.Strings.svg?color=royalblue)](https://www.nuget.org/packages/ThisAssembly.Strings)
[![Downloads](https://img.shields.io/nuget/dt/ThisAssembly.Strings.svg?color=green)](https://www.nuget.org/packages/ThisAssembly.Strings)

<!-- include src/ThisAssembly.Strings/readme.md#content -->
<!-- #content -->

This package generates a static `ThisAssembly.Strings` class with public 
constants exposing string resources in .resx files or methods with the right number of 
parameters for strings that use formatting parameters. 

![](https://raw.githubusercontent.com/devlooped/ThisAssembly/main/img/ThisAssembly.Strings.gif)

In addition, it groups constants and methods in nested classes according to an optional 
underscore separator to organize strings. For example, *User_InvalidCredentials* can be
accessed with *ThisAssembly.Strings.User.InvalidCredentials* if it contains a simple string, 
or as a method with the right number of parametres if its value has a format string.

Given the following Resx file:

| Name                          | Value                                 | Comment           |
|-------------------------------|---------------------------------------|-------------------|
| Infrastructure_MissingService | Service {0} is required.              | For logging only! |
| Shopping_NoShipping           | We cannot ship {0} to {1}.            |                   |
| Shopping_OutOfStock           | Product is out of stock at this time. |                   |

The following code would be generated:

```csharp
partial class ThisAssembly
{
    public static partial class Strings
    {
        public static partial class Infrastructure
        {
            /// <summary>
            /// For logging only!
            /// => "Service {0} is required."
            /// </summary>
            public static string MissingService(object arg0)
                => string.Format(CultureInfo.CurrentCulture, 
                    Strings.GetResourceManager("ThisStore.Properties.Resources").GetString("MissingService"), 
                    arg0);
        }

        public static partial class Shopping
        {
            /// <summary>
            /// => "We cannot ship {0} to {1}."
            /// </summary>
            public static string NoShipping(object arg0, object arg1)
                => string.Format(CultureInfo.CurrentCulture, 
                    Strings.GetResourceManager("ThisStore.Properties.Resources").GetString("NoShipping"), 
                    arg0, arg1);

            /// <summary>
            /// => "Product is out of stock at this time."
            /// </summary>
            public static string OutOfStock
                => Strings.GetResourceManager("ThisStore.Properties.Resources").GetString("OutOfStock");
        }
    }
}
```

<!-- #content -->
<!-- src/ThisAssembly.Strings/readme.md#content -->

# Dogfooding

[![CI Version](https://img.shields.io/endpoint?url=https://shields.kzu.io/vpre/Stunts/main&label=nuget.ci&color=brightgreen)](https://pkg.kzu.io/index.json)
[![Build](https://github.com/devlooped/ThisAssembly/workflows/build/badge.svg?branch=main)](https://github.com/devlooped/ThisAssembly/actions)

We also produce CI packages from branches and pull requests so you can dogfood builds as quickly as they are produced. 

The CI feed is `https://pkg.kzu.io/index.json`. 

The versioning scheme for packages is:

- PR builds: *42.42.42-pr*`[NUMBER]`
- Branch builds: *42.42.42-*`[BRANCH]`.`[COMMITS]`


<!-- include https://github.com/devlooped/sponsors/raw/main/footer.md -->
# Sponsors 

<!-- sponsors.md -->
[![Clarius Org](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/clarius.png "Clarius Org")](https://github.com/clarius)
[![Kirill Osenkov](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/KirillOsenkov.png "Kirill Osenkov")](https://github.com/KirillOsenkov)
[![MFB Technologies, Inc.](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/MFB-Technologies-Inc.png "MFB Technologies, Inc.")](https://github.com/MFB-Technologies-Inc)
[![Stephen Shaw](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/decriptor.png "Stephen Shaw")](https://github.com/decriptor)
[![Torutek](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/torutek-gh.png "Torutek")](https://github.com/torutek-gh)
[![DRIVE.NET, Inc.](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/drivenet.png "DRIVE.NET, Inc.")](https://github.com/drivenet)
[![David Kean](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/davkean.png "David Kean")](https://github.com/davkean)
[![](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/chiluap.png "")](https://github.com/chiluap)
[![Daniel Gnägi](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/dgnaegi.png "Daniel Gnägi")](https://github.com/dgnaegi)
[![Ashley Medway](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/AshleyMedway.png "Ashley Medway")](https://github.com/AshleyMedway)
[![Keith Pickford](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/Keflon.png "Keith Pickford")](https://github.com/Keflon)
[![bitbonk](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/bitbonk.png "bitbonk")](https://github.com/bitbonk)
[![Thomas Bolon](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/tbolon.png "Thomas Bolon")](https://github.com/tbolon)
[![Yurii Rashkovskii](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/yrashk.png "Yurii Rashkovskii")](https://github.com/yrashk)
[![Kori Francis](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/kfrancis.png "Kori Francis")](https://github.com/kfrancis)
[![Zdenek Havlin](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/wdolek.png "Zdenek Havlin")](https://github.com/wdolek)
[![Sean Killeen](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/SeanKilleen.png "Sean Killeen")](https://github.com/SeanKilleen)
[![Toni Wenzel](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/twenzel.png "Toni Wenzel")](https://github.com/twenzel)
[![Giorgi Dalakishvili](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/Giorgi.png "Giorgi Dalakishvili")](https://github.com/Giorgi)
[![Kelly White](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/mckhendry.png "Kelly White")](https://github.com/mckhendry)
[![Allan Ritchie](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/aritchie.png "Allan Ritchie")](https://github.com/aritchie)
[![Mike James](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/MikeCodesDotNET.png "Mike James")](https://github.com/MikeCodesDotNET)
[![Uno Platform](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/unoplatform.png "Uno Platform")](https://github.com/unoplatform)
[![Dan Siegel](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/dansiegel.png "Dan Siegel")](https://github.com/dansiegel)
[![Reuben Swartz](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/rbnswartz.png "Reuben Swartz")](https://github.com/rbnswartz)
[![Jeremy Simmons](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/jeremysimmons.png "Jeremy Simmons")](https://github.com/jeremysimmons)
[![Jacob Foshee](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/jfoshee.png "Jacob Foshee")](https://github.com/jfoshee)
[![](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/Mrxx99.png "")](https://github.com/Mrxx99)
[![Eric Johnson](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/eajhnsn1.png "Eric Johnson")](https://github.com/eajhnsn1)
[![Norman Mackay](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/mackayn.png "Norman Mackay")](https://github.com/mackayn)
[![Certify The Web](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/certifytheweb.png "Certify The Web")](https://github.com/certifytheweb)
[![Taylor Mansfield](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/lavahot.png "Taylor Mansfield")](https://github.com/lavahot)
[![Mårten Rånge](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/mrange.png "Mårten Rånge")](https://github.com/mrange)
[![David Petric](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/davidpetric.png "David Petric")](https://github.com/davidpetric)
[![Rich Lee](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/richlee.png "Rich Lee")](https://github.com/richlee)
[![Danilo das Neves Dantas](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/dannevesdantas.png "Danilo das Neves Dantas")](https://github.com/dannevesdantas)
[![](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/nietras.png "")](https://github.com/nietras)
[![Gary Woodfine](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/garywoodfine.png "Gary Woodfine")](https://github.com/garywoodfine)
[![](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/kristinnstefansson.png "")](https://github.com/kristinnstefansson)
[![](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/DarrenAtConexus.png "")](https://github.com/DarrenAtConexus)
[![Steve Bilogan](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/kazo0.png "Steve Bilogan")](https://github.com/kazo0)
[![Ix Technologies B.V.](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/IxTechnologies.png "Ix Technologies B.V.")](https://github.com/IxTechnologies)
[![New Relic](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/newrelic.png "New Relic")](https://github.com/newrelic)
[![Chris Johnston‮](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/Chris-Johnston.png "Chris Johnston‮")](https://github.com/Chris-Johnston)
[![David JENNI](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/davidjenni.png "David JENNI")](https://github.com/davidjenni)
[![](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/ehonda.png "")](https://github.com/ehonda)
[![Jonathan ](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/Jonathan-Hickey.png "Jonathan ")](https://github.com/Jonathan-Hickey)
[![Oleg Kyrylchuk](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/okyrylchuk.png "Oleg Kyrylchuk")](https://github.com/okyrylchuk)
[![Juan Blanco](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/juanfranblanco.png "Juan Blanco")](https://github.com/juanfranblanco)
[![LosManos](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/LosManos.png "LosManos")](https://github.com/LosManos)
[![Mariusz Kogut](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/MariuszKogut.png "Mariusz Kogut")](https://github.com/MariuszKogut)
[![Charley Wu](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/akunzai.png "Charley Wu")](https://github.com/akunzai)
[![](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/meisenring.png "")](https://github.com/meisenring)
[![Thomas Due](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/Tdue21.png "Thomas Due")](https://github.com/Tdue21)
[![Jakob Tikjøb Andersen](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/jakobt.png "Jakob Tikjøb Andersen")](https://github.com/jakobt)
[![Seann Alexander](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/seanalexander.png "Seann Alexander")](https://github.com/seanalexander)
[![Tino Hager](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/tinohager.png "Tino Hager")](https://github.com/tinohager)
[![Badre BSAILA](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/pedrobsaila.png "Badre BSAILA")](https://github.com/pedrobsaila)
[![Mark Seemann](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/ploeh.png "Mark Seemann")](https://github.com/ploeh)
[![Angelo Belchior](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/angelobelchior.png "Angelo Belchior")](https://github.com/angelobelchior)
[![Tony Qu](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/tonyqus.png "Tony Qu")](https://github.com/tonyqus)
[![Daniel May](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/danielrmay.png "Daniel May")](https://github.com/danielrmay)
[![Blauhaus Technology (Pty) Ltd](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/BlauhausTechnology.png "Blauhaus Technology (Pty) Ltd")](https://github.com/BlauhausTechnology)
[![Richard Collette](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/rcollette.png "Richard Collette")](https://github.com/rcollette)
[![Nick Vaughan](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/bngv.png "Nick Vaughan")](https://github.com/bngv)
[![Ken Bonny](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/KenBonny.png "Ken Bonny")](https://github.com/KenBonny)
[![Simon Cropp](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/SimonCropp.png "Simon Cropp")](https://github.com/SimonCropp)


<!-- sponsors.md -->

[![Sponsor this project](https://raw.githubusercontent.com/devlooped/sponsors/main/sponsor.png "Sponsor this project")](https://github.com/sponsors/devlooped)
&nbsp;

[Learn more about GitHub Sponsors](https://github.com/sponsors)

<!-- https://github.com/devlooped/sponsors/raw/main/footer.md -->
