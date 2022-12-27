# Coverlet

[![Build Status](https://dev.azure.com/tonerdo/coverlet/_apis/build/status/coverlet-coverage.coverlet?branchName=master)](https://dev.azure.com/tonerdo/coverlet/_build/latest?definitionId=5&branchName=master) [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://github.com/coverlet-coverage/coverlet/blob/master/LICENSE)   

| Driver  |  Current version  | Downloads  |
|---|---|---|
|  coverlet.collector  | [![NuGet](https://img.shields.io/nuget/v/coverlet.collector.svg)](https://www.nuget.org/packages/coverlet.collector/)    |  [![NuGet](https://img.shields.io/nuget/dt/coverlet.collector.svg)](https://www.nuget.org/packages/coverlet.collector/) 
|  coverlet.msbuild |  [![NuGet](https://img.shields.io/nuget/v/coverlet.msbuild.svg)](https://www.nuget.org/packages/coverlet.msbuild/)   |  [![NuGet](https://img.shields.io/nuget/dt/coverlet.msbuild.svg)](https://www.nuget.org/packages/coverlet.msbuild/) |
|  coverlet.console |  [![NuGet](https://img.shields.io/nuget/v/coverlet.console.svg)](https://www.nuget.org/packages/coverlet.console/)     |  [![NuGet](https://img.shields.io/nuget/dt/coverlet.console.svg)](https://www.nuget.org/packages/coverlet.console/) |

Coverlet is a cross platform code coverage framework for .NET, with support for line, branch and method coverage. It works with .NET Framework on Windows and .NET Core on all supported platforms.

**Coverlet documentation reflect the current repository state of the features, not the released ones.**  
**Check the [changelog](Documentation/Changelog.md) to understand if the documented feature you want to use has been officially released.**

# Main contents
* [QuickStart](#Quick-Start)
* [How It Works](#How-It-Works)
* [Drivers features differences](Documentation/DriversFeatures.md)
* [Deterministic build support](#Deterministic-build-support)
* [Known Issues](#Known-Issues)
* [Consume nightly build](#Consume-nightly-build)
* [Feature samples](Documentation/Examples.md)
* [Cake Add-In](#Cake-Add-In)
* [Visual Studio Add-In](#Visual-Studio-Add-In)
* [Changelog](Documentation/Changelog.md)
* [Roadmap](Documentation/Roadmap.md)

## Quick Start

Coverlet can be used through three different *drivers* 

* VSTest engine integration
* MSBuild task integration
* As a .NET Global tool (supports standalone integration tests)

Coverlet supports only SDK-style projects https://docs.microsoft.com/en-us/visualstudio/msbuild/how-to-use-project-sdk?view=vs-2019  


### VSTest Integration (preferred due to [known issue](https://github.com/coverlet-coverage/coverlet/blob/master/Documentation/KnownIssues.md#1-vstest-stops-process-execution-earlydotnet-test))

### Installation
```bash
dotnet add package coverlet.collector
```
N.B. You **MUST** add package only to test projects and if you create xunit test projects (`dotnet new xunit`) you'll find the reference already present in `csproj` file because Coverlet is the default coverage tool for every .NET Core and >= .NET 5 applications, you've only to update to last version if needed.

### Usage
Coverlet is integrated into the Visual Studio Test Platform as a [data collector](https://github.com/Microsoft/vstest-docs/blob/master/docs/extensions/datacollector.md). To get coverage simply run the following command:

```bash
dotnet test --collect:"XPlat Code Coverage"
```

After the above command is run, a `coverage.cobertura.xml` file containing the results will be published to the `TestResults` directory as an attachment.

See [documentation](Documentation/VSTestIntegration.md) for advanced usage.

#### Requirements
* _You need to be running .NET Core SDK v2.2.401 or newer_
* _You need to reference version 16.5.0 and above of Microsoft.NET.Test.Sdk_
```
<PackageReference Include="Microsoft.NET.Test.Sdk" Version="16.5.0" />
```

### MSBuild Integration (suffers of possible [known issue](https://github.com/coverlet-coverage/coverlet/blob/master/Documentation/KnownIssues.md#1-vstest-stops-process-execution-earlydotnet-test))

### Installation
```bash
dotnet add package coverlet.msbuild
```
N.B. You **MUST** add package only to test projects  

### Usage

Coverlet also integrates with the build system to run code coverage after tests. Enabling code coverage is as simple as setting the `CollectCoverage` property to `true`

```bash
dotnet test /p:CollectCoverage=true
```

After the above command is run, a `coverage.json` file containing the results will be generated in the root directory of the test project. A summary of the results will also be displayed in the terminal.

See [documentation](Documentation/MSBuildIntegration.md) for advanced usage.

#### Requirements
Requires a runtime that support _.NET Standard 2.0 and above_

### .NET Global Tool ([guide](https://docs.microsoft.com/en-us/dotnet/core/tools/global-tools), suffers from possible [known issue](https://github.com/coverlet-coverage/coverlet/blob/master/Documentation/KnownIssues.md#1-vstest-stops-process-execution-earlydotnet-test))

### Installation

```bash
dotnet tool install --global coverlet.console
```

### Usage

Visual Studio on Windows provides a way to perform code coverage.

The unit test project requires the coverlet.msbuild NuGet package.

Code coverage results are written to an XML file so that they can be processed by another tool. Azure Pipelines supports Cobertura and JaCoCo coverage result formats.

To convert Cobertura coverage results to a format that's human-readable, they can use a tool called ReportGenerator.

ReportGenerator provides many formats, including HTML. The HTML formats create detailed reports for each class in a .NET project.

Specifically, there's an HTML format called HtmlInline_AzurePipelines, which provides a visual appearance that matches Azure Pipelines.

```dotnet new tool-manifest
dotnet tool install dotnet-reportgenerator-globaltool
dotnet add Tailspin.SpaceGame.Web.Tests package coverlet.msbuild

dotnet test --no-build \
  --configuration Release \
  /p:CollectCoverage=true \
  /p:CoverletOutputFormat=cobertura \
  /p:CoverletOutput=./TestResults/Coverage/
  
dotnet tool run reportgenerator \
  -reports:./Tailspin.SpaceGame.Web.Tests/TestResults/Coverage/coverage.cobertura.xml \
  -targetdir:./CodeCoverage \
  -reporttypes:HtmlInline_AzurePipelines```
